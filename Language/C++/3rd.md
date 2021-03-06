# 3rd

## 1. libevent

### 1.1 编译安装

#### 1.1.1 windows下交叉编译

使用visual studio自带的交叉编译工具。新建build_zlib.bat编译zlib。

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\zlib
call %VS%

cd F:\Codebase\libevent\zlib-1.2.11
nmake /f win32\Makefile.msc clean
nmake /f win32\Makefile.msc

md %OUT%
md %OUT%\lib
md %OUT%\bin
md %OUT%\include

copy /Y *.lib %OUT%\lib
copy /Y *.h %OUT%\include
copy /Y *.exe %OUT%\bin
copy /Y *.dll %OUT%\bin
pause
```

新建build_openssl.bat编译open-ssl，需要安装perl、nasm。

**perl Configure [VC-WIN32/VC-WIN64A/VC-WIN64I} --prefix=%OUTPATH% **

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\openssl
call %VS%

cd F:\Codebase\libevent\openssl-1.1.1
perl Configure VC-WIN64A --prefix=%OUT%

md %OUT%

nmake clean
nmake
nmake install
@echo "编译openssl完成"
pause
```

新增build_libevent.bat编译libevent。

```bat
set VS="D:\IDE\VS2019\VC\Auxiliary\Build\vcvarsx86_amd64.bat"
set OUT=F:\Codebase\libevent\out\libvent
call %VS%

cd F:\Codebase\libevent\libevent-master

nmake /f Makefile.nmake clean
nmake /f Makefile.nmake OPENSSL_DIR=F:\Codebase\libevent\out\openssl

md %OUT%
md %OUT%\lib
md %OUT%\bin
md %OUT%\include


::/Y表示不提示 /S表示包含目录
copy /Y *.lib %OUT%\lib
xcopy /S/Y include %OUT%\include\
xcopy /S/Y WIN32-Code\nmake %OUT%\include\
copy /Y *.exe %OUT%\bin
copy /Y *.dll %OUT%\bin

@echo "编译libevent完成"
pause
```

#### 1.1.2 Linux 下编译

**zlib:**

```bash
$ ./configure
$ make && make install
```

**openssl**:

```bash
$ ./config
$ make && make install
```

**libevent**

```bash
$ cd libevent
$ ./autogen.sh
$ ./configure
$ make && make install
```

### 2. libevent 常用接口

#### 2.1 event_base

#### 2.2 bufferevent

### 3. libevent实战



## 2. boost

### 2.1 编译安装

#### 2.1.1 windows下编译

## 3. OTL 4.0

OTL 4.0, OTL connect class

This class encapsulates *connect* functions: connect, disconnect, commit, roll back, etc. In other words, the otl_connect class is the class for creating and handling *connect* objects together with transaction management.

