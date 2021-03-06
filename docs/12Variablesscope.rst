

=======================
Variables and Scope
=======================

	
Scope
--------------------

Simply, **scope** of a variable definition is the part or range of software that definition is valid.

In TROIA there are three levels of scope: global, member, local.

Global Scope
====================

If a variable defined as global variable, it is accessible from any part of the software. In TROIA, largest scope is global and it is bounded by transaction (application).
In other words, if you define a global variable, you can access it from any dialog or class method/event providing that all items run in a transaction (application).


Member Scope
====================

If a variable defined as a member variable, it is accessible in all methods of the class that is defined in, so class is the largest boundary for a member variable.

It is not possible to access member variables using only their name outside of the class, because they are defined for each instance of class. 
To access a member variable of a class instance @ operator is used. (It is similar to . operator in other languages, INSTANCE1@XPOS	: XPOS member of a class instance named INSTANCE1)

::
	
	In a class method, a member variable overrides variables that is 
	    defined for a larger scope (global). 
		
For example if you have an integer type member variable named XPOS and a global variable with same name, all methods use member when XPOS is accessed to do anything.
	

Local Scope
====================

If a variable defined as a local variable, it is accessable only in the method/event that it is defined in, so method/event is the largest boundary for a local variable.

In TROIA, it is not possible to define a variable for a narrower scope like a block, so if you define a local variable you can access it anywhere in method/event.

::

	A local variable overrides other variables 
	    which are defined for a larger scope (member, global).
	
Local variables are created for each instance of the method/event like any other language (think on recursive calls).

::

	A static local variable is a variable that is defined for once 
	    and shared by all instances of method. 
	
	Static local variables are not supported in TROIA.	
	
Function parameters are local symbols that, programmer can access them only inside the method using parameter name.


Defining Variables
--------------------

In TROIA, there are multiple variable definition methods. The first and most common method is using data definition commands such as LOCAL, GLOBAL, MEMBER and OBJECT.

Another common method is using controls on dialogs. For example, when a dialog containing textfield is opened, system automatically defines a global string symbol which has same name with textfield. They are called as "control symbols". All control symbols are global.
Each control type/subtype has a corresponding variable type to define automatically. Controls, types and subtypes will be discussed in next sections detailly.

Additionally, there are (limited) special commands which are able to define fixed type global variables if target variables do not exist. (ex:SELECT, TABLE), however this method is not recommended because of readibility.



Variable Definition Commands
-------------------------------------

GLOBAL Command
====================

GLOBAL command defines a global variable with given data type. Defining multiple variables with a single GLOBAL command is allowed, even if data types are different.

::

	/* define a single variable */
	GLOBAL:
		STRING STRINGVAR1;
		
	/* define multiple variables */
	GLOBAL:
		STRING STRINGVAR1,
		STRING STRINGVAR2,
		TABLE TABLEVAR1,
		INTEGER INTVAR,
		MYCLASS MYCLASSREC;


MEMBER Command
====================

MEMBER command defines a member variable which is accessible from all methods of the class. It can be used in only class methods including constructor and others, so using MEMBER command in dialogs, reports (etc.) is a programming error. It is possible to define multiple members variables in a single MEMBER command even if data types are different.

::

	/* define a single member */
	MEMBER:
		STRING STUDENTNAME;
		
	/* define multiple members */
	MEMBER:
		STRING FIRSTNAME,
		STRING LASTNAME,
		TABLE ITEMLIST,
		INTEGER XPOSITION
		MYCLASS MYCLASSREC;
		
All kind of data types such as STRING, INTEGER, TABLE or TROIA classes can be defined as member variable.
		
*TROIA Classes and its constructors (_CONSTRUCTOR & _VARIABLES) will be discussed detailly in next sections.*


LOCAL Command
====================

LOCAL Command defines a local variable with given data type. Similar to other data definition commands it is possible to define multiple variables with a single LOCAL command.

::

	/* define a single local variable */
	LOCAL:
		STRING STRINGVAR1;
		
	/* define multiple local variables */
	LOCAL:
		STRING STRINGVAR1,
		STRING STRINGVAR2,
		TABLE TABLEVAR1,
		INTEGER INTVAR,
		MYCLASS MYCLASSREC;

OBJECT Command
====================

OBJECT command is the oldest and most used variable definition command. When a variable is defined with OBJECT command, it's scope depends on data type of the variable and which method that OBJECT command is used in.

The main parameter is data type to decide scope. Tables and class instances are always global. But scope of simple type (STRING, DECIMAL, LONG, INTEGER,...) variables depends on the method that they defined in. Simple typed variables are defined as global if definition is made on a dialog/report method or event, if method is a class constructor ( _CONSTRUCTOR & _VARIABLES) scope is member, but if method is a regular class method simple variables are defined as local. 

Here is a simple table that shows how OBJECT command decides scope, depending on data type and method type.

