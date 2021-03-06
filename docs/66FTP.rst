

========================
Working with FTP Servers
========================

*TROIA Platform supports file transfer operations between application server and file server. This section aims to introduce ftp operations and related commands.*

Introduction
------------

FTP Operations are supported on builds after after 5.01.04 041203 in Secure File Transfer Protocol (also known as SFTP or SSH File Transfer Protocol), FTP (also known as File Transfer Protocol), FTPS (File Transfer Protocol SSL) protocols.

All FTP operations are performed on application server,so transferring files from/to client is not supported.

Connection to FTP Server
------------------------

All file server operations must be executed inside and file server connection which is established using MAKEFTPCONNECTION command. MAKEFTPCONNECTION command takes host, port, username and password to connect file server. Port parameter is optional and default ports are 21 for FTP, 990 for FTPS, 22 for SFTP. 

Additionally, programmers must provide the connection protocol to MAKEFTPCONNECTON command. This option is not a symbol and must be selected on development phase. Protocol parameter is optional and default option is FTP.

Here is the syntax of the command:

::

	MAKEFTPCONNECTION HOST {host} [PORT {port}] [USERNAME {username}] 
	                              [PASSW {password}][PROTOCOL {FTP|FTPS|SFTP}];

System allows creating multiple sessions to different file servers, but there is no need to create multiple sessions to same file server. Distinctive parameter between different servers is HOST parameter of connection, so system does not allow multiple connections to same host. Scope of connection management is execution context, so every transaction or TROIA component has its own connection pool.

TROIA programmer is responsible close all connections after operation finished. CLOSEFTPCONNECTION command is used to close connection which is established by MAKEFTPCONNECTION. Here is the syntax:

::
	
	CLOSEFTPCONNECTION {host};

Additionally system tries to kill remaining open connections before transaction close operation, but it is not recommended to leave ftp connections open after all ftp operations finished. Here is a sample code that connects an ftp server and closes connection. Please test the code with valid/invalid host and login cridentials.

::

	OBJECT: 
		STRING FTPHOST,
		STRING FTPPASS,
		STRING FTPUSER;

	FTPHOST = 'anyftp.com.tr';
	FTPUSER = 'user';
	FTPPASS = 'password';

	MAKEFTPCONNECTION HOST FTPHOST USERNAME FTPUSER PASSW FTPPASS PROTOCOL FTP;

	IF SYS_STATUS == 0 THEN
		/* connection is successful */
	ELSE
		/* connection failed */
	ENDIF;

	CLOSEFTPCONNECTION FTPHOST;



Possible Errors on FTP Connections
==================================

Both MAKEFTPCONNECTION and CLOSEFTPCONNECTION commands set SYS_STATUS and SYS_STATUSERROR, if connect/disconnect process fails. Also all exceptional cases are inserted to trace files.  Here are some possible exceptions and solutions.

 - **java.lang.NoClassDefFoundError:** missing required libraries, please read installation/deployment section.
 
 - **com.ias.server.ftp.iasFileServerException: java.net.UnknownHostException:** Application server cannot access given host, check your connection.
 
 - **ftp connection is already exists to … :** System does not allow to create multiple connections to same file server. Please be sure that you closed your ftp connection. Additionally, multiple file transfe operations allowed on a single connections, so there is no need to create connection again.
 
 - **login or password incorrect! or … Auth Fail :** Invalid connection credentials
 
 - **request timeout while connecting host … :** To establish connection between application server and ftp server, max waiting period is 10 seconds. If ftp server does not send response of connection within 10 seconds, system sets this error to SYS_STATUSERROR 



Working Directory
-----------------

After connection established to file server, a working directory is assigned to a client and all commands executed in this path. Also, relative file paths are computed from this working directory.

To read, currently which directory are you working on FTPRUNCOMMAND's CURRENTDIRECTORY variation is used. Changing working directory is possible with FTPRUNCOMMAND's CHANGEDIRECTORY variation. This command also allows some other operations on FTP server for more information please see the command help. Here is the syntax of two variations.

::

	FTPRUNCOMMAND CURRENTDIRECTORY TO {targetvariable} ON {host};
	FTPRUNCOMMAND CHANGEDIRECTORY {pathonftpserver} ON {host};

Transferring Files
------------------

As main functionalities of ftp operations uploading and downloading files must be executed in a FTP connection which is established by MAKEFTPCONNECTION command. Also user information and permissions is about the connection. Both uploading and dowloading commands gets host which shows ftp connection to perform operation on.

All upload and download paths are computed relatively from working directory. So programmers must be sure they are in correct working directory before upload and download files.

Path which file downloaded to/uploaded from must be a valid path on application server. Paths starting with * (client side paths) are not valid for FTP operations. If files are located on client or must be downloaded to client, programmers must transfer file to the server using related file commands.


Downloading Files
=================

To download files FTPDOWNLOAD command is used. Here is the syntax of the command:

::
	
	FTPDOWNLOAD {pathonftpserver} TO {localpath} FROM {host};
	

If downlaoding operation fails, system sets SYS_STATUS and SYS_STATUSERROR system variables, also exceptions are inserted to trace. Possible uploading problems are below:

 - **invalid local path … :** local path is empty string or client side path (starts with *)
 
 - **invalid ftp server path:** remote file path is empty string
 
 - **there is not a ftp connection to host … :** invalid connection id, invalid host. Check your connection is open.
 
 - **permission failure:** check ftp user rights, whether user have required privileges to download file. 
 
