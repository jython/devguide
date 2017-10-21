.. Jython companion to compiler.rst

.. _compiler-jy:

Design of Jython's Compiler
===========================

.. warning:: At present, this is not much more than a copy of the CPython original
   with the obviously inapplicable crudely hacked out.

Abstract
--------

In CPython, the compilation from source code to bytecode involves several steps:

1. Parse source code into a parse tree (:file:`Parser/pgen.c`)
2. Transform parse tree into an Abstract Syntax Tree (:file:`Python/ast.c`)
3. Transform AST into a Control Flow Graph (:file:`Python/compile.c`)
4. Emit bytecode based on the Control Flow Graph (:file:`Python/compile.c`)

The purpose of this document is to outline how these steps of the process work.

This document does not touch on how parsing works beyond what is needed
to explain what is needed for compilation.  It is also not exhaustive
in terms of the how the entire system works.  You will most likely need
to read some source to have an exact understanding of all details.


Parse Trees
-----------

Python's grammar parser is designed for an LL(1) parser mostly based off of the
implementation laid out in the Dragon Book [Aho86]_.

Jython uses the ANTLR parser-generator,
which is capable of much more than LL(1),
but we like to keep the same source grammar.

At the time of writing, we are still using an obsolete version of ANTLR.
ANTLR 4 changed substantially, and significant work is needed to follow suit.

The grammar file for Jython can be found in :file:`grammar/Python.g`.



Abstract Syntax Trees (AST)
---------------------------


.. sidebar:: Green Tree Snakes

   See also `Green Tree Snakes - the missing Python AST docs
   <https://greentreesnakes.readthedocs.io/en/latest/>`_ by Thomas Kluyver.

The abstract syntax tree (AST) is a high-level representation of the
program structure without the necessity of containing the source code;
it can be thought of as an abstract representation of the source code.  The
specification of the AST nodes is specified using the Zephyr Abstract
Syntax Definition Language (ASDL) [Wang97]_.

The definition of the AST nodes for Python is found in the file
:file:`ast/Python.asdl`.

Each AST node (representing statements, expressions, and several
specialized types, like list comprehensions and exception handlers) is
defined by the ASDL.  Most definitions in the AST correspond to a
particular source construct, such as an 'if' statement or an attribute
lookup.  The definition is independent of its realization in any
particular programming language.

The following fragment of the Python ASDL construct demonstrates the
approach and syntax::

  module Python
  {
        stmt = FunctionDef(identifier name, arguments args, stmt* body,
                            expr* decorators)
              | Return(expr? value) | Yield(expr value)
              attributes (int lineno)
  }

The preceding example describes three different kinds of statements;
function definitions, return statements, and yield statements.  All
three kinds are considered of type ``stmt`` as shown by ``|`` separating the
various kinds.  They all take arguments of various kinds and amounts.

Modifiers on the argument type specify the number of values needed; ``?``
means it is optional, ``*`` means 0 or more, while no modifier means only one
value for the argument and it is required.  ``FunctionDef``, for instance,
takes an ``identifier`` for the *name*, ``arguments`` for *args*, zero or more
``stmt`` arguments for *body*, and zero or more ``expr`` arguments for
*decorators*.

Do notice that something like 'arguments', which is a node type, is
represented as a single AST node and not as a sequence of nodes as with
stmt as one might expect.

All three kinds also have an 'attributes' argument; this is shown by the
fact that 'attributes' lacks a '|' before it.


.. note:: something needed here about AST classes supporting the parser.


Memory Management
-----------------




Parse Tree to AST
-----------------

.. note:: not sure how much of this is true for Jython

The AST is generated from the parse tree ...

The function begins a tree walk of the parse tree, creating various AST
nodes as it goes along.  It does this by allocating all new nodes it
needs, calling the proper AST node creation functions for any required
supporting functions, and connecting them as needed.

Do realize that there is no automated nor symbolic connection between
the grammar specification and the nodes in the parse tree.  No help is
directly provided by the parse tree as in yacc.

