```c
#include <stdio.h>
#include <windows.h>
#include <sql.h>
#include <sqlext.h>
#include <sqltypes.h>
#include <odbcss.h>
 
int main(){
 
	// 环境句柄
	SQLHENV henv ;
	// 连接句柄
	SQLHDBC hdbcl ;
	// 语句句柄
	SQLHSTMT hstmt ;
	// 返回值
	SQLRETURN retcode;
 
	UCHAR szDSN[SQL_MAX_DSN_LENGTH+1]="myfeeling", szUID[MAXNAME]="sa",szAuthStr[MAXNAME]="9527";
	UCHAR sql[40]="insert into Student values('aaa','100')";
	UCHAR pre_sql[29]="insert int test values(?,?)";
 
	retcode = SQLAllocHandle(SQL_HANDLE_ENV,SQL_NULL_HANDLE,&henv);
	if((retcode!=SQL_SUCCESS)&&(retcode!=SQL_SUCCESS_WITH_INFO))
	{
	printf("环境句柄失败！\n");
	return 0;
	}
	SQLSetEnvAttr(henv,SQL_ATTR_ODBC_VERSION,(SQLPOINTER)SQL_OV_ODBC3,SQL_IS_INTEGER);
 
	retcode = SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbcl); 
	if ((retcode != SQL_SUCCESS )&&( retcode != SQL_SUCCESS_WITH_INFO))
	{
		printf("连接句柄失败！\n");
		return 0;
	}
	retcode = SQLConnect(hdbcl,szDSN,26,szUID,2,szAuthStr,6);
	if ((retcode != SQL_SUCCESS)&&(retcode != SQL_SUCCESS_WITH_INFO))
	{
		printf("连接数据源失败");
		return 0;
	}
 
	retcode = SQLAllocHandle(SQL_HANDLE_STMT,hdbcl,&hstmt);
	if ((retcode != SQL_SUCCESS)&&(retcode != SQL_SUCCESS_WITH_INFO))
	{
		printf("语句句柄失败");
		return 0;
	}
 
	retcode = SQLExecDirect(hstmt,sql,40);
	SQLDisconnect(hdbcl);
	SQLFreeHandle(SQL_HANDLE_DBC,hdbcl);
	SQLFreeHandle(SQL_HANDLE_ENV,henv);
	return 0;
}
```