+--------------------+----------------------------------+----------------------------------+--------------------+
|                    | **Dialog/Report Events&Methods** | **Class Constructor&Variables**  | **Class Methods**  |
+--------------------+----------------------------------+----------------------------------+--------------------+
| **Table**          |              Global              |              Global              |       Global       |
+--------------------+----------------------------------+----------------------------------+--------------------+
| **Class Instance** |              Global              |              Global              |       Global       |
+--------------------+----------------------------------+----------------------------------+--------------------+
| **Simple Types**   |              Global              |              Member              |       Local        |
+--------------------+----------------------------------+----------------------------------+--------------------+

It is also supported multiple variable definitions on a single OBJECT command.

::

	/* suppose that this is a dialog method, think on its scope */
	OBJECT:
		STRING STRINGVAR1;
		
	/* suppose that this is a class method, think on their scope */
	OBJECT:
		STRING STRINGVAR1,
		STRING STRINGVAR2,
		TABLE TABLEVAR1,
		INTEGER INTVAR,
		MYCLASS MYCLASSREC;

At first glance, it is a little bit hard to decide scope of a variable that is defined by OBJECT command. As a result of this fact, using GLOBAL, LOCAL and MEMBER instead of OBJECT is recommended to increase readibility. (Unfortunately, you must know that OBJECT is the most used data definition command on existing TROIA applications.)

System Variables
--------------------

System variables are global and predefined variables that stores information about system, user session or some specific actions to use these values on TROIA level.
Most of system variables are read-only and their data types depends on variable's purpose.

Some examples of system variables are listed below, for more please view TROIA Help.

::

	SYS_CURRENTDATE       : Returns long value of now.
	SYS_CLIENT            : Client value that is used while login.
	SYS_LANGU             : Language value that is used while login.
	SYS_USER              : Username of current user.
	SYS_VERSION           : TROIA platform server version.
	SYS_AFFECTEDROWCOUNT  : Number of affected rows after db update/insert/delete.
	SYS_CURRENTDIALOG     : Name of current dialog.
	CONFIRM               : Selected value after a confirm or option message.
	SQL                   : Latest SQL Query that is sent to database.
	
It is not allowed to define variables which have same name with a system variable. Most of them starts with SYS prefix, although there are exceptions such as SQL, CONFIRM etc. 

::

	Defining variables that start with 'SYS' prefix 
	    is not a good programming practice.


Some Facts About Defining Variables
------------------------------------------------------------

Using Undefined Variables
===========================

Using undefined variables do not cause compiling errors because of TROIA's structure (data transfer between dialogs). If a variable is used before it is defined, it returns its name as value like a string variable that has same value with its name. **Although undefined variables are similar to string variables, we must know that they are not STRING.**

::

	LOCAL:
		STRING MYVAR;
		
		MYVAR = MYUNDEFINEDVAR;
		
		/* MYVAR's value: MYUNDEFINEDVAR */
		

Defining Same Variable More Than Once
========================================

The first way of defining same variable more than once is writing different definition commands which define same variable. In this case second command ignores the definition. Here is the sample:

::
	
	OBJECT:
		STRING RESULT,
		STRING MYVAR;
		
	MYVAR = 'Hello World';
	
	OBJECT:
		STRING MYVAR;
		
	RESULT = MYVAR;
	
	/*  RESULT's value: Hello World
		Second OBJECT command ignored the MYVAR definition. */
		
Second method is running same definition command multiple times. In a loop or an event which is triggered multiple times. In this case, the definition command which defines the variable initializes it (sets its default value, to see the default values please see data types section).


::
	
	start loop block that runs twice:
		OBJECT:
			STRING MYVAR;
			
		RESULT = MYVAR;
			
		MYVAR = 'Hello World';
	end loop

	OBJECT:
		STRING MYVAR;
		
	RESULT = MYVAR;
		
	/* in first and second iteration RESULT's value is empty string.
	   after last assignment RESULT's value: Hello World			*/
	   
*Looping and assignments will be discussed detailly in next sections, in this section please focus on defining same variable in multiple times.*

Third method is running a data definition command for a variable that is already defined as control symbol. This case is ignored by the interpreter.

To Learn Data Type of a Variable
================================

In some cases, programmers may need data type of a variable. In TROIA, GETVARTYPE() system function(predefined function) returns type of given variable as string.

For undefined variables GETVARTYPE returns 'UNKNOWN TYPE' even if undefined variables are similar to strings. Here is an example of GETVARTYPE() function.

::

	OBJECT:
		STRING STRINGVAR,
		INTEGER INTVAR,
		STRING RESULT;
		
	RESULT = GETVARTYPE(STRINGVAR); /* RESULT : 'STRING' */
	RESULT = GETVARTYPE(INTVAR);    /* RESULT : 'INTEGER' */
	RESULT = GETVARTYPE(NOVAR);     /* RESULT : 'UNKNOWN TYPE' */


Naming & Conventions
======================

+ Although using numbers in variable names is supported, using a number as a first character is not recommended.

+ Defining a variable which has same name with a TROIA command, function, system variable or data type is considered as TROIA coding error.

+ As a TROIA programming convention TROIA codes are written in upper-case, so using upper-case for variable names is recommended.

+ Defining all variables as global is not a good programming convention, variables must be defined narrowest scope that is possible, to save memory, eliminate possible bugs and readability.
