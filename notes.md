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