For instance, one must keep track of which node in the parse tree
one is working with (e.g., if you are working with an 'if' statement
you need to watch out for the ':' token to find the end of the conditional).

The functions called to generate AST nodes from the parse tree all have
the name ``ast_for_xx`` where *xx* is the grammar rule that the function
handles (``alias_for_import_name`` is the exception to this).  These in turn
call the constructor functions as defined by the ASDL grammar and
contained in :file:`Python/Python-ast.c` (which was generated by
:file:`Parser/asdl_c.py`) to create the nodes of the AST.  This all leads to a
sequence of AST nodes stored in ``asdl_seq`` structs.


Function and macros for creating and using ``asdl_seq *`` types as found
in :file:`Python/asdl.c` and :file:`Include/asdl.h` are as follows:

``_Py_asdl_seq_new(Py_ssize_t, PyArena *)``
        Allocate memory for an ``asdl_seq`` for the specified length
``asdl_seq_GET(asdl_seq *, int)``
        Get item held at a specific position in an ``asdl_seq``
``asdl_seq_SET(asdl_seq *, int, stmt_ty)``
        Set a specific index in an ``asdl_seq`` to the specified value
``asdl_seq_LEN(asdl_seq *)``
        Return the length of an ``asdl_seq``

If you are working with statements, you must also worry about keeping
track of what line number generated the statement.  Currently the line
number is passed as the last parameter to each ``stmt_ty`` function.


Control Flow Graphs
-------------------

A control flow graph (often referenced by its acronym, CFG) is a
directed graph that models the flow of a program using basic blocks that
contain the intermediate representation (abbreviated "IR", and in this
case is Python bytecode) within the blocks.  Basic blocks themselves are
a block of IR that has a single entry point but possibly multiple exit
points.  The single entry point is the key to basic blocks; it all has
to do with jumps.  An entry point is the target of something that
changes control flow (such as a function call or a jump) while exit
points are instructions that would change the flow of the program (such
as jumps and 'return' statements).  What this means is that a basic
block is a chunk of code that starts at the entry point and runs to an
exit point or the end of the block.

As an example, consider an 'if' statement with an 'else' block.  The
guard on the 'if' is a basic block which is pointed to by the basic
block containing the code leading to the 'if' statement.  The 'if'
statement block contains jumps (which are exit points) to the true body
of the 'if' and the 'else' body (which may be ``NULL``), each of which are
their own basic blocks.  Both of those blocks in turn point to the
basic block representing the code following the entire 'if' statement.

CFGs are usually one step away from final code output.  Code is directly
generated from the basic blocks (with jump targets adjusted based on the
output order) by doing a post-order depth-first search on the CFG
following the edges.


AST to CFG to Bytecode
----------------------

.. note:: names wrong here but are we close in organisation?

With the AST created, the next step is to create the CFG. The first step
is to convert the AST to Python bytecode without having jump targets
resolved to specific offsets (this is calculated when the CFG goes to
final bytecode). Essentially, this transforms the AST into Python
bytecode with control flow represented by the edges of the CFG.

Conversion is done in two passes.  The first creates the namespace
(variables can be classified as local, free/cell for closures, or
global).  With that done, the second pass essentially flattens the CFG
into a list and calculates jump offsets for final output of bytecode.

The conversion process is initiated by a call to the function
``PyAST_Compile()`` in :file:`Python/compile.c`.  This function does both the
conversion of the AST to a CFG and outputting final bytecode from the CFG.
The AST to CFG step is handled mostly by two functions called by
``PyAST_Compile()``; ``PySymtable_Build()`` and ``compiler_mod()``.  The former
is in :file:`Python/symtable.c` while the latter is in :file:`Python/compile.c`.

