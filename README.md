# utl-example-of-sqlite-group_concat-and-associated-sas-datastep-solution
Example of sqlite group_concat and associated sas datastep solution
    %let pgm=utl-example-of-sqlite-group_concat-and-associated-sas-datastep-solution;

    Example of sqlite group_concat and associated sas datastep solution

        TWO SOLUTIONS
              1 r sql
                for excel and python see
                https://tinyurl.com/4e6yaap8
              2 sas datastep
                https://communities.sas.com/t5/user/viewprofilepage/user-id/6401

    github
    https://tinyurl.com/37kr8jcn
    https://github.com/rogerjdeangelis/utl-example-of-sqlite-group_concat-and-associated-sas-datastep-solution

    communities.sas
    https://tinyurl.com/4spsezf5
    https://communities.sas.com/t5/SAS-Programming/Adding-multiple-rows-of-data-and-display-in-one-row-based-on-ID/m-p/816423#M322246


    /**************************************************************************************************************************/
    /*    INPUT                      |  PROCESS                                           |     OUTPUT                        */
    /*    =====                      |                                                    |                                   */
    /*                               |                                                    |                                   */
    /*    ID     DESC                | 1 R SQL                                            |  R WANT                           */
    /*                               | =======                                            |                                   */
    /*   1234     AB                 |                                                    |      ID   lst                     */
    /*   5678     CD                 | proc datasets lib=sd1 nolist nodetails;            |  1 1234    AB                     */
    /*   5678     EF                 |  delete want;                                      |  2 5678 CD-EF                     */
    /*   9999     GH                 | run;quit;                                          |  3 9999    GH                     */
    /*                               |                                                    |                                   */
    /*  options                      | %utl_rbeginx;                                      |                                   */
    /*  validvarname=upcase;         | parmcards4;                                        |  SAS                              */
    /*  libname sd1 "d:/sd1";        | library(haven)                                     |                                   */
    /*  data sd1.have;               | library(sqldf)                                     |  ROWNAMES  ID  LST                */
    /*  input ID DESC $2.;           | source("c:/oto/fn_tosas9x.R")                      |                                   */
    /*  cards4;                      | options(sqldf.dll = "d:/dll/sqlean.dll")           |      1    1234 AB                 */
    /*  1234 AB                      | have<-read_sas("d:/sd1/have.sas7bdat")             |      2    5678 CD-EF              */
    /*  5678 CD                      | print(have)                                        |      3    9999 GH                 */
    /*  5678 EF                      | want<-sqldf('                                      |                                   */
    /*  9999 GH                      |   select                                           |                                   */
    /*  ;;;;                         |     id                                             |                                   */
    /*  run;quit;                    |    ,group_concat(desc,"-") as lst                  |                                   */
    /*                               |   from                                             |                                   */
    /*                               |     have                                           |                                   */
    /*                               |   group                                            |                                   */
    /*                               |     by id                                          |                                   */
    /*                               | ')                                                 |                                   */
    /*                               | want                                               |                                   */
    /*                               | fn_tosas9x(                                        |                                   */
    /*                               |       inp    = want                                |                                   */
    /*                               |      ,outlib ="d:/sd1/"                            |                                   */
    /*                               |      ,outdsn ="want"                               |                                   */
    /*                               |      )                                             |                                   */
    /*                               | ;;;;                                               |                                   */
    /*                               | %utl_rendx;                                        |                                   */
    /*                               |                                                    |                                   */
    /*                               | proc print data=sd1.want;                          |                                   */
    /*                               | run;quit;                                          |                                   */
    /*                               |                                                    |                                   */
    /*                               |----------------------------------------------------------------------------------------*/
    /*                               |                                                    |                                   */
    /*                               | 2 SAS DATASTEP                                     |   ID     LIST                     */
    /*                               | ==============                                     |                                   */
    /*                               |                                                    |  1234    AB                       */
    /*                               | data want(keep=id list);                           |  5678    CD_EF                    */
    /*                               |   do until (last.id);                              |  9999    GH                       */
    /*                               |     set sd1.have;                                  |                                   */
    /*                               |     by id;                                         |                                   */
    /*                               |     length list $30;                               |                                   */
    /*                               |     list = catx('_',list,desc);                    |                                   */
    /*                               |   end;                                             |                                   */
    /*                               | run;quit;                                          |                                   */
    /*                               |                                                    |                                   */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options
    validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input ID DESC $2.;
    cards4;
    1234 AB
    5678 CD
    5678 EF
    9999 GH
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*    ID     DESC                                                                                                         */
    /*                                                                                                                        */
    /*   1234     AB                                                                                                          */
    /*   5678     CD                                                                                                          */
    /*   5678     EF                                                                                                          */
    /*   9999     GH                                                                                                          */
    /**************************************************************************************************************************/

    /*                    _
    / |  _ __   ___  __ _| |
    | | | `__| / __|/ _` | |
    | | | |    \__ \ (_| | |
    |_| |_|    |___/\__, |_|
                       |_|
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    %utl_rbeginx;
    parmcards4;
    library(haven)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    options(sqldf.dll = "d:/dll/sqlean.dll")
    have<-read_sas("d:/sd1/have.sas7bdat")
    print(have)
    want<-sqldf('
      select
        id
       ,group_concat(desc,"-") as lst
      from
        have
      group
        by id
    ')
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    ;;;;
    %utl_rendx;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /* R WANT          | SAS                                                                                                  */
    /*     ID   lst    | ROWNAMES  ID  LST                                                                                    */
    /*                 |                                                                                                      */
    /* 1 1234    AB    |     1    1234 AB                                                                                     */
    /* 2 5678 CD-EF    |     2    5678 CD-EF                                                                                  */
    /* 3 9999    GH    |     3    9999 GH                                                                                     */
    /**************************************************************************************************************************/

    /*___                        _       _            _
    |___ \   ___  __ _ ___    __| | __ _| |_ __ _ ___| |_ ___ _ __
      __) | / __|/ _` / __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \
     / __/  \__ \ (_| \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) |
    |_____| |___/\__,_|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/
                                                             |_|
    */

    data want(keep=id list);
      do until (last.id);
        set sd1.have;
        by id;
        length list $30;
        list = catx('_',list,desc);
      end;
    run;quit;

    /**************************************************************************************************************************/
    /*   ID     LIST                                                                                                          */
    /*                                                                                                                        */
    /*  1234    AB                                                                                                            */
    /*  5678    CD_EF                                                                                                         */
    /*  9999    GH                                                                                                            */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
