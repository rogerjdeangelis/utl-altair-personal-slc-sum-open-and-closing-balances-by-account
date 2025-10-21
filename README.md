# utl-altair-personal-slc-sum-open-and-closing-balances-by-account
Altair personal slc sum open and closing balances by account
    %let pgm=utl-altair-personal-slc-sum-open-and-closing-balances-by-account;

    %stop_submission;

    altair personal slc sum open and closing balances by account

    Too long to post in a listserv, see github

    github
    https://github.com/rogerjdeangelis/utl-altair-personal-slc-sum-open-and-closing-balances-by-account

    community.altair.com
    https://community.altair.com/discussion/5912/formula?tab=all#latest


    PROBLEM  select count in sum, sum of value & balance, opens and closes
                                                                           RECORD OF
                  INPUT                       OUTPUT                       OPEN & CLOSE
                                                                          ROWNUM  ROWNUM
    ROWNUM ACCOUNT    DATE    VALUE BALANCE   ACCOUNT COUNT VALSUM BALSUM BALOPN  BALCLS  OPEN CLOSE

       1     a     2025-10-15   10     10        a      1     10     10   0001 10 0001 10  10   10
       2     b     2025-10-15    5      5        b      2      0      5   0002 5  0003 0   5    0
       3     b     2025-10-15   -5      0        c      4     35     85   0004 10 0007 35  10   35
       4     c     2025-10-15   10     10        d      1     99     99   0008 99 0008 99  99   99
       5     c     2025-10-15   15     25
       6     c     2025-10-15  -10     15
       7     c     2025-10-15   20     35
       8     d     2025-10-15   99     99


    OUTOUT as EXCEL SHEET d:/xls/want.xlsx

    -------------------------+
    | A1| fx       | ACCOUNT |
    -------------------------------------------------------------------------------------+
    [_] |    A     |    B    |    C    |   D     |    E    |    F    |    G    |    I    |
    -------------------------------------------------------------------------------------|
        |          |         |         |         | ROWUMT  | ROWNUM  |         |         |
     1  | ACCOUBT  | COUNT   | VALSUM  | BALSUM  | BALOPN  | BALCLS  | OPEN    | CLOSE   |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     2  |  a       |  1      |  10     |  10     |  0001 10|  0001 10|  10     |  10     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     3  |  b       |  2      |  0      |  5      |  0002 5 |  0003 0 |  5      |  0      |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     4  |  c       |  4      |  35     |  85     |  0004 10|  0007 35|  10     |  35     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     5  |  d       |  1      |  99     |  99     |  0008 99|  0008 99|  99     |  99     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
    [WANT]

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data have;
     rownum=_n_;
     input account$ date $11. value balance;
    cards4;
    a 2025-10-15  10 10
    b 2025-10-15   5  5
    b 2025-10-15  -5  0
    c 2025-10-15  10 10
    c 2025-10-15  15 25
    c 2025-10-15 -10 15
    c 2025-10-15  20 35
    d 2025-10-15  99 99
    ;;;;
    run;quit;

    proc print data=have;
    run;quit;

    INPUT WORK.HAVE

    ROWNUM ACCOUNT    DATE    VALUE BALANCE

       1     a     2025-10-15   10     10
       2     b     2025-10-15    5      5
       3     b     2025-10-15   -5      0
       4     c     2025-10-15   10     10
       5     c     2025-10-15   15     25
       6     c     2025-10-15  -10     15
       7     c     2025-10-15   20     35
       8     d     2025-10-15   99     99

    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */

    5763
    5764      libname xls excel "d:/xls/want.xlsx";
    NOTE: Library xls assigned as follows:
          Engine:        OLEDB
          Physical Name: d:/xls/want.xlsx

    5765      proc sql;
    5766        create
    5767           table xls.want  as
    5768        select
    5769           account
    5770          ,count(*)      as count
    5771          ,sum(value)    as valSum
    5772          ,sum(balance)  as balSum
    5773          ,min(catx(' ',put(rownum,z4.),balance)) as rownum_balOpn length=10
    5774          ,max(catx(' ',put(rownum,z4.),balance)) as rownum_balCls length=10
    5775          ,scan(calculated rownum_balOpn,2) as open
    5776          ,scan(calculated rownum_balCls,2) as close
    5777        from
    5778          have
    5779        group
    5780          by account
    5781      ;quit;
    NOTE: Data set "XLS.want" has an unknown number of observation(s) and 8 variable(s)
    NOTE: Procedure sql step took :
          real time       : 0.725
          user cpu time   : 0.296
          system cpu time : 0.218
          Timestamp       :   20OCT25:17:17:56
          Peak working set    : 91176k
          Current working set : 52256k
          Page fault count    : 7020


    NOTE: Libref XLS has been deassigned.
    5782      libname xls clear;
    5783
    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    NOTE: Excel will convert text open close to numeric
    */

    %utlfkil(d:/xls/want.xlsx);

    libname xls excel "d:/xls/want.xlsx";
    proc sql;
      create
         table xls.want  as
      select
         account
        ,count(*)      as count
        ,sum(value)    as valSum
        ,sum(balance)  as balSum
        ,min(catx(' ',put(rownum,z4.),balance)) as rownum_balOpn length=10
        ,max(catx(' ',put(rownum,z4.),balance)) as rownum_balCls length=10
        ,scan(calculated rownum_balOpn,2) as open
        ,scan(calculated rownum_balCls,2) as close
      from
        have
      group
        by account
    ;quit;
    libname xls clear;


     OUTPUTS

                                ROWNUM  ROWNUM
    ACCOUNT COUNT VALSUM BALSUM BALOPN  BALCLS  OPEN CLOSE

       a      1     10     10   0001 10 0001 10  10   10
       b      2      0      5   0002 5  0003 0   5    0
       c      4     35     85   0004 10 0007 35  10   35
       d      1     99     99   0008 99 0008 99  99   99


    OUTBOOK WORKBOOK d:/xls/want.xlsx

    -------------------------+
    | A1| fx       | ACCOUNT |
    -------------------------------------------------------------------------------------+
    [_] |    A     |    B    |    C    |   D     |    E    |    F    |    G    |    I    |
    -------------------------------------------------------------------------------------|
        |          |         |         |         | ROWUMT  | ROWNUM  |         |         |
     1  | ACCOUBT  | COUNT   | VALSUM  | BALSUM  | BALOPN  | BALCLS  | OPEN    | CLOSE   |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     2  |  a       |  1      |  10     |  10     |  0001 10|  0001 10|  10     |  10     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     3  |  b       |  2      |  0      |  5      |  0002 5 |  0003 0 |  5      |  0      |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     4  |  c       |  4      |  35     |  85     |  0004 10|  0007 35|  10     |  35     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
     5  |  d       |  1      |  99     |  99     |  0008 99|  0008 99|  99     |  99     |
     -- |----------+---------+---------+---------+---------+---------+---------+---------|
    [WANT]


    /*
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    */
    5763
    5764      libname xls excel "d:/xls/want.xlsx";
    NOTE: Library xls assigned as follows:
          Engine:        OLEDB
          Physical Name: d:/xls/want.xlsx

    5765      proc sql;
    5766        create
    5767           table xls.want  as
    5768        select
    5769           account
    5770          ,count(*)      as count
    5771          ,sum(value)    as valSum
    5772          ,sum(balance)  as balSum
    5773          ,min(catx(' ',put(rownum,z4.),balance)) as rownum_balOpn length=10
    5774          ,max(catx(' ',put(rownum,z4.),balance)) as rownum_balCls length=10
    5775          ,scan(calculated rownum_balOpn,2) as open
    5776          ,scan(calculated rownum_balCls,2) as close
    5777        from
    5778          have
    5779        group
    5780          by account
    5781      ;quit;
    NOTE: Data set "XLS.want" has an unknown number of observation(s) and 8 variable(s)
    NOTE: Procedure sql step took :
          real time       : 0.725
          user cpu time   : 0.296
          system cpu time : 0.218
          Timestamp       :   20OCT25:17:17:56
          Peak working set    : 91176k
          Current working set : 52256k
          Page fault count    : 7020


    NOTE: Libref XLS has been deassigned.
    5782      libname xls clear;
    5783

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
