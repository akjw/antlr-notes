# Interpreter
- an application that executes sentences 
- e.g. calculators, Python interpreters


# Translator
- an application that converts sentences into other languages 
- e.g. Java to C# converters & compilers

These applications need to read sentences & react appropriately to input symbols they discover. Hence the need for a specific program:

# Parser / Syntax Analyzer
## a) Lexer: 
- Lexcial analysis / tokenizing (grouping characters into words / symbols); e.g. token types like INT, ID, FLOTA
## b) Parser: 
- feeds off tokens to recognize sentence structure 
-ANTLR generates parsers which build parse trees (these record structure of input sentence and & its phrases)

## Parse trees
- record complete info about how parser grouped symbols into phrases
- allow multiple apps that need to recognize the same language to reuse a single parser 
- tidier than embedding app-specific code snippets into the grammar, which is the traditional move of parser generators
- useful for translations that require multiple tree walks (compared to reparsing input characters for each stage) due to computation dependencies 


# Implementing Parsers
- ANTLR generates recursive-descent parsers (aka top-down parsing)
- parsing begins at root of tree & moves towards leaves (tokens)
- start symbol (first-invoked rule) = root of parse tree

## Method that ANTLR generates for rule `assign`:
```
// assign : ID '=' expr ';' ;​
​ 	
​void​ assign() {     ​// method generated from rule assign​
​ 	
    match(ID);      ​// compare ID to current input symbol then consume​
​ 	
    match(​'='​);
​ 	
    expr();         ​// match an expression by calling expr()​
​ 	
    match(​';'​);
​ 	
}
```
- method checks that all necessary tokens are present & in right order
- parser does not have to choose between more than 1 alternative:
```
/** Match any kind of statement starting at the current input position */​
​ 	
stat: assign          ​// First alternative ('|' is alternative separator)​
​ 	
    | ifstat          ​// Second alternative​
​ 	
    | whilestat
​ 	
   ...
​ 	
    ;
```
- parsing rule for stat (switch statement):

```
​void​ stat() {
​ 	
    ​switch​ ( current input token ) {
​ 	
        CASE ID    : assign(); ​break​;
​ 	
        CASE IF    : ifstat(); ​break​; ​// IF is token type for keyword 'if'​
​ 	
        CASE WHILE : whilestat(); ​break​;
​ 	
        ...
​ 	
        default    : raise no viable alternative exception
​ 	
    }
​ 	
}
```
- method makes parsing decision by examining next input token (aka lookahead token)
- sometimes many lookahead tokens are needed so that the parser can predict which alternative will succeed

- unambiguous grammars must be provided so that parser can match each input phrase in exactly one way

# Building Language Applications w/ Parse Trees
- lexers process characters & pass tokens to parser
- parser checks syntax & creates parse tree
- relevant ANTLR classes: CharStream, Lexer, Token, Parser, ParseTree
- TokenStream connects lexer & parser
- ANTLR data structures share as much data possible to reduce memory requirements
- token nodes in parse tree are containers pointing at tokens in token stream
- tokens store start & stop char indexes into CharStream
- context objects know start & stop tokens for the recognized phrase & give full access to all elements of the phrase

# Listeners & Visitors

## Listeners
- ANTLR generates parse-tree listener interface to respond to events triggered by in-built tree walker 
- Language apps require building a ParseTreeListener implementation containing app-specific code 
- Listener mechanism is automatic; listener methods do not need to explicitly visit children

## Visitors
- -visitor option causes ANTLR to generate visitor interface 

# C5 : ANTLR grammars
x ~~~ Match token, rule reference, or subrule x.

x y ... z ~~~ Match a sequence of rule elements.

(... | ... | ...) ~~~ Subrule with multiple alternatives.

x? ~~~ Match x or skip it. (optional; one or none)

x* ~~~ Match x zero or more times.

x+ ~~~ Match x one or more times.

r : ... ; ~~~ Define rule r.

r : ... | ... | ... ; ~~~ Define rule r with multiple alternatives.