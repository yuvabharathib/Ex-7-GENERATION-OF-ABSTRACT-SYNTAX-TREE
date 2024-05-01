# Ex-7-GENERATION-OF-ABSTRACT-SYNTAX-TREE
CONVERSION OF THE BNF RULES INTO YACC FORM AND GENERATION OF ABSTRACT SYNTAX TREE
# Date:20.03.2024
# Aim: 
To write a program to convert the BNF rules into YACC form and to generate Abstract Syntax Tree.
# ALGORITHM
1.	Start the program.
2.	Write a program in the vi editor and save it with .l extension.
3.	In the lex program, write the translation rules for the keywords, identifiers and relational operators.
4.	Write a program in the vi editor and save it with .y extension.
5.	In the YACC program, the following tasks are performed:
  a.	Reading an input file line by line.
  b.	Convert it into abstract syntax tree using three address code.
  c.	Represent three address code in the form of quadruple tabular form.
6.	Compile the lex program with lex compiler to produce output file as lex.yy.c. eg $ lex filename.l
7.	Compile the yacc program with yacc compiler to produce output file as y.tab.c. eg $ yacc –d arith_id.y
8.	Compile these with the C compiler as gcc lex.yy.c y.tab.c
9.	A C program is given as input and the abstract syntax tree is generated as output.
# PROGRAM
Program:int.| file
```
%{
#include "y.tab.h"
#include <stdio.h>
#include <string.h>
int LineNo=1;
%}

identifier [a-zA-Z][_a-zA-Z0-9]*
number [0-9]+|([0-9]*\.[0-9]+)

%%

main\(\) { return MAIN; }
if { return IF; }
else { return ELSE; }
while { return WHILE; }
int|char|float { return TYPE; }

{identifier} { strcpy(yylval.var,yytext); return VAR; }
{number} { strcpy(yylval.var,yytext); return NUM; }

\< { return '<'; }
\> { return '>'; }
\>= { return ">="; }
\<= { return "<="; }
== { return "=="; }

[ \t] ;
\n { LineNo++; }

. { return yytext[0]; }

%%
```
Program: int.y file
```
%{
#include <string.h>
#include <stdio.h>

struct quad {
    char op[5];
    char arg1[10];
    char arg2[10];
    char result[10];
} QUAD[30];

struct stack {
    int items[100];
    int top;
} stk;

int Index=0, tIndex=0, StNo, Ind, tInd;
extern int LineNo;

void push(int data);
int pop();
void AddQuadruple(char op[5],char arg1[10],char arg2[10],char result[10]);
%}

%union {
    char var[10];
}

%token <var> NUM VAR RELOP
%token MAIN IF ELSE WHILE TYPE
%type <var> EXPR ASSIGNMENT CONDITION IFST ELSEST WHILELOOP

%left '-' '+'
%left '*' '/'

%%

PROGRAM : MAIN BLOCK ;

BLOCK: '{' CODE '}' ;

CODE: BLOCK
     | STATEMENT CODE
     | STATEMENT ;

STATEMENT: DESCT ';'
          | ASSIGNMENT ';'
          | CONDST
          | WHILEST ;

DESCT: TYPE VARLIST ;

VARLIST: VAR ',' VARLIST
       | VAR ;

ASSIGNMENT: VAR '=' EXPR {
    strcpy(QUAD[Index].op,"=");
    strcpy(QUAD[Index].arg1,$1);
    strcpy(QUAD[Index].arg2,"");
    strcpy(QUAD[Index].result,$3);
    Index++;
} ;

EXPR: EXPR '+' EXPR
    | EXPR '-' EXPR
    | EXPR '*' EXPR
    | EXPR '/' EXPR
    | '-' EXPR
    | '(' EXPR ')'
    | VAR
    | NUM ;

CONDST: IFST
      | IFST ELSEST ;

IFST: IF '(' CONDITION ')' BLOCK {
    strcpy(QUAD[Index].op,"==");
    strcpy(QUAD[Index].arg1,$3);
    strcpy(QUAD[Index].arg2,"FALSE");
    strcpy(QUAD[Index].result,"-1");
    push(Index);
    Index++;
} ;

ELSEST: ELSE BLOCK ;

CONDITION: VAR RELOP VAR
         | VAR
         | NUM ;

WHILEST: WHILELOOP ;

WHILELOOP: WHILE '(' CONDITION ')' BLOCK ;

%%

extern FILE *yyin;

int main(int argc,char *argv[]) {
    FILE *fp;
    int i;
    if(argc > 1) {
        fp = fopen(argv[1],"r");
        if(!fp) {
            printf("\n File not found");
            exit(0);
        }
        yyin = fp;
    }
    yyparse();
    printf("\n\n\t\t\n\t\t Pos Operator \tArg1 \tArg2 \tResult \n\t\t ");
    for(i = 0; i < Index; i++) {
        printf("\n\t\t %d\t %s\t %s\t %s\t%s",i,QUAD[i].op,QUAD[i].arg1,QUAD[i].arg2,QUAD[i].result);
    }
    printf("\n\t\t ");
    printf("\n\n");
    return 0;
}

void push(int data) {
    stk.top++;
    if(stk.top == 100) {
        printf("\n Stack overflow\n");
        exit(0);
    }
    stk.items[stk.top] = data;
}

int pop() {
    int data;
    if(stk.top == -1) {
        printf("\n Stack underflow\n");
        exit(0);
    }
    data = stk.items[stk.top--];
    return data;
}

void AddQuadruple(char op[5],char arg1[10],char arg2[10],char result[10]) {
    strcpy(QUAD[Index].op,op);
    strcpy(QUAD[Index].arg1,arg1);
    strcpy(QUAD[Index].arg2,arg2);
    strcpy(QUAD[Index].result,result);
    Index++;
}

void yyerror() {
    printf("\n Error on line no:%d",LineNo);
}
```
Program: test.c file
```
main() {
    int a, b, c;
    if (a < b) {
        a = a + b;
    }
    while (a < b) { 
        a = a + b;
    }
    if (a <= b) { 
        c = a - b;
    } else {
        c = a + b;
    }
}
```
# Output
![image](https://github.com/POKALAGURAVAIAH8121/Ex-7-GENERATION-OF-ABSTRACT-SYNTAX-TREE/assets/128034765/c5ad0bbd-d199-4cc7-939e-858a661f7c12)

# Result
Conversion of the BNF rules into YACC form and to generate Abstract Syntax Tree is implemented.

