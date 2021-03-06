

=======================
Flow Control
=======================

	
IF Statement
--------------------
Like other programming languages, IF statement is the main and most used conditional statement in TROIA language. TROIA Interpreter decides to run or ignore IF block depending on conditional expression result.
Here is the syntax of IF Statement in TROIA:

::

	IF condition THEN
		block
	ENDIF;

As it is obvious on syntax above, there is not any kind of enclosing brackets both for condition and true block, three keywords ( IF, THEN, ENDIF) enclose 'condition' and 'block'.
'condition' is an expression which is a combination of relational and logical operators and function calls. 'block' is another TROIA code block.
Here is an example:

::

	IF A > 1 && A < THIS.GETMAXVALUE(P1) THEN
		RESULT = 'A is between one and maximum value';
	ENDIF;
	
In TROIA all logical expression is executed even if first condition is enough to calculate logical expression's result, unlike most programming languages. Lets say A is 0 before the IF statement, so A > 1 condition returns false. If one of && (and) operator's operands is false, result is always false. TROIA Interpreter executes A < THIS.GETMAXVALUE() condition, although condition result is obvious. Behaviour is same for || (or) operator when its first operand is true.
	

IF-ELSE Statement
====================
ELSE keyword is used to define another block that will be executed when 'if condition' is false.

::

	IF A == 1 THEN
		RESULT = 'A equals to one';
	ELSE
		RESULT = 'A is less or more than one';
	ENDIF;

ELSE IF is not supported in TROIA, to run multiple conditions you must use nested if blocks. Here is a valid ELSE IF example:

::

	IF A == 1 THEN
		RESULT = 'A equals to one';
	ELSE
		IF A < 1 THEN
			RESULT = 'A is less than one';
		ELSE
			RESULT = 'A is more than one';
		ENDIF;
	ENDIF;


SWITCH Statement
--------------------

SWITCH statement is another way of defining conditional blocks. Here is the syntax of SWITCH statement in TROIA:

::

	SWITCH item
		CASE value[,value]:
			case block 1
		CASE value:
			case block 2
		.
		.
		CASE value:
			case block n
		DEFAULT:
			default block
	ENDSWITCH;
	

SWITCH Statement allows using only variables as switch item so you can not use an expression. SWITCH command compares item's text value with string values of cases defined with CASE keyword, so programmers must consider string values of cases and given item.

If you want to execute same block for more than one CASE values you can list values in a single case using comma (,) as separator. In TROIA, BREAK keyword is not supported and required in SWITCH statements. Here is a simple example of SWITCH statement:

::

	OBJECT: 
		STRING VAR,
		STRING RESULT;

	VAR = '8';

	SWITCH VAR 
	CASE 5:
		RESULT = 'It is five';
	CASE 6:
		RESULT = 'It is six';
	CASE '7','8':
		RESULT = 'It is seven or eight';
	DEFAULT:
		RESULT = 'I do not know what it is.';
	ENDSWITCH;
	


WHILE Loop
--------------------

While loop is a flow-control statement that allows executing a block of code repeatedly based on a condition. Like most of programming languages, TROIA also supports while loops. Here is the syntax of while loop in TROIA.

::

	WHILE condition
	BEGIN
		block
	ENDWHILE;


Here is a simple example of WHILE command that tries to find if numbers are even or odd. 	
	
::

	OBJECT: 
		INTEGER VAR,
		STRING RESULT;

	VAR = 1;
	RESULT = '';

	WHILE VAR < 10 
	BEGIN

		IF VAR % 2 == 0 THEN
			RESULT = RESULT + VAR + ':even, ';
		ELSE
			RESULT = RESULT + VAR + ':odd, ';
		ENDIF;

		VAR = VAR + 1;
	ENDWHILE;

In most programming languages there are alternative looping statements like for, foreach, even if programmers are able to implement same behaviour using different looping statements. TROIA does not support FOR and FOREACH statements.

LOOP Command
--------------------

As mentioned before, TABLE is the most important data type of the language and programming approach is mostly depends on tables. So TROIA supports many options, commands, functions etc. to manipulate tables. One of this table specific commands is "LOOP" which is an alternative looping command for only tables.

The LOOP command has many options such as condition or performance issues, but "for now" simply it can thought of as a kind of "foreach row".

::

	LOOP AT table
	BEGIN
		block
	ENDLOOP;

