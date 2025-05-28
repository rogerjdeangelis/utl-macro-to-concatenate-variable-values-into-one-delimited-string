# utl-macro-to-concatenate-variable-values-into-one-delimited-string
Macro to concatenate variable values into one delimited string
    %let pgm=utl-macro-to-concatinate-variable-values-into-one-delimited-string;

    %inc "c:/oto/sas_saspac.sas";

    %stop_submission;

    Macro to concatinate variable values into one delimited string

    This post provides a way to to leverage dosubl to eliminate macro klingon code.

           CONTENTS

              1 Example
              2 Documentation
              3 Macro  (on end)

    github
    https://tinyurl.com/23frr627
    https://github.com/rogerjdeangelis/utl-macro-to-concatenate-variable-values-into-one-delimited-string


    /**************************************************************************************************************************/
    /*                      |                                       |                                                         */
    /* INPUT                |  PROCESS                              |      OUTPUT                                             */
    /* =====                |  =======                              |      ======                                             */
    /*                      |                                       |                                                         */
    /* META DATA OF         | 1 EXAMPLE                             | UNKNOWN IS EXCLUDED FROM SUM  prx="/^(?!un).*/"         */
    /* VARIABLE NAMES       | =========                             |                                                         */
    /*                      |                                       |                      SUM                                */
    /* SD1.META             | Sum only males and females            |                      Males+                             */
    /*                      |                                       |                      Females                            */
    /* NAMES                | Apply a filter that removes           | FEMALE MALE UNKNOWN  M_F                                */
    /*                      | unknown sex from total                |                                                         */
    /* male                 | Also use only unique names in         |     6     5     1     11                                */
    /* female               | meta table                            |     7     4     0     11                                */
    /* unknown              | Do no use names that begin            |     5     8     1     13                                */
    /* male                 | with un.                              |                                                         */
    /*                      |                                       |                                                         */
    /* SD1.MASTER           | data want;                            | From macro                                              */
    /*                      |    array val[*]                       |                                                         */
    /* MALE UNKNOWN FEMALE  |      %utl_concat(                     | %put &=_vals;                                           */
    /*                      |         meta                          |                                                         */
    /*   5     1       6    |        ,unique=Y  /*--- 1 male ---*/  | _VALS=female male                                       */
    /*   4     0       7    |        ,var=names                     |                                                         */
    /*   8     1       5    |        ,prx="/^(?!un).*/") ;          |                                                         */
    /*                      |    set master;                        |                                                         */
    /* data meta;           |    m_f=sum(of val[*]) ;               |                                                         */
    /*  input names$;       | run;quit;                             |                                                         */
    /* cards4;              |                                       |                                                         */
    /* male                 | %put &=_vals;                         |                                                         */
    /* female               |                                       |                                                         */
    /* unknown              | _VALS=female male                     |                                                         */
    /* male                 |                                       |                                                         */
    /* ;;;;                 |                                       |                                                         */
    /* run;quit;            |                                       |                                                         */
    /*                      |                                       |                                                         */
    /* data master;         |                                       |                                                         */
    /*   input              |                                       |                                                         */
    /*    male unknown      |                                       |                                                         */
    /*    female;           |                                       |                                                         */
    /* cards4;              |                                       |                                                         */
    /* 5 1 6                |                                       |                                                         */
    /* 4 0 7                |                                       |                                                         */
    /* 8 1 5                |                                       |                                                         */
    /* ;;;;                 |                                       |                                                         */
    /* run;quit;            |                                       |                                                         */
    /*                      |                                       |                                                         */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                                                                                        */
    /* 2 DOCUMENTATION                                                                                                        */
    /* ===============                                                                                                        */
    /*                                                                                                                        */
    /* Name: concat.sas                                                                                                       */
    /* Type: Macro function                                                                                                   */
    /* Description: Returns macro variable _vals                                                                              */
    /* Parameters:                                                                                                            */
    /*         Data       - Name of table <libname.>memname                                                                   */
    /*         VAR        - Variable values to cocatenate                                                                     */
    /*         UNIQUE     - N,Y  Unique vales of variable                                                                     */
    /*         Qstyle=    - Quote style:                                                                                      */
    /*                        DOUBLE is like "Name" "Sex" "Weight"...                                                         */
    /*                        SAS is like 'Name' 'Sex' 'Weight'...                                                            */
    /*         Od         - %str( ) - Output delimiter                                                                        */
    /*         PRX=      -  PRX expression                                                                                    */
    /*                                                                                                                        */
    /* data class;                                                                                                            */
    /*  set sashelp.class(obs=5                                                                                               */
    /*   keep=name sex);                                                                                                      */
    /*  if _n_=3 then sex='U';                                                                                                */
    /* run;quit;                                                                                                              */
    /*                                                                                                                        */
    /* *---- WITH QUOTES ----*;                                                                                               */
    /* %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str(,),prx="/^F/");   ;  "F","F"   * comma delimites              */
    /* %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str( ),prx="/^F/");   ;  "F" "F"   * space delimited              */
    /* %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str(,));              ;  "M","F","U","F","M"                      */
    /* %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str( ));              ;  "M" "F" "U" "F" "M"                      */
    /* %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str(,),prx="/^F/");      ;  'F','F'                                  */
    /* %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str( ),prx="/^F/");      ;  'F' 'F'                                  */
    /* %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str(,));                 ;  'M','F','U','F','M'                      */
    /* %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str( ));                 ;  'M' 'F' 'U' 'F' 'M'                      */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,),prx="/^F|U/"); ;  "F","U"                                  */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,),prx="/^F|U/"); ;  "F","U"                                  */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,));              ;  "F","M","U"                              */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str( ));              ;  "F" "M" "U"                              */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str(,),prx="/^F|U/");    ;  'F','U'                                  */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str( ),prx="/^F|U/");    ;  'F' 'U'                                  */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str(,));                 ;  'F','M','U'                              */
    /* %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str( ));                 ;  'F' 'M' 'U'                              */
    /*                                                                            ;                                           */
    /* *---- WITHOUT QUOTES ----*;                                                                                            */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str(,),prx="/^F/");            ;   F,F                                     */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str( ),prx="/^F/");            ;   F F                                     */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str(,),return=vals);           ;   M,F,U,F,M                               */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str( ));                       ;   M F U F M                               */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str(,),prx="/^F/");            ;   F,F                                     */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str(),prx="/^F/");             ;   FF                                      */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str(,));                       ;   M,F,U,F,M                               */
    /* %put %utl_concat(class,var=sex,unique=N,od=%str( ));                       ;   M F U F M                               */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                       */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                       */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str(,));                       ;   F,M,U                                   */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str( ));                       ;   F M U                                   */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                       */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str( ),prx="/^F/");            ;   F                                       */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str(,));                       ;   F,M,U                                   */
    /* %put %utl_concat(class,var=sex,unique=Y,od=%str( ));                       ;   F M U                                   */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    data meta;
     input names$;
    cards4;
    male
    female
    unknown
    male
    ;;;;
    run;quit;

    data master;
      input
       male unknown
       female;
    cards4;
    5 1 6
    4 0 7
    8 1 5
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*    META DATA OF                                                                                                        */
    /*    VARIABLE NAMES                                                                                                      */
    /*                                                                                                                        */
    /*    SD1.META                                                                                                            */
    /*    ========                                                                                                            */
    /*    NAMES                                                                                                               */
    /*                                                                                                                        */
    /*    male                                                                                                                */
    /*    female                                                                                                              */
    /*    unknown                                                                                                             */
    /*    male                                                                                                                */
    /*                                                                                                                        */
    /*    SD1.MASTER                                                                                                          */
    /*    ==========                                                                                                          */
    /*    MALE UNKNOWN FEMALE                                                                                                 */
    /*                                                                                                                        */
    /*      5     1       6                                                                                                   */
    /*      4     0       7                                                                                                   */
    /*      8     1       5                                                                                                   */
    /**************************************************************************************************************************/

    /*                                  _
    / |   _____  ____ _ _ __ ___  _ __ | | ___
    | |  / _ \ \/ / _` | `_ ` _ \| `_ \| |/ _ \
    | | |  __/>  < (_| | | | | | | |_) | |  __/
    |_|  \___/_/\_\__,_|_| |_| |_| .__/|_|\___|
                                 |_|
    */

    data want;
       array val[*]
         %utl_concat(
            meta
           ,unique=Y  /*--- 1 male ---*/
           ,var=names
           ,prx="/^(?!un).*/") ;
       set master;
       m_f=sum(of val[*]) ;
    run;quit;

    /**************************************************************************************************************************/
    /*                                     Added                                                                              */
    /*                                     Sum                                                                                */
    /*                                     ====                                                                               */
    /* Obs    FEMALE    MALE    UNKNOWN    M_F    Male + Female                                                               */
    /*                                                                                                                        */
    /*  1        6        5        1        11                                                                                */
    /*  2        7        4        0        11                                                                                */
    /*  3        5        8        1        13                                                                                */
    /**************************************************************************************************************************/

    /*____
    |___ /   _ __ ___   __ _  ___ _ __ ___
      |_ \  | `_ ` _ \ / _` |/ __| `__/ _ \
     ___) | | | | | | | (_| | (__| | | (_) |
    |____/  |_| |_| |_|\__,_|\___|_|  \___/

    */

    filename ft15f001 "c:/oto/utl_concat.sas";
    parmcards4;
    /**************************************************************************************************************************/
    /*  Name: utl_concat.sas                                                                                                  */
    /*  Type: Macro function                                                                                                  */
    /*  Description: Returns macro variable _vals                                                                             */
    /*  Parameters:                                                                                                           */
    /*          Data       - Name of table <libname.>memname                                                                  */
    /*          VAR        - Variable values to cocatenate                                                                    */
    /*          UNIQUE     - N,Y  Unique vales of variable                                                                    */
    /*          Qstyle=    - Quote style:                                                                                     */
    /*                         DOUBLE is like "Name" "Sex" "Weight"...                                                        */
    /*                         SAS is like 'Name' 'Sex' 'Weight'...                                                           */
    /*          Od         - %str( ) - Output delimiter                                                                       */
    /*          PRX=      -  PRX expression                                                                                   */
    /*                                                                                                                        */
    /*  EXAMPLES                                                                                                              */
    /*                                                                                                                        */
    /*  data class;                                                                                                           */
    /*   set sashelp.class(obs=5                                                                                              */
    /*    keep=name sex);                                                                                                     */
    /*   if _n_=3 then sex='U';                                                                                               */
    /*  run;quit;                                                                                                             */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  *---- WITH QUOTES ----*;                                                                                              */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str(,),prx="/^F/");   ;  "F","F"   * comma delimites             */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str( ),prx="/^F/");   ;  "F" "F"   * space delimited             */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str(,));              ;  "M","F","U","F","M"                     */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=DOUBLE,od=%str( ));              ;  "M" "F" "U" "F" "M"                     */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str(,),prx="/^F/");      ;  'F','F'                                 */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str( ),prx="/^F/");      ;  'F' 'F'                                 */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str(,));                 ;  'M','F','U','F','M'                     */
    /*  %utl_concat(class,var=sex,unique=N,qstyle=SAS,od=%str( ));                 ;  'M' 'F' 'U' 'F' 'M'                     */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,),prx="/^F|U/"); ;  "F","U"                                 */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,),prx="/^F|U/"); ;  "F","U"                                 */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str(,));              ;  "F","M","U"                             */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=DOUBLE,od=%str( ));              ;  "F" "M" "U"                             */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str(,),prx="/^F|U/");    ;  'F','U'                                 */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str( ),prx="/^F|U/");    ;  'F' 'U'                                 */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str(,));                 ;  'F','M','U'                             */
    /*  %utl_concat(class,var=sex,unique=Y,qstyle=SAS,od=%str( ));                 ;  'F' 'M' 'U'                             */
    /*                                                                             ;                                          */
    /*  *---- WITHOUT QUOTES ----*;                                                                                           */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str(,),prx="/^F/");            ;   F,F                                    */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str( ),prx="/^F/");            ;   F F                                    */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str(,),return=vals);           ;   M,F,U,F,M                              */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str( ));                       ;   M F U F M                              */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str(,),prx="/^F/");            ;   F,F                                    */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str(),prx="/^F/");             ;   FF                                     */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str(,));                       ;   M,F,U,F,M                              */
    /*  %put %utl_concat(class,var=sex,unique=N,od=%str( ));                       ;   M F U F M                              */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                      */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                      */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str(,));                       ;   F,M,U                                  */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str( ));                       ;   F M U                                  */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str(,),prx="/^F/");            ;   F                                      */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str( ),prx="/^F/");            ;   F                                      */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str(,));                       ;   F,M,U                                  */
    /*  %put %utl_concat(class,var=sex,unique=Y,od=%str( ));                       ;   F M U                                  */
    /*                                                                                                                        */
    /*  CHANGE LOG                                                                                                            */
    /*                                                                                                                        */
    /*  1. Added code to handle numeric second argument to prxmatck   2025-05-28 RJD                                          */
    /*     prxmatch('\.\',var) failed when var was numeric                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    %macro utl_concat(data,var=name,unique=N,qstyle=,od=%str( ),prx=%str('/./'));

      %symdel _vals / nowarn;
      %global _vals ;
      %local  varx typ dsid posv rc;

      /*----
        %let data=dupgroups;
        %let var=age;
      ----*/

       %let dsid = %sysfunc(open(&data,i));
       %let posv = %sysfunc(varnum(&dsid,&var));
       %let typ=   %sysfunc(vartype(&dsid,&posv));
       %let rc =   %sysfunc(close(&dsid));

       %put &=typ;

       %if &typ=N %then %do;
          %let varx='N';
       %end;
       %else %do;
          %let varx=&var;
       %end;

       %put &=varx;

      %if &unique=N & &qstyle=DOUBLE %then %do;
         %dosubl(%nrstr(
           proc sql noprint;
            select quote(strip(&var)) into: _vals separated by "&od" from &data where prxmatch(&prx,&varx)
           ;quit;
         ))
      %end;
      %else %if &unique=N & &qstyle=SAS %then %do;
         %dosubl(%nrstr(
          proc sql noprint;
            select quote(strip(&var)) into: _vals separated by " " from &data  where prxmatch(&prx,&varx)
           ;quit;
           ))
          %let _vals =%sysfunc(tranwrd(%str(&_vals),%str(%"),%str(%')));
          %let _vals =%sysfunc(tranwrd(%str(&_vals),%str( ),%str(&od)));
      %end;
      %else %if &unique=Y & &qstyle=DOUBLE %then %do;
         %dosubl(%nrstr(
           proc sql noprint;
            select distinct(quote(strip(&var))) into: _vals separated by "&od" from &data  where prxmatch(&prx,&varx)
           ;quit;
         ))
      %end;
      %else %if &unique=Y & &qstyle=SAS %then %do;
         %dosubl(%nrstr(
           proc sql noprint;
            select distinct(quote(strip(&var))) into: _vals separated by " " from &data  where prxmatch(&prx,&varx)
           ;quit;
         ))
          %let _vals =%sysfunc(tranwrd(%str(&_vals),%str(%"),%str(%')));
          %let _vals =%sysfunc(tranwrd(%str(&_vals),%str( ),%str(&od)));
      %end;
      %else %if &unique = N %then %do;
         %dosubl(%nrstr(
           proc sql noprint;
            select &var into: _vals separated by "&od" from &data  where prxmatch(&prx,&varx)
           ;quit;
         ))
      %end;
      %else %if %substr(%upcase(&unique),1,1) =Y %then %do;
         %dosubl(%nrstr(
           proc sql noprint;
            select distinct(&var) into: _vals separated by "&od" from &data  where prxmatch(&prx,&varx)
           ;quit;
         ))
      %end;
      &_vals
    %mend utl_concat;
    ;;;;
    run;quit;


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