| *Function / Data member*                                     | *Description*                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `int connected;`                                             | *Connection* flag which shows whether the object is connected to the database or not. The flag only shows that the otl_connect object was successfully connected to the database. If the database connection under the otl_connect object is closed, the flag would still be set to 1. In order to check out whether the database connection is still open or not, a "heartbeat" database round-trip needs to be made, say, a "SELECT <current time> FROM <dual table>", or an actual, application related SQL statement. In other words, in order to detect a database connection loss, a database "heartbeat" round trip needs to be coded up in the source code. |
| `static int otl_initialize (const int threaded_mode=0);`     | Static (in class) function to initialize the OTL environment. It needs to be called only once at the beginning of the program before making the very first connection to the database. The *threaded_mode* is a parameter for specifying if the program is running in the multi-threaded mode but it does not automatically guarantee thread safety, because OTL does not set any mutex locks or critical sections. Threaded_mode = 1 means the multi-threaded mode, 0 -- the single threaded mode. |
| `static int otl_terminate(void);`                            | Static (in class) function to terminate the Oracle 8i/9i OCI environment. It needs to be called only once at the end of the program after closing the very last database connection. This function is just a wrapper around the OCITerminate() call. Usually, in multi-threaded programs, in order to be able to terminate/end the main thread of control, otl_terminate needs to be called, because it detaches the process from the OCI client side shared memory, and does something else, that is not well documented. |
| `void cancel(void);`                                         | *OTL/OCI8/9/10/11 only**.*** Cancel any operation / database call, executing / active in the connect object / database session. Say, the database session is in the *active* state, or, in other words, has a running SQL statement in a thread. This function can be called from another thread, to cancel the execution of the SQL statement asynchronously. Right after the cancel() call returns, the first thread will raise an otl_exception with the following message: *user requested cancel of current operation.* |
| `void change_password(...);`*Parameter* *Description* const char* user_nameuser name const char* old_password,old password const char* new_passwordnew password | *OTL/OCI8/9/10/11 only*. This function changes Oracle user's passwords. The program needs to attach to an Oracle server, and only then this function can be called |
| `void set_transaction_isolation_level(const long int level);` | Sets transaction isolation level. *OTL/DB2-CLI, OTL/ODBC only*. The function allows the user to set the following levels: READ COMMITTED, READ UNCOMMITTED, REPEATABLE READ, SERIALIZABLE. For more detail on the transaction isolation levels, see the regular database manuals.  OTL/ODBC and OTL/DB2-CLI define the following global constants, which can be used as substitutes for the *level* parameter of the function:  **otl_tran_read_uncommitted -** READ UNCOMITTED**otl_tran_read_committed -** READ COMITTED**otl_tran_repetable_read -**REPEATABLE READ**otl_tran_serializable -** SERIALIZABLE |
| `void set_max_long_size(const int amax_size);`               | Set the maximum buffer size for operations with Large Objects: [varchar_long](http://otl.sourceforge.net/otl3_bind_variables.htm#varchar_long), [raw_long](http://otl.sourceforge.net/otl3_bind_variables.htm#raw_long), [clob](http://otl.sourceforge.net/otl3_bind_variables.htm#clob) and [blob](http://otl.sourceforge.net/otl3_bind_variables.htm#blob). This function needs to be called in order to increase the buffer size (default size is 32767). |
| `int get_max_long_size(void);`                               | Get the maximum buffer size for operations with Large Objects: [varchar_long](http://otl.sourceforge.net/otl3_bind_variables.htm#varchar_long), [raw_long](http://otl.sourceforge.net/otl3_bind_variables.htm#varchar_long), [clob](http://otl.sourceforge.net/otl3_bind_variables.htm#clob) and [blob](http://otl.sourceforge.net/otl3_bind_variables.htm#blob). |
| `void set_cursor_type(const int acursor_type=0);`            | This function works in *OTL 4.0/ODBC and OTL 4.0/DB2-CLI only*, and it has not effect in OTL 4.0/OCIx. Sets the cursor type. Once the cursor type is set, the setting will be propagated to all SELECT statements opened via the [otl_stream](http://otl.sourceforge.net/otl3_stream_class.htm) class in the current connect object.  The following cursor types are available (for more detail, see the ODBC Programmer's Guide, or the DB2 CLI Programmer's Guide): SQL_CURSOR_FORWARD_ONLY (default setting)SQL_CURSOR_STATICSQL_CURSOR_KEYSET_DRIVENSQL_CURSOR_DYNAMIC |
| `void set_timeout(const int atimeout=0);`                    | This function works in *OTL 4.0/ODBC only*, and has no effect in OTL 4.0/OCIx, or OTL 4.0/DB2-CLI. It sets the timeout for the current connect object. Once the timeout is set, the setting will be effective for all SQL statements opened in the current connect object. The time unit for the function is 1 second, e.g. setting 60 means the timeout of 1 minute.  In OCIx, a different technique is used to simulate timeouts: create a worker thread that will execute your SQL statement, and a control thread with a timer. If the timer goes off and the SQL statement is still executing, the control thread can call otl_connect::[cancel](http://otl.sourceforge.net/otl3_connect_class.htm#cancel)() on the same connect object that is being used to execute the SQL statement.  With OTL 4.0/DB2 CLI, the same worker thread technique can be used, only otl_stream::[cancel](http://otl.sourceforge.net/otl3_stream_class.htm#cancel)() should be called on the otl_stream object.  In the future, if the OCI or the DB2 CLI provides similar functionality, this function may be implemented. |
| `void set_lob_prefetch_size``(const int prefetch_size=0);`   | *OTL/**[OCI11GR2](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA11G_R2)**/**[OCI12C](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA12C)* only. Sets the `OCI_ATTR_DEFAULT_LOBPREFETCH_SIZE` attribute for the session. The value of this attribute will indicate the default prefetch data size for a LOB locator. This enables you to enable prefetching for all the LOB locators fetched in the session. Default value for this attribute is zero (no prefetch of LOB data). |
| `otl_connect(const char* connect_str,const int auto_commit=0);` | General constructor. It creates an otl_connect object and then calls the [rlogon()](http://otl.sourceforge.net/otl3_connect_class.htm#rlogon) function. |
| `void rlogon(...);`*Parameter* *Description* const char* connect_strconnect string const int auto_commit=0auto commit flag | Logs on / connects to the database.  OTL 4.0/OCIx, OTL 4.0/ODBC, and OTL 4.0/DB2-CLI have different styles of *connect strings: *OTL 4.0/OCIx style  "USER/PASSWORD" (for local Oracle connections)"USER/PASSWORD@TNS_ALIAS" (for remote Oracle connections via SQL*Net)  OTL 4.0/ODBC and OTL 4.0/DB2-CLI style  "USER/PASSWORD@DSN" (Oracle-like style for ODBC or DB2-CLI connections)"DSN=value;UID=value;PWD=value" (ODBC-like style for ODBC or DB2-CLI connection)In OTL/ODBC/OCI8/OCI8i/OCI9i/OCI10g/OCI11g, it is possible for a password value to have a "@" (commercial at character) in it. In order to specify a "@" in a password, or in a UID, the character has to be prefixed with a "**\**", for example: "userid/pass\\@word@DSN", or "UID=scott;PWD=ti\\@ger;DSN=mssql", or "UID=scott\\@servername;PWD=ti\\@ger;DSN=mssql". In these examples, "\\" was used because the examples show C++ string literals.  The auto_commit parameter in this function is used for setting the connection "auto-commit" mode. This means that every single SQL statement, executed in the connection, will be automatically committed. This auto-commit mode has nothing to do with the [otl_stream auto-commit](http://otl.sourceforge.net/otl3_stream_class.htm#set_commit) mode. In order to set the mode to "auto-commit", the auto_commit parameter needs to be set to 1. By default, it is 0, meaning that "auto-commit" is off.  In Oracle 7, ODBC and DB2-CLI, this mode is called "auto-commit". In Oracle 8/8i/9i, it is called "commit-on-success." It is not quite clear why in Oracle 8 the term was changed to "commit-on-success" and pushed from the level of the connection down to the level of the SQL statement execution. |
| `void rlogon(Lda_Def* lda);`                                 | Logs on / connects to / attaches to the primary database connection, which was made in Pro*C/C++ or any other third-party library. This function works for ***OTL/OCI7\*** only, and it helps to make transition from the legacy environment to the OTL environment smoother. See also example [58](http://otl.sourceforge.net/otl3_ex58.htm). |
| `void rlogon(...);`*Parameter* *Description* HENV henvenvironment handle``HDBC hdbcdatabase connect handleconst int auto_commit=0connect auto-commit mode`` | Logs on / connects to / attaches to the primary database connection, which was made in straight ODBC, DB2-CLI, or any other third-party (ODBC, or DB2-CLI based) library. This function works for *OTL/ODBC, DB2-CLI* *only*, and it helps make transition from the legacy environment to the OTL environment smoother. |
| void rlogon(...); *Function* *Description* const [OTL_UNICODE_CHAR_TYPE](http://otl.sourceforge.net/otl3_compile.htm#OTL_UNICODE_CHAR_TYPE)* usernameuser nameconst OTL_UNICODE_CHAR_TYPE* passwdpasswordconst OTL_UNICODE_CHAR_TYPE* dsndata source nameconst int auto_commit=0connect auto-commit mode | *OTL/ODBC, DB2-CLI* *only.* Under #define [OTL_UNICODE_EXCEPTION_AND_RLOGON](http://otl.sourceforge.net/otl3_compile.htm#OTL_UNICODE_EXCEPTION_AND_RLOGON).  Logs on / connects to the database (DSN / database alias). This function works when Unicode ODBC driver functions are enabled (via the C++ standard #define UNICODE, #define _UNICODE). The function allows Unicode user names, passwords, and DSNs to be passed into the ODBC layer. |
| void rlogon(...); *Function* *Description* const char* usernameuser nameconst char* passwdpasswordconst char* dsndata source nameconst int auto_commit=0connect auto-commit mode | *OTL/ODBC, DB2-CLI* *only.  *When !defined([OTL_UNICODE_EXCEPTION_AND_RLOGON](http://otl.sourceforge.net/otl3_compile.htm#OTL_UNICODE_EXCEPTION_AND_RLOGON)) && (ODBCVER>=0x300 \|\| defined([OTL_DB2_CLI](http://otl.sourceforge.net/otl3_compile.htm#OTL_DB2_CLI))).  Logs on / connect to the database (DSN / database alias). This function is available when #define OTL_UNICODE_EXCEPTION_AND_LOGON is not enabled, for ODBC/ DB2 CLI drivers that support ODBC/DB2 CLI versions >= 300 (not older than 25-27 years old in 2018). The function allows one-byte character string user names, passwords, and DSNs to be passed into the ODBC/DB2 CLI layer. The function should be used when passwords have special characters. |
| `otl_connect(...);`*Parameter* *Description* HENV henvenvironment handleHDBC hdbcdatabase connect handleconst int auto_commit=0connect auto-commit mode`` | General constructor: attaches to the primary database connection, which was made in straight ODBC, DB2-CLI, or any other third-party (ODBC, or DB2-CLI based) library. This function works for ***OTL/ODBC, DB2-CLI\*** only, and it helps make transition from the legacy environment to the OTL environment smoother. |
| `void rlogon(...);`*Parameter* *Description* OCIEnv* envhppointer to the OCI8/8i/9i/10g/11g environment handle, which needs to be obtained by calling the *SQLEnvGet* function.OCISvcCtx* svchppointer to the OCI8/8i/9i/10g/11g service context handle, which needs to be obtained by calling the *SQLSvcCtxGet* function. | *OCI8 / 8i / 9i / 10g / 11g* *only*. Logs on / connect to / attach to the primary database connection, which was made in Pro*C/C++ or any other third-party library. It helps make integration of OTL/OCI8/8i/9i/10/g/11g and the Pro*C/C++ environment smooth and easy. In order to use this function in the Pro*C/C++ environment, #define OTL_ORA8_PROC needs to be set at the beginning of the program. For more detail, see example [59](http://otl.sourceforge.net/otl3_ex59.htm) |
| `void rlogon(...);`*Parameter* *Description* const char* connect_strconnect string const int aauto_commit=0auto commit flag const char* xa_server_external_name=0XA server external name const char* xa_server_internal_name=0,XA server internal name bool threaded_mode=falseunder #define [OTL_ORA_OCI_ENV_CREATE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA_OCI_ENV_CREATE) | *OTL/OCI8/8i/9i/10g/11g* *only****.\*** Connects to the database and set the following connection attributes that are required by the Oracle XA Library: server external name, server internal name. |
| `void logoff();`                                             | Logs off / disconnects from the database. In case if the primary connection was done in Pro*C/C++ (OTL/OCIx), this function just detaches from that connection without actually disconnecting from the database. |
| `void server_attach(...);`*Parameter* *Description* const char* tnsname=0TNS name const char* xa_server_external_name=0XA server external name const char* xa_server_internal_name=0XA server internal name bool threaded_mode=falseUnder #define [OTL_ORA_OCI_ENV_CREATE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA_OCI_ENV_CREATE)`` | Attaches to Oracle (*OTL/OCI8/8i/9i/10g/11g* *only*). This function is a wrapper around the ServerAttach function of the OCI8/9/10/11. The main reason to have separate functions to attach to the server and then begin a session is performance. For more detail, see Oracle's regular manuals. These functions, along with the [session_begin](http://otl.sourceforge.net/otl3_connect_class.htm#session_begin) and [session_end](http://otl.sourceforge.net/otl3_connect_class.htm#session_end) functions, could be used for implementing a connection management mechanism that is more efficient than the traditional connection pooling. See also example [60](http://otl.sourceforge.net/otl3_ex60.htm).  tnsname (TNS alias from TNSNAMES.ORA file) can be specified. In order to connect to a remote Oracle server. For local connections, tnsname defaults to 0. The function attaches to the database server (local or remote). The following "server attachment" attributes that are required by the Oracle XA Library can be also specified. |
| `void server_detach();`                                      | *OTL/OCI8/8i/9i/10g/11g* *only.* Detaches from Oracle        |
| `void session_begin(...);`*Function* *Description* const char* usernameuser name, e.g. "scott"const char* passwordpassword, e.g. "tiger"const int auto_commit=0,session [auto-commit](http://otl.sourceforge.net/otl3_connect_class.htm#auto_commit) modeconst int session_mode=OCI_DEFAULTOCI_SYSDBA -- in this mode, the user is authenticated for SYSDBA access. OCI_SYSOPER -- in this mode, the user is authenticated for SYSOPER access. OCI_MIGRATE -- in this mode, the new user session context may be set in a service handle with a different server handle.OCISession* authp=nullptr OCI session handle | Begin / end Oracle 8 session (*OTL/OCI8/8i/9i/10g/11g* *only*). These functions complement the server [attach/detach](http://otl.sourceforge.net/otl3_connect_class.htm#attach_detach) suite of functions, and are used to begin / end an Oracle 8 session. The main reason to have separate server attach/detach and session begin/end groups of function is performance. session_begin() is approximately 50-100 times faster than the normal [rlogon](http://otl.sourceforge.net/otl3_connect_class.htm#rlogon)(). These functions, along with the [server_attach](http://otl.sourceforge.net/otl3_connect_class.htm#attach_detach)() and [server_detach](http://otl.sourceforge.net/otl3_connect_class.htm#server_detach)() functions, could be used for implementing a connection management mechanism that is several times more efficient [thus, more scalable] than the traditional connection pooling.  For more detail on OCISession* and OCI_MIGRATE, see the [OCI Relational Functions/OCISessionBegin](https://docs.oracle.com/cd/B10501_01/appdev.920/a96584/oci15r13.htm). |
| `void session_end();`                                        | *OTL/OCI8/8i/9i/10g/11g* *only.* Ends Oracle session.        |
| `void session_reopen(...);`*Parameter* *Description* const int aauto_commit=0session [auto-commit](http://otl.sourceforge.net/otl3_connect_class.htm#auto_commit) mode | *OTL/OCI8/8i/9i/10g/11g* *only*. Reopens session. If a session was started via [session_begin](http://otl.sourceforge.net/otl3_connect_class.htm#session_begin)() for a given *username/password* and then closed via [session_end](http://otl.sourceforge.net/otl3_connect_class.htm#session_end)(), another session can be started faster (5-10%) for the same username/password via session_reopen(). This function takes advantage of the fact that the username and password attributes of the OCI8/9/10/11 internal connect structure have been already set by the previous session_begin() function call. If a session has been started via session_reopen(), it can be closed by calling session_end().  Also, see example [61](http://otl.sourceforge.net/otl3_ex61.htm) |
| `void commit();`                                             | Commits transaction. In the otl_connect object, Oracle, ODBC and DB2-CLI connections, by default, are set to *auto-commit off.* This means that all transactions need to be committed by explicitly calling the commit() function. It is not recommended to use connections that are set to *auto-commit on.  *This function does not automatically flush any buffered data (a.k.a. otl_stream *dirty* buffers), so you must call [otl_stream::flush](http://otl.sourceforge.net/otl3_stream_class.htm#flush)() on any open INSERT / UPDATE / DELETE / stored procedure call (that modifies the database) first, otherwise the buffered data may not be written to the database in time for the commit. Note that there is an implicit call to flush() in the otl_stream destructor, unless [otl_stream::set_flush](http://otl.sourceforge.net/otl3_stream_class.htm#set_flush)(false) was called before the corresponding otl_stream was opened. |
| void commit_nowait();                                        | Under #define [OTL_ORA10G_R2](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA10G_R2) Under #define[ OTL_ORA11G](http://otl.sourceforge.net/otl3_compile.htm#OTL_ORA11G)  Commits transaction asynchronously, same as commit and forget / commit with no wait. Available only for Oracle 10g Release 2 (Oracle 10.2) and higher. As soon as the rest of the database vendors release a similar feature, it will be implemented in OTL. |
| `void rollback();`                                           | Rolls back transaction.  This function does not discard any buffered data (a.k.a. otl_stream *dirty* buffers), so you must call [otl_stream::flush](http://otl.sourceforge.net/otl3_stream_class.htm#flush)() on any open INSERT / UPDATE / DELETE / stored procedure call (that modifies the database) first, otherwise the buffered data may still be written to the database despite the rollback. Note that there is an implicit call to flush() in the otl_stream destructor, unless [otl_stream::set_flush](http://otl.sourceforge.net/otl3_stream_class.htm#set_flush)(false) was called before the corresponding otl_stream was opened. |
| `void auto_commit_off();void auto_commit_on();`              | Sets otl_connect's [auto-commit](http://otl.sourceforge.net/otl3_connect_class.htm#auto_commit) OFF and ON, within one open session/connect. These two functions are for partial backward compatibility only. |
| `void set_stream_pool_size(const int max_size=otl_max_default_pool_size);` | Sets the [otl_stream pool](http://otl.sourceforge.net/otl3_stream_pooling.htm) size. This function can be activated with #define [OTL_STREAM_POOLING_ON,](http://otl.sourceforge.net/otl3_compile.htm#OTL_STREAM_POOLING_ON) used together with #define [OTL_STL](http://otl.sourceforge.net/otl3_compile.htm#OTL_STL) (or #define [OTL_ACE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ACE), or #define [OTL_UNICODE_STRING_TYPE](http://otl.sourceforge.net/otl3_compile.htm#OTL_UNICODE_STRING_TYPE)). If OTL_STREAM_POOLING_ON is defined in the program, the function body gets included into the target code. Also, a default stream pool gets allocated as part the otl_connect object, even if the function doesn't get called. Therefore, a call to the function will reallocate all the resources, allocated by the default stream pool, or by previous calls to set_stream_pool_size(). |
| `void set_fetch_scroll_mode(bool use_fetch_scroll=true);`    | Sets the "SQLFetchScroll" mode, meaning that otl_stream instances opened under a given otl_connect will use SQLFetchScroll() instead of SQLExtendedFetch(). This function can be activated with #define [OTL_ODBC_USES_SQL_FETCH_SCROLL_WHEN_SPECIFIED_IN_OTL_CONNECT](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_USES_SQL_FETCH_SCROLL_WHEN_SPECIFIED_IN_OTL_CONNECT). |
| void stream_pool_enable(); void stream_pool_disable(); bool get_stream_pool_enabled_flag() const; | These functions allow to enable / disable the stream pool at runtime, as well as to get the "stream pool enabled" flag (a.k.a. an ON/OFF switch). The functions can be activated with #define [OTL_STREAM_POOLING_ON,](http://otl.sourceforge.net/otl3_compile.htm#OTL_STREAM_POOLING_ON) used together with #define [OTL_STL](http://otl.sourceforge.net/otl3_compile.htm#OTL_STL) (or #define [OTL_ACE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ACE)). If OTL_STREAM_POOLING_ON is defined in the program, the function bodies get included into the target code.  "OTL stream pool enabled" flag is set to true by default. |
| `void set_character_set(const int char_set=SQLCS_IMPLICIT);` | This function is available for OTL/***OCI8i/9i\**/10g/11g\*** under #define [OTL_UNICODE](http://otl.sourceforge.net/otl3_compile.htm#OTL_UNICODE) only. In Unicode OTL, the Oracle database default or national character set is allowed to be set:  SQLCS_IMPLICIT -- database default (implicit) character setSQLCS_NCHAR -- database national (second) character set. The default character set (SQLCS_IMPLICIT) needs to be set when Unicode containers are used with the VARCHAR2, CHAR, LONG, and CLOB column types. In the case if Unicode containers are used with NVARCHAR2, NCAHR, and NCLOB datatypes, the national character set (SQLCS_NCHAR) needs to be set.  SQLCS_IMPLICIT, and SQLCS_NCHAR are defined by the OCI header files.  Since this function is part of the otl_connect class, all otl_streams, that gets opened via a concrete connect object, will use the character set, that was set by the function.  The semantic of :var<[char](http://otl.sourceforge.net/otl3_bind_variables.htm#char)[...]...> declaration of string bind variables was extended to accomodate the Unicode strings. For example, char[20] means a string of 19 double-byte Unicode characters, plus 19 double-byte possible surrogate characters, plus one double-byte null terminator. It sounds a little complicated, but that is just a reflection of a plethora of Unicode standards, coding techniques. It is strongly recommended to read the Unicode standards themselves, then the Oracle implementations the Unicode. By the way, Oracle 8i and 9i differ in that regard substantially. |
| `void set_throw_on_sql_success_with_info(const bool throw_flag=false);` | *OTL/ODBC, OTL/DB2-CLI only*. Under #define [OTL_THROWS_ON_SQL_SUCCESS_WITH_INFO](http://otl.sourceforge.net/otl3_compile.htm#OTL_THROWS_ON_SQL_SUCCESS_WITH_INFO)  Sets the "OTL throws on SQL_SUCCESS_WITH_INFO"" flag. When the flag is set, and SQLExecDirect() / SQLExecute() function returns SQL_SUCCESS_WITH_INFO, OTL throws an otl_exception. This is useful for communicating more information from the SQL call, especially if the call is a call to a stored procedure, or a Transact-SQL batch that may return more diagnostic information than usual. The function is enabled only when #define OTL_THROWS_ON_SQL_SUCCES_WITH_INFO is defined. |
| `otl_connect& operator<<(const char* str);`                  | Sends a string to the otl_connect object. If the object is not connected to a database then the string is a connection string in the "userid/passwd@db" format and it makes the object connect to the database. If the object is already connected, the string is a static SQL statement that the connect object executes immediately. |
| `otl_connect& operator<<=(const char* str);`                 | Sends a string to the otl_connect object. The connect object saves the string for further use with operator [>>](http://otl.sourceforge.net/otl3_connect_class.htm#operator_gg)(). The string is an SQL statement with placeholders that could be sent to an otl_stream object. In other words, this operator is a deferred SQL statement. |
| `otl_connect& operator>>(otl_stream& s);`                    | Sends the SQL statement that was saved with operator [<<=](http://otl.sourceforge.net/otl3_connect_class.htm#operator_lle) to an otl_stream object. It makes the otl_stream object open the SQL statement. If there was no SQL statement previously saved by the connect object, the connect object sends the following string to the otl_stream: "*** INVALID COMMAND ***"., which causes an SQL parse error in the otl_stream, which ends up in an otl_exception. |
| `long direct_exec(...);`*Parameter* *Description* const char *sqlstm SQL Statementint ignore_error = [otl_exception::enabled](http://otl.sourceforge.net/otl3_exception_class.htm#enum) "ignore error" flag`` | Directly executes a static SQL statement. The function returns the Rows Processed Count (RPC).  The direct_exec() function may return the following values of the *long int* datatype:**-1**, if otl_exceptions are disabled (the second parameter is set to otl_exception::disabled) and the underlying API returns an error.**>=0**, if the SQL command to be executed is finished succesfully. Actually, the function returns the *ROWS PROCESSED COUNT* (RPC) in case of INSERT, UPDATE or DELETE statements |
| `void syntax_check(const char *sqlstm // SQL Statement``);`  | Parses a static SQL statement, raises an [otl_exception](http://otl.sourceforge.net/otl3_exception_class.htm) if there is an SQL error. |
| `void set_connection_mode(const int connection_mode);`       | When #define [OTL_ODBC_MULTI_MODE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_MULTI_MODE) is enabled, OTL allows ODBC connections to more than one database type to be made from the same program at the same time without enabling any dababase specific #define's, which would be in conflict.  Sets connection mode before otl_connect::rlogon() is called. See the [connection mode list](http://otl.sourceforge.net/otl3_connect_class.htm#connection_mode_list) for mode detail.  The following is the list of available ODBC connection mode integer constants that can be used in calls to set_connection_mode() (ODBC driver specific restrictions like the stream buffer size / unsupported bulk interface still apply, see example [675](http://otl.sourceforge.net/otl4_ex675.htm) for more detail):  OTL_DEFAULT_ODBC_CONNECT (equivalent of #define [OTL_ODBC](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC)). The following database types can be used with the OTL default ODBC multi-mode when the corresponding ODBC driver is used:  DB2FireBirdInformixMS ACCESSOracleSAP/MAX DB SQLite SybaseAny other compatible ODBC source  OTL_TIMESTEN_ODBC_CONNECT (equivalent of #define [OTL_ODBC](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC) + ODBC connection to TimesTen on the backend)  OTL_MSSQL_2005_ODBC_CONNECT (equivalent of #define [OTL_ODBC_MSSQL_2005](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_MSSQL_2005))  OTL_POSTGRESQL_ODBC_CONNECT (equivalent of #define [OTL_ODBC_POSTGRESQL](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_POSTGRESQL))  OTL_ENTERPRISE_DB_ODBC_CONNECT (equivalent of #define [OTL_ODBC_ENTERPRISEDB](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_ENTERPRISEDB))  OTL_MYODBC35_ODBC_CONNECT (equivalent of #define [OTL_ODBC](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC) + #define [OTL_ODBC_SELECT_STM_EXECUTE_BEFORE_DESCRIBE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_SELECT_STM_EXECUTE_BEFORE_DESCRIBE))  OTL_MSSQL_2008_ODBC_CONNECT (equivalent of #define [OTL_ODBC_MSSQL_2008](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_MSSQL_2008)) |
| `int get_connection_mode();`                                 | Under #define [OTL_ODBC_MULTI_MODE](http://otl.sourceforge.net/otl3_compile.htm#OTL_ODBC_MULTI_MODE).  Gets connection mode for the otl_connect object. |
| void set_prog_name(const char* prog_name);                   | Under #define [OTL_DB2_CLI](http://otl.sourceforge.net/otl3_compile.htm#OTL_DB2_CLI).  Sets the program name that shows up as the "Application Name" column in the output of the "list applications" command in DB2 command line processsor. The function should be called before the connection to the database is made (before rlogon() is called), for example:    otl_connect db;   ...   db.set_prog_name("MYAPP");   ...   db.rlogon("scott/tiger@db2sql");   ... |

## 4. gtest