We will discuss LOOP command and other table options in next sections. For now we must know only its basic usage. As it is obvious in syntax below, you must specify the name of table variable to loop on and the code block that will be executed for each row.


Other Looping Options
--------------------------------

Another looping option in TROIA is PARSE command which allows you loop on strings which is splitted by a given separator(delimiter). This command also will be discussed in next sections.


BREAK & CONTINUE Statements
----------------------------

In some cases within loop/while block, there is need to skip the remaining part of loop/while block and continue with the next iteration of the loop. To cancel remaining part of loop and jump to the beginning of loop/while block, CONTINUE statement is used.

Similarly, BREAK statement is used to cancel whole loop. 

Here is a simple example that combines BREAK and CONTINUE statements to find list and total of odd numbers between 0 and 10. In this example, condition of WHILE loop is always TRUE, so at first glance this loop is an infinite loop. But in first IF block, code limits re-execution count of loop block with BREAK command. In second IF block, condition checks whether value is even and if value is even CONTINUE skips remaining parts of block and jumps beginning of while block.

::

	OBJECT: 
		INTEGER INDEXNUM,
		INTEGER ODDNUMBERSTOTAL,
		STRING ODDNUMBERS;

	ODDNUMBERSTOTAL = 0;
	INDEXNUM = 0;
	ODDNUMBERS = '';

	WHILE 1 == 1 
	BEGIN

		INDEXNUM = INDEXNUM + 1;

		/* if block 1 */
		IF INDEXNUM == 10 THEN
		   BREAK;
		ENDIF;

		/* if block 2 */
		IF INDEXNUM % 2 == 0 THEN
			CONTINUE;
		ENDIF;
		
		ODDNUMBERSTOTAL = ODDNUMBERSTOTAL + INDEXNUM;
		ODDNUMBERS = ODDNUMBERS + INDEXNUM + ',';
		
	ENDWHILE;
	
Please run the example above and try to find values of ODDNUMBERS and ODDNUMBERSTOTAL variables. And discuss the new behaviour of code, when you swap the orders of "if block 1" and "if block 2".

Exercise 1: Fibonacci Numbers
----------------------------

Fibonacci Numbers is a number sequence that each element is the sum of previous two items. In this example we will try to calculate fibonacci sequence whose maximum item is less than 100.

::

	OBJECT: 
		INTEGER NUMBER1,
		INTEGER NUMBER2,
		INTEGER NUMBER3,
		STRING LISTOFNUMBERS;

	OBJECT: 
		INTEGER MAXNUM;

	MAXNUM = 100;
	NUMBER1 = 1;
	NUMBER2 = 1;
	LISTOFNUMBERS = '';

	WHILE 1 == 1 
	BEGIN
		NUMBER3 = NUMBER1 + NUMBER2;

		IF NUMBER3 > MAXNUM THEN
			BREAK;
		ENDIF;

		NUMBER1 = NUMBER2;
		NUMBER2 = NUMBER3;
		LISTOFNUMBERS = LISTOFNUMBERS + NUMBER3 + ';';
	ENDWHILE;

	
Exercise 2: Factorial
----------------------------

Calculating factorial of a given number with a simple while loop. This example can be tested in test transaction, such as "DEVT11 - Runcode Test Transaction"

::

	LOCAL:
		INTEGER NUMBER;

	LOCAL:
		INTEGER FACTORIAL,
		INTEGER INDEXNUM;

	NUMBER = 4;

	INDEXNUM = 1;
	FACTORIAL = 1;

	WHILE INDEXNUM <= NUMBER
	BEGIN
		FACTORIAL = FACTORIAL * INDEXNUM;
		INDEXNUM = INDEXNUM + 1;
	ENDWHILE;
	

Another option is writing a recursive method (simply, a function that calls itself). *For now, you can ignore the question "How can i define a method?", this will be discussed in next sections. Just focus on function call, recursivity and loop relation and discuss the scope of variables that is defined in a recursive function (MINUS1)*

::

	/* this is method's code */
	PARAMETERS:
		INTEGER PNUM;
	
	LOCAL:
		INTEGER MINUS1;
	   
	IF PNUM <= 1 THEN
		RETURN 1;
	ELSE
		MINUS1 = PNUM - 1;
		RETURN PNUM * THIS.FACTORIAL(MINUS1);
	ENDIF;
	
	
	
	/* this is the code that calls method */
	OBJECT:
		INTEGER FACTORIAL;
	
	FACTORIAL = THIS.FACTORIAL(3);
	
