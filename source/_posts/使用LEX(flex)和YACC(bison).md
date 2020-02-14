---
title: 使用LEX(flex)和YACC(bison)
date: 2018-11-14 11:49:00
tags:
---

# *LEX*与*YACC*介绍

Lex(Lexxical Analyzar)和Yacc(Yet Another Compiler)分别是词法解析器和语法解析器，用目标语言的正则表达式编写Lex代码，经过flex编译可以生成词法分析的C代码，用目标语言的BNF编写Yacc代码，经过bison编译可以生成语法分析的C代码，将词法分析和语法分析的C代码一起编译即可生成目标语言的编译器。 Lex和Yacc原本是Unix/Linux系统下的工具，UnxUtils将他们移植到了 Windows平台。具体安装配置步骤可以参考：[lex和yacc安装配置](https://blog.csdn.net/XiaoPANGXia/article/details/44132693)）。

# 环境和工具

- Windows
- lex和yacc
- gcc编译器
- PowerShell/cmd或IDE+ParserGenerator (推荐直接使用命令行编译，我没用IDE配置过)

# Lex使用

一个基本的lex.l程序 

```
%{ #include /第一部分/ %} 
/第二部分/ %% 
/第三部分/ %% 
/第四部分/ 
void main() { 
    printf(“The first Lex program.\n”);
} 
int yywrap(void) {
    return 1; 
}
```



保存为lex.l文件 在保存文件目录下打开命令行 

`flex lex.l`

会编译生成lex.yy.c文件 编译lex.yy.c 

`lex.yy.c`

会编译生成a.exe可执行文件 运行a.exe文件即可看到输出。

# Lex语法规则

lex代码被我分为四个部分

- 第一部分 - 定义

包含在`%{/*第一部分*/%}`，这个部分用来发需要用到的头文件、全局变量、函数说明等，这一部分内容会全部一模一样的拷贝到lex.yy.x文件中。

- 第二部分 - 正规定义

这部分可以为一些词法规则进行简化定义，方便使用。 如：`[A-Za-z]`  之后就可以直接用letter代替正则式[A-Za-z]出现

- 第三部分-词法规则

这部分为Lex模式匹配，也是lex代码的重点书写部分。列出词法分析器所需要匹配的正则表达式（或其在第二部分定义的简称），及其在匹配到该正规式时需要进行的动作。 如： 

```
{printf(“whitespace\n”);}
{printf(“linebreak\n”);} 
“int” {printf(“INT\n”)}; 
“=” {printf(“colon\n”);} 
{letter} 
{printf(“ID\n”);}
```

每一行一条规则，规则前半部分时正规式或简称，注意若是匹配字符串则需要用双引号包含，若使用第二部分定义的简称需要用{}包含。后半部分用空格和前半部分用空格隔开，用{}包含，编写匹配到了该词法是程序需要执行的步骤，完全遵照C语言语法，例子中是匹配成功输出相应提示信息。

- 第四部分 - 辅助函数段

这一部分也是用C语言语法，内容也会全部原原本本地拷贝lex.yy.c函数中。在使用中我定义了`yywrap()` 的函数，这是为了兼容flex版本>2.5.4，除了定义该函数，也可以在文件首行写`%option noyywrap`，否则编译lex.yy.c时会出现`undefined reference toyywrap`的错误。具体可参考：[Undefined Reference To yywrap - stackoverflow](https://stackoverflow.com/questions/1811125/undefined-reference-to-yywrap) 

# Yacc使用

一个基本的yacc.y程序 

```c
%{ #include /*第一部分*/ %} 
/*第二部分*/ %token ID %% 
/*第三部分*/ definition: INT ID | ID ; %% 
/*第四部分*/ 
int yyerror(char * msg) { 
	printf(“\nerror:%s\n”,msg); 
}
```

编译yacc.y文件 `bison yacc.y` 生成yacc.tab.c文件

# Yacc语法

要生成最终的语法编译器，需要词法分析器的配合，所以yacc的语法结合lex程序一起分析。

- 第一部分

与lex相同

- 第二部分

说明语法规则中需要用到的所有终结符和定义算符优先级和结合性等。 具体如下： 

```
%start //语法开始符 
%token //终结符 
%left //左结合性 
%right //右结合性 
%nonassoc //无结合性 
%type //非终结符语义类型 
%union //语义值类型说明
```

`%start` 后接的是上下文无关文法的开始符号，它是一个特殊的非终结符，所有的推导都从这个非终结符开始。如果不说明%start，yacc自动将语法规则部分（第三部分）中第一条语法规则左部的非终结符作为语法开始符。 `%token`所接的是终结符，也就是词法分析器中匹配的元素，我们首先要在lex.l中的词法匹配中返回终结符的名称，才能在yacc.y中使用这个非终结符。 举个例子 在lex.l第三部分中： 

```
%% [A-Za-z]+ {printf(“ID\n”);return (ID);} %% 
```

 返回名称为ID的终结符 在yacc.y第二部分中： [cc]%token ID 如果有多个非终结符，可以换行，也可以写在同一行`%token ID NUM` `%token ID %token NUM `  `%left ` `%right  ` `%nonassoc`后接需要定义优先级的算符，定义在越后排优先级越高，`%nonassoc`需要对应`%prec`使用，`%prec`使用在语法规则中。 举个例子 

```
/*第二部分*/ %token ID %left ‘+’ ‘-‘ %left ‘*‘ %nonassoc UMINUS %% 
/*第三部分*/ term: ‘-‘ term %prec | term ‘*‘ ID | term ‘+’ ID | term ‘-‘ ID ; %%
```

 就可以定义’*‘优先级大于’+’和’-‘，但首项可以带符号’-‘，首项的符号优先级大于因子的算符。 比如1-2\*3的计算是1-(2*3)，但-1*2的计算是(-1)*2而不是-(1*2)。

### 区分左结合性和右结合性

文法1：

```
term : term+factor |factor
```

 文法2：

```
term : factor+term |factor 
```

文法1中‘+’是左结合的，文法2中‘+’是右结合的。 文法1中`term = ( term + factor1) + factor2`是符合的，其中factor1和前一个+号结合，所以该运算符‘+’是左结合的 文法2中`term = factor1 + ( factor2 + term)`是符合的，其中factor2和后一个+号结合，所以该运算符‘+’是右结合的 除了算符可以定义优先级，我们也可以利用优先级来定义语法的关键字。 比如在S语言中，有var, const, if, while等关键字，它们同样也符合变量的词法定义`{A-Za-z}({A-Za-z}|{0-9})*`，在语法分析中就会被识别成变量而导致分析错误，但如果把这些关键字的优先级设置为高于变量，这样在语法分析中就会优先被识别为关键字。注意它们应该都是右结合性，原因参考上面写的优先级判别。 如：

```
%right ID %right CONST VAR IF ELSE THEN WHILE DO BEGIN END
```



- 第三部分

这部分写语言的BNF范式，冒号:的左边即BNF范式的左边，冒号右边即BNF范式的右边，多个推导用|分隔，最后要用分号;结尾。每项推导后面也可以用{}包含C代码，作为匹配到该条语法规则后想要程序进行的动作。

- 第四部分

与lex相同 值得注意的是yacc自带的一个函数yyerror 

```
int yyerror(char *msg) { printf(“\nerror:%s\n”,msg); }
```

可以输出编译测试代码的错误信息。 但是要清醒一点，输出的是测试代码的错误，而不是yacc代码的错误，yacc是语言编译器的编译器，以c语言举例，它编译生成的是gcc，使用gcc编译用c语言写的测试代码，输出的是测试代码的错误信息而不是gcc编译器源码的错误。有段时间我一直测试输出parse error，其实是进行测试的代码段不符合语言的语法，我非常脑子不清楚地改了半天源码= = 测试代码可以用txt文件储存 

```c
int main() { 
	extern FILE* yyin; yyin = fopen(“test.txt”,”r”); 
	yyparse(); 
	return 0; 
}
```

在主函数中读取并分析

# 生成语法分析器

lex.l需要引用yacc.tab.h头文件，所以还要用bison生成yacc.tab.h头文件`yacc -d yacc.y`加参数-d会生成yacc.tab.h和yacc.tab.c文件 然后编译lex.l文件生成lex.yy.c文件 `flex lex.l`编译lex.yy.c和yacc.tab.c文件 g`cc lex.yy.c yacc.tab.c` 默认生成的可执行文件是a.exe，这个可执行文件就是我们的语法编译器了。

# 解决文法冲突

在第一次写复杂如S语言的文法，难免会出现移进/归约冲突、归约/归约冲突，还可能会定义了无用规则等错误，这些错误bison都会在我们编译yacc.y时指出来

 我们可以通过生成yacc.output文件来找到具体是那些文法出了错误 `bison -v yacc.y` 在yacc.output文件中 

```
Useless nonterminals: loop_stmt Terminals which are not used: WHILE DO CASE Useless rules: #18 loop_stmt : WHILE condition DO stmt; Conflict in state 42 between rule 31 and token ‘+’ resolved as shift. Conflict in state 42 between rule 31 and token ‘*‘ resolved as shift. Conflict in state 65 between rule 17 and token ELSE resolved as shift. Conflict in state 70 between rule 29 and token ‘+’ resolved as shift. Conflict in state 70 between rule 29 and token ‘*‘ resolved as shift. Conflict in state 71 between rule 30 and token ‘+’ resolved as reduce. Conflict in state 71 between rule 30 and token ‘*‘ resolved as shift. 
State 27 contains 1 shift/reduce conflict.
State 43 contains 11 reduce/reduce conflicts.
```

先检查无用的非终结符和无用的规则是否忘了在文法中使用，如果不是则要删除。 状态27存在移进/归约错误 

```
state 27 term ; factor . ‘*‘ term (rule 34) term ; factor . ‘/‘ term (rule 35) unary ; factor . (rule 38) ‘*‘ shift, and go to state 54 ‘/‘ shift, and go to state 55 ‘*‘ [reduce using rule 38 (unary)] $default reduce using rule 38 (unary)
```

可以看到识别到’*’时可以移进也可以归约，需要对文法进行更改。 状态43存在移进归约/归约错误，查看同移进/归约，找到相应规则后进行修改。

# 附录

## S语言的BNF表示

(1) <程序>→[<常量说明>][<变量说明>]<语句> 

(2) <常量说明>→Const <常量定义>{，<常量定义>}； 

(3) <常量定义>→<标识符>＝<无符号整数> 

(4) <无符号整数>→<数字>{<数字>} 

(5) <字母>→a|b|c| … |z 

(6) <数字>→0|1|2| … |9 

(7) <标识符>→<字母>{<字母>|<数字>} 

(8) <变量说明>→Var <标识符>{，<标识符>}； 

(9) <语句>→<赋值语句>|<条件语句>|<当循环语句>|<复合语句>|ε 

(10) <赋值语句>→<标识符>＝<表达式>;

 (11) <表达式>→[＋|－]<项>{<加法运算符><项>} 

(12) <项>→<因子>{<乘法运算符><因子>} 

(13) <因子>→<标识符>|<无符号整数>|‘(’<表达式>‘)’

 (14) <加法运算符>→＋|－

 (15) <乘法运算符>→* |／ 

(16) <条件语句>→if <条件> then <语句>| if <条件> then <语句> else <语句> 

(17) <条件>→<表达式><关系运算符><表达式> 

(18) <关系运算符>→＝＝|＜＝|＜|＞|＞＝|＜＞ 

(19) <当循环语句>→while <条件> do <语句> 

(20) <复合语句>→begin <语句>{；<语句>} end 注：产生式中<、>括起的部分表示一个非终结符号，[、]括起的部分表示可 选项，{、}括起的部分表示可重复，符号 | 表示“或”。

一个简单文法的lex.l和yacc.y代码 文法： E→E+T | T T→T*F |F F→( E )