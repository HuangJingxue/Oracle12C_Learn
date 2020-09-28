```sql
SCOTT@testdb>conn / as sysdba
Connected.
SYS@testdb>create table t as select * from dba_indexes;

Table created.

SYS@testdb>select index_type,status,count(*) from t group by index_type,status;

INDEX_TYPE		    STATUS     COUNT(*)
--------------------------- -------- ----------
NORMAL			    VALID	   3135
LOB			    N/A 	      1
NORMAL			    N/A 	     78
FUNCTION-BASED NORMAL	    VALID	     36
FUNCTION-BASED DOMAIN	    VALID	      4
LOB			    VALID	    843
BITMAP			    VALID	      1
IOT - TOP		    VALID	    102
CLUSTER 		    VALID	     10

9 rows selected.


SYS@testdb>select index_type,status,count(*) from t group by ROLLUP(index_type,status);

INDEX_TYPE		    STATUS     COUNT(*)
--------------------------- -------- ----------
LOB			    N/A 	      1
LOB			    VALID	    843
LOB					    844
BITMAP			    VALID	      1
BITMAP					      1
NORMAL			    N/A 	     78
NORMAL			    VALID	   3135
NORMAL					   3213
CLUSTER 		    VALID	     10
CLUSTER 				     10
IOT - TOP		    VALID	    102
IOT - TOP				    102
FUNCTION-BASED DOMAIN	    VALID	      4
FUNCTION-BASED DOMAIN			      4
FUNCTION-BASED NORMAL	    VALID	     36
FUNCTION-BASED NORMAL			     36
					   4210

17 rows selected.





SYS@testdb>select index_type,status,count(*) from t group by index_type,status;

INDEX_TYPE		    STATUS     COUNT(*)
--------------------------- -------- ----------
NORMAL			    VALID	   3135
LOB			    N/A 	      1
NORMAL			    N/A 	     78
FUNCTION-BASED NORMAL	    VALID	     36
FUNCTION-BASED DOMAIN	    VALID	      4
LOB			    VALID	    843
BITMAP			    VALID	      1
IOT - TOP		    VALID	    102
CLUSTER 		    VALID	     10

9 rows selected.

SYS@testdb>select index_type,status,count(*) from t group by cube(index_type,status);

INDEX_TYPE		    STATUS     COUNT(*)
--------------------------- -------- ----------
					   4210
			    N/A 	     79
			    VALID	   4131
LOB					    844
LOB			    N/A 	      1
LOB			    VALID	    843
BITMAP					      1
BITMAP			    VALID	      1
NORMAL					   3213
NORMAL			    N/A 	     78
NORMAL			    VALID	   3135
CLUSTER 				     10
CLUSTER 		    VALID	     10
IOT - TOP				    102
IOT - TOP		    VALID	    102
FUNCTION-BASED DOMAIN			      4
FUNCTION-BASED DOMAIN	    VALID	      4
FUNCTION-BASED NORMAL			     36
FUNCTION-BASED NORMAL	    VALID	     36

19 rows selected.

SYS@testdb>select grouping(index_type) g_ind, grouping(status) g_st, index_type, status, count(*) from t group by rollup(index_type, status) order by 1, 2;

     G_IND	 G_ST INDEX_TYPE		  STATUS     COUNT(*)
---------- ---------- --------------------------- -------- ----------
	 0	    0 LOB			  N/A		    1
	 0	    0 LOB			  VALID 	  843
	 0	    0 FUNCTION-BASED NORMAL	  VALID 	   36
	 0	    0 FUNCTION-BASED DOMAIN	  VALID 	    4
	 0	    0 IOT - TOP 		  VALID 	  102
	 0	    0 CLUSTER			  VALID 	   10
	 0	    0 NORMAL			  VALID 	 3135
	 0	    0 NORMAL			  N/A		   78
	 0	    0 BITMAP			  VALID 	    1
	 0	    1 FUNCTION-BASED NORMAL			   36
	 0	    1 NORMAL					 3213
	 0	    1 IOT - TOP 				  102
	 0	    1 FUNCTION-BASED DOMAIN			    4
	 0	    1 BITMAP					    1
	 0	    1 CLUSTER					   10
	 0	    1 LOB					  844
	 1	    1						 4210

17 rows selected.

SYS@testdb>select grouping(index_type) g_ind, grouping(status) g_st, index_type, status, count(*) from t group by cube(index_type, status) order by 1, 2;

     G_IND	 G_ST INDEX_TYPE		  STATUS     COUNT(*)
---------- ---------- --------------------------- -------- ----------
	 0	    0 LOB			  N/A		    1
	 0	    0 CLUSTER			  VALID 	   10
	 0	    0 FUNCTION-BASED NORMAL	  VALID 	   36
	 0	    0 NORMAL			  VALID 	 3135
	 0	    0 IOT - TOP 		  VALID 	  102
	 0	    0 LOB			  VALID 	  843
	 0	    0 FUNCTION-BASED DOMAIN	  VALID 	    4
	 0	    0 BITMAP			  VALID 	    1
	 0	    0 NORMAL			  N/A		   78
	 0	    1 IOT - TOP 				  102
	 0	    1 CLUSTER					   10
	 0	    1 NORMAL					 3213
	 0	    1 BITMAP					    1
	 0	    1 LOB					  844
	 0	    1 FUNCTION-BASED NORMAL			   36
	 0	    1 FUNCTION-BASED DOMAIN			    4
	 1	    0				  N/A		   79
	 1	    0				  VALID 	 4131
	 1	    1						 4210


```
CUBE和ROLLUP相比，CUBE又增加了对STATUS列的GROUP BY统计。
GROUP BY ROLLUP(A, B, C)的话，首先会对(A、B、C)进行GROUP BY，然后对(A、B)进行GROUP BY，然后是(A)进行GROUP BY，最后对全表进行GROUP BY操作。
GROUP BY CUBE(A, B, C)，则首先会对(A、B、C)进行GROUP BY，然后依次是(A、B)，(A、C)，(A)，(B、C)，(B)，(C)，最后对全表进行GROUP BY操作。 
grouping_id()可以美化效果。