``PySymtable_Build()`` begins by entering the starting code block for the
AST (passed-in) and then calling the proper ``symtable_visit_xx`` function
(with *xx* being the AST node type).  Next, the AST tree is walked with
the various code blocks that delineate the reach of a local variable
as blocks are entered and exited using ``symtable_enter_block()`` and
``symtable_exit_block()``, respectively.

Once the symbol table is created, it is time for CFG creation, whose
code is in :file:`Python/compile.c`.  This is handled by several functions
that break the task down by various AST node types.  The functions are
all named ``compiler_visit_xx`` where *xx* is the name of the node type (such
as stmt, expr, etc.).  Each function receives a ``struct compiler *``
and ``xx_ty`` where *xx* is the AST node type.  Typically these functions
consist of a large 'switch' statement, branching based on the kind of
node type passed to it.  Simple things are handled inline in the
'switch' statement with more complex transformations farmed out to other
functions named ``compiler_xx`` with *xx* being a descriptive name of what is
being handled.

When transforming an arbitrary AST node, use the ``VISIT()`` macro.
The appropriate ``compiler_visit_xx`` function is called, based on the value
passed in for <node type> (so ``VISIT(c, expr, node)`` calls
``compiler_visit_expr(c, node)``).  The ``VISIT_SEQ`` macro is very similar,
but is called on AST node sequences (those values that were created as
arguments to a node that used the '*' modifier).  There is also
``VISIT_SLICE()`` just for handling slices.

Emission of bytecode is handled by the asm library.

.. note:: names again?

Several helper functions that will emit bytecode and are named
``compiler_xx()`` where *xx* is what the function helps with (list, boolop,
etc.).  A rather useful one is ``compiler_nameop()``.
This function looks up the scope of a variable and, based on the
expression context, emits the proper opcode to load, store, or delete
the variable.

As for handling the line number on which a statement is defined, this is
handled by ``compiler_visit_stmt()`` and thus is not a worry.

In addition to emitting bytecode based on the AST node, handling the
creation of basic blocks must be done.  Below are the macros and
functions used for managing basic blocks:

``NEXT_BLOCK(struct compiler *)``
    create an an implicit jump from the current block
    to the new block
``compiler_new_block(struct compiler *)``
    create a block but don't use it (used for generating jumps)

Once the CFG is created, it must be flattened and then final emission of
bytecode occurs.  Flattening is handled using a post-order depth-first
search.  Once flattened, jump offsets are backpatched based on the
flattening and then a ``PyCodeObject`` is created.  All of this is
handled by calling ``assemble()``.


Introducing JVM Bytecode
------------------------



Code Objects
------------

The result of ``PyAST_Compile()`` is a ``PyCodeObject`` which is defined in
:file:`Include/code.h`.  And with that you now have executable Python bytecode!

The code objects (byte code) are executed in :file:`Python/ceval.c`.  This file
will also need a new case statement for the new opcode in the big switch
statement in ``PyEval_EvalFrameDefault()``.


Important Files
---------------

+ ast/

    Python.asdl
        ASDL syntax file

    asdl.py
        Parser for ASDL definition files. Reads in an ASDL description
        and parses it into an AST that describes it.

    asdl_java.py
        "Generate Java code from an ASDL description."  Generates
        :file:`...` .



Known Compiler-related Experiments
----------------------------------

This section lists known experiments involving the compiler (including
bytecode).




References
----------

.. .. [Aho86] Alfred V. Aho, Ravi Sethi, Jeffrey D. Ullman.
   `Compilers: Principles, Techniques, and Tools`,
   https://www.amazon.com/exec/obidos/tg/detail/-/0201100886/104-0162389-6419108

.. .. [Wang97]  Daniel C. Wang, Andrew W. Appel, Jeff L. Korn, and Chris
   S. Serra.  `The Zephyr Abstract Syntax Description Language.`_
   In Proceedings of the Conference on Domain-Specific Languages, pp.
   213--227, 1997.

.. _The Zephyr Abstract Syntax Description Language.:
   http://www.cs.princeton.edu/research/techreps/TR-554-97

