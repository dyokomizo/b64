Base Sixty-Forth is a minimalistic esoteric language designed for the [Into the Turing Tarpit](http://www.pltgames.com/competition/2012/12) PLT Game.

Our main goal here is to create an useless language that, being a Forth, become an useful one after a few steps of self-improvement. Of course it won't be particularly convenient to use, after all it's an esoteric language.

Let's start with the syntax. We're going down the Forth tradition of a minimalistic syntax, so we have words separated by spaces. We're using ASCII only, no Unicode shenanigans, because 128 charset should be enough for anyone.

    space   = <any ASCII char from 00-20 or 7F>
    word    = literal | program
    literal = <base 64 encoded string, but using "'` instead of +/= for encoding>
    program = {code}
    code    = <any ASCII char from 21-7E but 22,39,60>

Our semantics is also inspired by Forth, so we have a stack and RPN. Program evaluation is LTR, literal words push its address (the compiler places the literal in the binary's data section) into the stack and program words are executed (each program word points to an area in the binary's code section, so we just JMP there).

Now we start to diverge from Forth's letter (albeit we stick to it's spirit). We have a few primitive program words that we use to do everything:

- = is for definitions. It expects two literal addresses in the stack, uses the top one as the new definition name (i.e. it must be a valid program) and the one underneath as the base64 encoded program body. It'll compile the body, put it in the code section and it's address will be accessible by the definition name.
- # is for inline assembly. It expects one literal address in the stack and it executes it as assembly code.

Actually, = is unnecessary, we just need to have a primitive # and then define = in assembly.

Everything else is either defined from inline assembly or from word juggling.