::

	OBJECT: 
		STRING FTPHOST,
		STRING FTPPASS,
		STRING FTPUSER,
		STRING LOCALPATH,
		STRING FTPSERVERPATH;

	FTPHOST = 'anyftp.com.tr';
	FTPUSER = 'user';
	FTPPASS = 'password';
	FTPSERVERPATH = 'file.xml';
	LOCALPATH = 'TempFiles\file.xml';

	MAKEFTPCONNECTION HOST FTPHOST USERNAME FTPUSER PASSW FTPPASS PROTOCOL FTP;

	IF SYS_STATUS == 0 THEN
		FTPDOWNLOAD FTPSERVERPATH TO LOCALPATH FROM FTPHOST;
	ENDIF;

	CLOSEFTPCONNECTION FTPHOST;

Uploading Files
===============

To upload files FTPUPLOAD command is used. Here is the synta of the command:	

::

	FTPUPLOAD {localpath} TO {host};
	
If uploading operation fails, system sets SYS_STATUS and SYS_STATUSERROR system variables, also exceptions are inserted to trace. Possible uploading problems are below:

 - **invalid local path … :** local path is empty string or client side path (starts with *)
 
 - **there is not a ftp connection to host … :** invalid connection id, invalid host. Check your connection is open.
 
 - **permission failure:** check ftp user rights, whether user have required privileges to upload file. 
 
::

	OBJECT: 
		STRING FTPHOST,
		STRING FTPPASS,
		STRING FTPUSER,
		STRING LOCALPATH,
		STRING FTPSERVERPATH;

	FTPHOST = 'anyftp.com.tr';
	FTPUSER = 'user';
	FTPPASS = 'password';
	FTPSERVERPATH = 'file.xml';

	MAKEFTPCONNECTION HOST FTPHOST USERNAME FTPUSER PASSW FTPPASS PROTOCOL FTP;

	IF SYS_STATUS == 0 THEN
		FTPUPLOAD LOCALPATH TO FTPHOST;
	ENDIF;

	CLOSEFTPCONNECTION FTPHOST;


Listing Files
-------------

FTP Infrastructure supports listing files. Operation is fired by FTPRUNCOMMAND command's LISTFILE variation and executed as working directory. Result of this command must be assigned to a table symbol, similar to FILELIST command. This command also allows some other operations on FTP server for more information please see the command help. Here is the syntax to list files:

::

	FTPRUNCOMMAND FILELIST TO {targettable} ON {host};
	
	
Here is an example that lists and prints file on initial directory.

::
	
	OBJECT: 
		STRING FTPHOST,
		STRING FTPPASS,
		STRING FTPUSER,
		TABLE FILESTABLE,
		STRING STRINGVAR3;

	FTPHOST = 'anyftp.com.tr';
	FTPUSER = 'user';
	FTPPASS = 'password';

	MAKEFTPCONNECTION HOST FTPHOST USERNAME FTPUSER PASSW FTPPASS PROTOCOL FTP;

	IF SYS_STATUS == 0 THEN

		FTPRUNCOMMAND FILELIST TO FILESTABLE ON FTPHOST;

		LOOP AT FILESTABLE
		BEGIN
			STRINGVAR3 = STRINGVAR3 + FILESTABLE_NAME + TOCHAR(10);
		ENDLOOP;

	ENDIF;

	CLOSEFTPCONNECTION FTPHOST;


Creating/Deleting Folders and Files
-------------------------------------

Infrastructure allows TROIA programmer to create and delete folders on working directory.  These operations are executed on a ftp connection which is established by MAKEFTPCONNECTION command.
To create and delete folders and delete files use FTPRUNCOMMAND command. This command also allows some other operations on FTP server for more information please see the command help. Here is the syntax for directory and file operations:

::

	FTPRUNCOMMAND DELETEDIRECTORY {pathonftpserver} ON {host}; 
	FTPRUNCOMMAND DELETEFILE {pathonftpserver} ON {host};
	FTPRUNCOMMAND CREATEDIRECTORY {pathonftpserver} ON {host}; 
	
Here is an example, that creates a folder on ftp server, changes directory, uploads a local file, finally deletes file and directory.
	
::

	OBJECT: 
		STRING FTPHOST,
		STRING FTPPASS,
		STRING FTPUSER,
		STRING LOCALPATH,
		STRING FTPSERVERPATH,
		STRING DIRNAME,
		TABLE FILESTABLE;

	FTPHOST = 'anyftp.com.tr';
	FTPUSER = 'user';
	FTPPASS = 'password';
	DIRNAME = 'myfolder';
	
	LOCALPATH = 'file.xml';

	MAKEFTPCONNECTION HOST FTPHOST USERNAME FTPUSER PASSW FTPPASS PROTOCOL FTP;

	IF SYS_STATUS == 0 THEN

		FTPRUNCOMMAND CREATEDIRECTORY DIRNAME ON FTPHOST; 
		FTPRUNCOMMAND CHANGEDIRECTORY DIRNAME ON FTPHOST;
		
		FTPUPLOAD LOCALPATH TO FTPHOST;
		FTPRUNCOMMAND DELETEFILE 'file.xml' ON FTPHOST;
	
		FTPRUNCOMMAND DELETEDIRECTORY DIRNAME ON FTPHOST; 

	ENDIF;

	CLOSEFTPCONNECTION FTPHOST;




