# utl-select-rows-where-the-sum-of-columns-equals-one-aql-sas-r-python-multi-language
    %let pgm=utl-select-rows-where-the-sum-of-columns-equals-one-aql-sas-r-python-multi-language;

    Select rows where the sum of columns equals one aql sas r python multi language

    https://stackoverflow.com/questions/79177163/dplyrselect-or-dplyrfilter-if-all-values-are-true

    True is equivalent to 1 and false to 0

      SOLUTIONS
          1 sas sql varlist
          2 r sql varlist
          3 r sql no varlist
          4 python varlist
          5 r tidyverse language (== filter pick starts_with)
            (solution not provided - shoud be better base r solutions)
            filter(rowSums(!pick(starts_with("Exp"))) == 1)

    sqllite should handle logicals like 0/1 in arithmatic functions?
    However sas does not support logical type (less is more in this case?)

     /*              _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /**************************************************************************************************************************/
    /*                                          |                                                      |                      */
    /*                                          |                                                      |                      */
    /*           INPUT                          |           PROCESS                                    |      OUTPUT          */
    /*           =====                          |      (SELF EXPLANATORY)                              |                      */
    /*          SD1.HAVE                        |                                                      |                      */
    /*                                          |                                                      |                      */
    /* Obs ID    E1 E2 E3 ... E34 E35 E36       | SAS R PYTHON (WITH MACRO VARLIST)                    |  WANT total obs=17   */
    /*                                          | =================================                    |                      */
    /*    1 g1     0  0  0 ...  0   0   0       |                                                      |               SUM_   */
    /*    2 g2     0  0  0 ...  0   0   0       | %let vars=%utl_varlist(sd1.have,keep=e:,od=%str(+)); |      ID      IS_ONE  */
    /*    3 g3     0  0  0 ...  0   0   0       |                                                      |                      */
    /* ...                                      | select                                               |     g89         1    */
    /* 1998 g1998  0  0  0 ...  0   0   0       |     id                                               |     g347        1    */
    /* 1999 g1999  0  0  0 ...  0   0   0       |    ,1 as sum_is_one                                  |     g458        1    */
    /* 2000 g2000  0  0  0 ...  0   0   0       | from                                                 |     g577        1    */
    /*                                          |    have                                              |     g802        1    */
    /* options validvarname=upcase;             | where                                                |     g824        1    */
    /* libname sd1 "d:/sd1";                    |    (&vars)=1                                         |     g827        1    */
    /* data sd1.have;                           |                                                      |     g907        1    */
    /*   length id $5;                          |  -------------------------------------------         |     g950        1    */
    /*   call streaminit(4821);                 |                                                      |     g1011       1    */
    /*   array es e1-e36;                       |  R WITHOUT VARLIST                                   |     g1036       1    */
    /*   do i=1 to 2000;                        |  ==================                                  |     g1147       1    */
    /*     id=cats('g',i);                      |                                                      |     g1258       1    */
    /*   do j=1 to 36;                          |  column_names <- names(have)[-1];                    |     g1302       1    */
    /*    if rand('uniform')<.9997 then es[j]=0;|  es <- paste(column_names, collapse = "+ ")          |     g1439       1    */
    /*    else es[j]=1;                         |                                                      |     g1491       1    */
    /*   end;                                   |  want <- sqldf(paste(                                |     g1947       1    */
    /*   output;                                |        "select                                       |                      */
    /*   end;                                   |            id                                        |                      */
    /*   keep id e:;                            |           ,1 as sum_is_one                           |                      */
    /* run;quit;                                |        from                                          |                      */
    /*                                          |            have                                      |                      */
    /*                                          |        where                                         |                      */
    /*                                          |           (",es,")=1."                               |                      */
    /*                                          |        ))                                            |                      */
    /*                                          |                                                      |                      */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      length id $5;
      call streaminit(4821);
      array es e1-e36;
      do i=1 to 2000;
        id=cats('g',i);
      do j=1 to 36;
        if rand('uniform')<.9997 then es[j]=0;
        else es[j]=1;
      end;
      output;
      end;
      keep id e:;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Obs ID    E1 E2 E3 ... E34 E35 E36                                                                                    */
    /*                                                                                                                        */
    /*     1 g1     1  0  1 ...  0   0   1                                                                                    */
    /*     2 g2     1  0  1 ...  1   0   1                                                                                    */
    /*     3 g3     1  0  0 ...  1   0   0                                                                                    */
    /*  ...                                                                                                                   */
    /*  1998 g1998  1  0  0 ...  0   1   1                                                                                    */
    /*  1999 g1999  1  0  1 ...  0   0   1                                                                                    */
    /*  2000 g2000  1  1  0 ...  0   0   1                                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                             _                   _ _     _
    / |  ___  __ _ ___   ___  __ _| | __   ____ _ _ __| (_)___| |_
    | | / __|/ _` / __| / __|/ _` | | \ \ / / _` | `__| | / __| __|
    | | \__ \ (_| \__ \ \__ \ (_| | |  \ V / (_| | |  | | \__ \ |_
    |_| |___/\__,_|___/ |___/\__, |_|   \_/ \__,_|_|  |_|_|___/\__|
                                |_|
    */

    /*--- what varlist does ---*/
    %put %utl_varlist(sd1.have,keep=e:,od=%str(+));

    %let vars=%utl_varlist(sd1.have,keep=e:,od=%str(+));
    /*--- VARS=E1+E2+E3+...+E34+E35+E36 ---*/

    proc sql;
       create
          table want as
       select
           id
          ,1 as sum_is_one
       from
          sd1.have
       where
          %utl _varlist(
            sd1.have
           ,keep=e:
           ,od=%str(+)) = 1

    ;quit;

    proc print data=want;
    run;quit;

    proc print data=sd1.have(where=(id='g89'));
    run;quit;



    /*-- 0.97 seconds --*/

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WANT total obs=17                                                                                                      */
    /*                                                                                                                        */
    /*              SUM_                                                                                                      */
    /*     ID      IS_ONE                                                                                                     */
    /*                                                                                                                        */
    /*    g89         1                                                                                                       */
    /*    g347        1                                                                                                       */
    /*    g458        1                                                                                                       */
    /*    g577        1                                                                                                       */
    /*    g802        1                                                                                                       */
    /*    g824        1                                                                                                       */
    /*    g827        1                                                                                                       */
    /*    g907        1                                                                                                       */
    /*    g950        1                                                                                                       */
    /*    g1011       1                                                                                                       */
    /*    g1036       1                                                                                                       */
    /*    g1147       1                                                                                                       */
    /*    g1258       1                                                                                                       */
    /*    g1302       1                                                                                                       */
    /*    g1439       1                                                                                                       */
    /*    g1491       1                                                                                                       */
    /*    g1947       1                                                                                                       */
    /*                                                                                                                        */
    /*  CHECK                                                                                                                 */
    /*                                                                                                                        */
    /*  ID  1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36                */
    /*                                          ---                                                                           */
    /*  g89 0  0  0  0  0  0  0   0  0  0  0  0  1  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0                */
    /*                                          ---                                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _                   _ _     _
    |___ \   _ __   ___  __ _| | __   ____ _ _ __| (_)___| |_
      __) | | `__| / __|/ _` | | \ \ / / _` | `__| | / __| __|
     / __/  | |    \__ \ (_| | |  \ V / (_| | |  | | \__ \ |_
    |_____| |_|    |___/\__, |_|   \_/ \__,_|_|  |_|_|___/\__|
                           |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete rwant;
    run;quit;

    %let vars=%utl_varlist(sd1.have,keep=e:,od=%str(+));

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    want<-sqldf("
       select
           id
          ,1 as sum_is_one
       from
          have
       where
          (&vars)=1
       ")
    print(want)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rwant"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.rwant;
    run;quit;

    /**************************************************************************************************************************/
    /*                       |                                                                                                */
    /* R                     |  SAS                                                                                           */
    /*                       |                        SUM_                                                                    */
    /*        ID sum_is_one  |  ROWNAMES     ID      IS_ONE                                                                   */
    /*                       |                                                                                                */
    /*  1    g89          1  |      1       g89         1                                                                     */
    /*  2   g347          1  |      2       g347        1                                                                     */
    /*  3   g458          1  |      3       g458        1                                                                     */
    /*  4   g577          1  |      4       g577        1                                                                     */
    /*  5   g802          1  |      5       g802        1                                                                     */
    /*  6   g824          1  |      6       g824        1                                                                     */
    /*  7   g827          1  |      7       g827        1                                                                     */
    /*  8   g907          1  |      8       g907        1                                                                     */
    /*  9   g950          1  |      9       g950        1                                                                     */
    /*  10 g1011          1  |     10       g1011       1                                                                     */
    /*  11 g1036          1  |     11       g1036       1                                                                     */
    /*  12 g1147          1  |     12       g1147       1                                                                     */
    /*  13 g1258          1  |     13       g1258       1                                                                     */
    /*  14 g1302          1  |     14       g1302       1                                                                     */
    /*  15 g1439          1  |     15       g1439       1                                                                     */
    /*  16 g1491          1  |     16       g1491       1                                                                     */
    /*  17 g1947          1  |     17       g1947       1                                                                     */
    /*                       |                                                                                                */
    /**************************************************************************************************************************/

    /*____                    _                                 _ _     _
    |___ /   _ __   ___  __ _| |  _ __   ___   __   ____ _ _ __| (_)___| |_
      |_ \  | `__| / __|/ _` | | | `_ \ / _ \  \ \ / / _` | `__| | / __| __|
     ___) | | |    \__ \ (_| | | | | | | (_) |  \ V / (_| | |  | | \__ \ |_
    |____/  |_|    |___/\__, |_| |_| |_|\___/    \_/ \__,_|_|  |_|_|___/\__|
                           |_|
    */

    /*--- create the varlist within base r language ---*/

    %let vars=%utl_varlist(sd1.have,keep=e:,od=%str(+));

    proc

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    have<-read_sas("d:/sd1/have.sas7bdat")
    column_names <- names(have)[-1];
    es <- paste(column_names, collapse = "+ ")
    want <- sqldf(paste(
          "select
              id
             ,1 as sum_is_one
          from
              have
          where
             (",es,")=1."
          ))
    print(want)
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="rrwant"
         )
    head(want)
    ;;;;
    %utl_rendx;

    proc print data=sd1.rwant;
    run;quit;

    /**************************************************************************************************************************/
    /*                       |                                                                                                */
    /* R                     |  SAS                                                                                           */
    /*                       |                        SUM_                                                                    */
    /*        ID sum_is_one  |  ROWNAMES     ID      IS_ONE                                                                   */
    /*                       |                                                                                                */
    /*  1    g89          1  |      1       g89         1                                                                     */
    /*  2   g347          1  |      2       g347        1                                                                     */
    /*  3   g458          1  |      3       g458        1                                                                     */
    /*  4   g577          1  |      4       g577        1                                                                     */
    /*  5   g802          1  |      5       g802        1                                                                     */
    /*  6   g824          1  |      6       g824        1                                                                     */
    /*  7   g827          1  |      7       g827        1                                                                     */
    /*  8   g907          1  |      8       g907        1                                                                     */
    /*  9   g950          1  |      9       g950        1                                                                     */
    /*  10 g1011          1  |     10       g1011       1                                                                     */
    /*  11 g1036          1  |     11       g1036       1                                                                     */
    /*  12 g1147          1  |     12       g1147       1                                                                     */
    /*  13 g1258          1  |     13       g1258       1                                                                     */
    /*  14 g1302          1  |     14       g1302       1                                                                     */
    /*  15 g1439          1  |     15       g1439       1                                                                     */
    /*  16 g1491          1  |     16       g1491       1                                                                     */
    /*  17 g1947          1  |     17       g1947       1                                                                     */
    /*                       |                                                                                                */
    /**************************************************************************************************************************/


    /*  _                _   _                                   _ _     _
    | || |   _ __  _   _| |_| |__   ___  _ __   __   ____ _ _ __| (_)___| |_
    | || |_ | `_ \| | | | __| `_ \ / _ \| `_ \  \ \ / / _` | `__| | / __| __|
    |__   _|| |_) | |_| | |_| | | | (_) | | | |  \ V / (_| | |  | | \__ \ |_
       |_|  | .__/ \__, |\__|_| |_|\___/|_| |_|   \_/ \__,_|_|  |_|_|___/\__|
            |_|    |___/
    */


    proc datasets lib=sd1 nolist nodetails;
     delete pywant;
    run;quit;

    %utl_pybeginx;
    parmcards4;
    exec(open('c:/oto/fn_python.py').read());
    have,meta = ps.read_sas7bdat('d:/sd1/have.sas7bdat');
    want=pdsql('''          \
       select               \
           id               \
          ,1 as sum_is_one  \
       from                 \
          have              \
       where                \
          (&vars)=1         \
       ''');
    print(want);
    fn_tosas9x(want,outlib='d:/sd1/',outdsn='pywant',timeest=3);
    ;;;;
    %utl_pyendx(resolve=Y);

    proc print data=sd1.pywant;
    run;quit;

    /**************************************************************************************************************************/
    /*                         |                                                                                              */
    /* PYTHON                  | R                                                                                            */
    /*                         |            SUM_                                                                              */
    /*         ID  sum_is_one  |   ID      IS_ONE                                                                             */
    /*                         |                                                                                              */
    /*  0     g89           1  |  g89         1                                                                               */
    /*  1    g347           1  |  g347        1                                                                               */
    /*  2    g458           1  |  g458        1                                                                               */
    /*  3    g577           1  |  g577        1                                                                               */
    /*  4    g802           1  |  g802        1                                                                               */
    /*  5    g824           1  |  g824        1                                                                               */
    /*  6    g827           1  |  g827        1                                                                               */
    /*  7    g907           1  |  g907        1                                                                               */
    /*  8    g950           1  |  g950        1                                                                               */
    /*  9   g1011           1  |  g1011       1                                                                               */
    /*  10  g1036           1  |  g1036       1                                                                               */
    /*  11  g1147           1  |  g1147       1                                                                               */
    /*  12  g1258           1  |  g1258       1                                                                               */
    /*  13  g1302           1  |  g1302       1                                                                               */
    /*  14  g1439           1  |  g1439       1                                                                               */
    /*  15  g1491           1  |  g1491       1                                                                               */
    /*  16  g1947           1  |  g1947       1                                                                               */
    /*                         |                                                                                              */
    /*                         |                                                                                              */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
Select rows where the sum of columns equals one aql sas r python multi language
