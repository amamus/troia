

===============================
Table on Dialog : Table Control
===============================

.

Introduction
------------

.

Features of Table Control
-------------------------
.


Column Information & Labels
---------------------------


Table Events
-----------------------

|RowInsert         | |
|RowDelete         | |
|RowUndelete       | |
|CellChangeBefore  | |
|CellChangeAfter   | |
|ZoomAfter         | |
|ZoomBefore        | |
|ColumnDoubleClick | |
|Copy              | |
|Paste             | |
|Drag              | |
|Drop              | |
|RightClickMenu    | |
|ExpandBefore      | |
|ArrowClick        | |
|ButtonCellClick   | |


Other events of table control; ColumnSort will be discussed in related sections.


Sorting UI Tables
-----------------

As mentioned before all table controls has a table variable as its model on server side. So there is no difference between sorting a ui table and a table variable. All SORT command variations are supported for table variables that is created as a ui table model. 

Additionally users are able to sort table data over right click menu of table column header. In right click menu there are three options for "ascending sorting", "descending sorting" and "clear sorting info". 

Although sorting over right click menu is an user level operation and its not directly related with development process, programmers is able to add some behaviour when user sorts table over column right click menu thanks to **ColumnSort** event. This event is fired after sorting operation, so programmers must consider that table's data is sorted on event code. Also some system variables are set befere the event, to help programmers read some useful information about the sorting operation. Here is the list of system variables which set before ColumnSort event:

+------------------+----------------------------------------------------------------+
| SYS_SORTEDTABLE  | Name of sorted last sorted table.                              |
+------------------+----------------------------------------------------------------+
| SYS_SORTEDCOLUMN | Comma separated list of sorted columns                         |
+------------------+----------------------------------------------------------------+
| SYS_SORTED       | Comma separated list of sort columns an directions (asc, desc).|
+------------------+----------------------------------------------------------------+

**SYS_SORTED** variable returns columns and sorting directions in SORT command's format (ASC USERNAME, DESC CREATEDBY), so programmers is able to reuse this data on dynamic sort operations. Here is a sample **ColumnSort** event code, that prints variables anda table data for "DEVT11 - Runcode Test Transaction".

::

	STRINGVAR3 = 'SORTEDTABLE: ' + SYS_SORTEDTABLE + TOCHAR(10);
	STRINGVAR3 = STRINGVAR3 + 'SORTED: ' + SYS_SORTED + TOCHAR(10);
	STRINGVAR3 = STRINGVAR3 + 'SORTEDCOLUMN: ' + SYS_SORTEDCOLUMN + TOCHAR(10);

	STRINGVAR3 = STRINGVAR3 + 'ROWS' + TOCHAR(10) + '---------' + TOCHAR(10);
	LOOP AT TMPTABLE
	BEGIN
		STRINGVAR3 = STRINGVAR3 + TMPTABLE_USERNAME + TOCHAR(10);
	ENDLOOP;


Menu item that clears sorting information also fires **ColumnSort** event and clears system variables, but does not relocate rows to their previous position before the table sort.
	

Aggregate Commands/Calculating Subtotals
----------------------------------------

Calculating summary data's such as subtotals, maximum, minimum or average is very common operation in business layer applications. Aggregate commands eases calculating such summary information based on colum values.
	
Tree Tables
-----------
tree table and related flags.


Pivot View and Pivot Configurations
-----------------------------------
.

Chart View and Chart Configurations
-----------------------------------
.

Other Useful Features
---------------------

Report Wizard & Templates
=========================

.

Filtering Rows
==============
. FILTERED flag.



Conditional Formatting
======================
.


Aggregate and Aggregate Commands
================================

.


Exercise 1: Colouring Rows & Row ToolTip
--------------------------------------


Exercise 2: Hiding Rows
---------------------

