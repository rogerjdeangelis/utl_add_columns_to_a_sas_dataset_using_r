    # utl_add_columns_to_a_sas_dataset_using_r
    Add BMI to sashelp.class using R 
    %let pgm=utl_add_columns_to_a_sas_dataset_using_r ;
    
    GitHub
    https://github.com/rogerjdeangelis/utl_add_columns_to_a_sas_dataset_using_r

    Add BMI to sashelp.class using R

      Two Solutions

          1. Using dplyr and mutate
          2. Using sqldf

    SETUP FOR DULLES RESEARCH JDBC SAS DRIVE

    Add these lines to your RProfile, ./etc/Rprofile.site yext file

    # theis sets up c:/temp to hold sas datasets created by Dulles research JDBC sas driver;
    
    drv<- JDBC("com.dullesopen.jdbc.Driver","c:/carolina/carolina-jdbc-2.4.3.jar")
    
    conn <- dbConnect(drv, "jdbc:carolina:bulk:libnames=(dir=\'c:/temp\')", "", "")
    options(sqldf.driver='SQLite');

     _                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|

    options validvarname=upcase;

    libname temp "c:/temp";
    data temp.class;
      set sashelp.class(obs=5);
    run;quit;



    Up to 40 obs TEMP.CLASS total obs=5

    Obs     NAME      SEX    AGE    HEIGHT    WEIGHT

     1     Alfred      M      14     69.0      112.5
     2     Alice       F      13     56.5       84.0
     3     Barbara     F      13     65.3       98.0
     4     Carol       F      14     62.8      102.5
     5     Henry       M      14     63.5      102.5

                 _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|

     Up to 40 obs from TEMP.CLASSBMI total obs=5

    Obs     NAME      SEX    AGE    HEIGHT    WEIGHT      BMI

     1     Alfred      M      14     69.0      112.5    16.6115
     2     Alice       F      13     56.5       84.0    18.4986
     3     Barbara     F      13     65.3       98.0    16.1568
     4     Carol       F      14     62.8      102.5    18.2709
     5     Henry       M      14     63.5      102.5    17.8703

     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|        _
     ___  __ _| |
    / __|/ _` | |
    \__ \ (_| | |
    |___/\__, |_|
            |_|

    * incase you rerun;
    %utlfkil(c:/temp/classBmi.sas7bdat);

    %utl_submit_r64('
     library(RJDBC);
     library(rJava);
     library(haven);
     library(sqldf);
     source("c:/r400/etc/Rprofile.site");
     class<-read_sas("c:/temp/class.sas7bdat");
     classBmi<-sqldf("
       select
           *
          ,703 * WEIGHT / (HEIGHT * HEIGHT)  as BMI
       from
           class
            ");
     head(classBmi);
     classBmi<- dbWriteTable(conn,"classBmi",classBmi);
    ');

    proc print data=temp.classBmi;
    title "BMI added";
    run;quit;
                     _        _
     _ __ ___  _   _| |_ __ _| |_ ___
    | `_ ` _ \| | | | __/ _` | __/ _ \
    | | | | | | |_| | || (_| | ||  __/
    |_| |_| |_|\__,_|\__\__,_|\__\___|


    * in case you rerun;
    %utlfkil(c:/temp/classBmi.sas7bdat);

    libname temp "c:/temp";
    options validvarname=upcase;

    data temp.class;
      set sashelp.class(obs=5);
    run;quit;

    %utl_submit_r64('
     library(RJDBC);
     library(rJava);
     library(dplyr);
     library(haven);
     source("c:/r400/etc/Rprofile.site");
     class<-read_sas("c:/temp/class.sas7bdat");
     head(class);
     classBmi <- mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT));
     classBmi<- dbWriteTable(conn,"classBmi",classBmi);
    ');

    proc print data=temp.classBmi;
    run;quit;
               _   _
     ___  __ _| | | | ___   __ _
    / __|/ _` | | | |/ _ \ / _` |
    \__ \ (_| | | | | (_) | (_| |
    |___/\__, |_| |_|\___/ \__, |
            |_|            |___/


    48289  * in case you rerun;
    48290  %utlfkil(c:/temp/classBmi.sas7bdat);
    MLOGIC(UTLFKIL):  Beginning execution.
    MLOGIC(UTLFKIL):  Parameter UTLFKIL has value c:/temp/classBmi.sas7bdat
    MLOGIC(UTLFKIL):  %LOCAL  URC
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable UTLFKIL resolves to c:/temp/classBmi.sas7bdat
    SYMBOLGEN:  Macro variable URC resolves to 0
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00889
    MLOGIC(UTLFKIL):  %IF condition &urc = 0 and %sysfunc(fexist(&fname)) is TRUE
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00889
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    MPRINT(UTLFKIL):   run;
    MLOGIC(UTLFKIL):  Ending execution.
    48291  libname temp "c:/temp";
    NOTE: Libref TEMP was successfully assigned as follows:
          Engine:        V9
          Physical Name: c:\temp
    48292  options validvarname=upcase;
    48293  data temp.class;
    48294    set sashelp.class(obs=5);
    48295  run;

    NOTE: There were 5 observations read from the data set SASHELP.CLASS.
    NOTE: The data set TEMP.CLASS has 5 observations and 5 variables.
    NOTE: DATA statement used (Total process time):
          real time           0.00 seconds
          user cpu time       0.00 seconds
          system cpu time     0.00 seconds
          memory              564.28k
          OS Memory           25600.00k
          Timestamp           09/14/2021 08:11:23 AM
          Step Count                        1340  Switch Count  0


    48295!     quit;
    48296  %utl_submit_r64('
    MLOGIC(UTL_SUBMIT_R64):  Beginning execution.
    48297   library(RJDBC);
    48298   library(rJava);
    48299   library(dplyr);
    48300   library(haven);
    48301   source("c:/r400/etc/Rprofile.site");
    48302   class<-read_sas("c:/temp/class.sas7bdat");
    48303   head(class);
    48304   classBmi <- mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT));
    48305   classBmi<- dbWriteTable(conn,"classBmi",classBmi);
    48306  ');
    MLOGIC(UTL_SUBMIT_R64):  Parameter PGMX has value ' library(RJDBC); library(rJava); library(dplyr); library(haven); source("c:/r400/etc/Rprofile.site");
          class<-read_sas("c:/temp/class.sas7bdat"); head(class); classBmi <- mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT)); classBmi<-
          dbWriteTable(conn,"classBmi",classBmi);'
    MLOGIC(UTL_SUBMIT_R64):  Parameter RETURNVAR has value N
    MPRINT(UTL_SUBMIT_R64):   * write the program to a temporary file;
    MLOGIC(UTL_SUBMIT_R64):  %LET (variable name is _WRKSTM)
    MLOGIC(UTL_SUBMIT_R64):  %LET (variable name is _SUBDIR)
    MLOGIC(UTL_SUBMIT_R64):  %PUT &=_subdir
    SYMBOLGEN:  Macro variable _SUBDIR resolves to _TD11372_RDEANGELISL5410_
    _SUBDIR=_TD11372_RDEANGELISL5410_
    MLOGIC(UTL_SUBMIT_R64):  %LET (variable name is _DIR)
    SYMBOLGEN:  Macro variable _WRKSTM resolves to C:/Users/rdeangelis/AppData/Local/Temp/SASTEM~1
    SYMBOLGEN:  Macro variable _SUBDIR resolves to _TD11372_RDEANGELISL5410_
    MLOGIC(UTL_SUBMIT_R64):  %PUT &_dir
    SYMBOLGEN:  Macro variable _DIR resolves to C:/Users/rdeangelis/AppData/Local/Temp/SASTEM~1/_TD11372_RDEANGELISL5410_
    C:/Users/rdeangelis/AppData/Local/Temp/SASTEM~1/_TD11372_RDEANGELISL5410_
    MPRINT(UTL_SUBMIT_R64):   filename r_pgm "c:/txt/r_pgm.txt" lrecl=32766 recfm=v;
    MPRINT(UTL_SUBMIT_R64):   data _null_;
    MPRINT(UTL_SUBMIT_R64):   length pgm $32756;
    MPRINT(UTL_SUBMIT_R64):   file r_pgm;
    SYMBOLGEN:  Macro variable PGMX resolves to ' library(RJDBC); library(rJava); library(dplyr); library(haven); source("c:/r400/etc/Rprofile.site");
                class<-read_sas("c:/temp/class.sas7bdat"); head(class); classBmi <- mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT)); classBmi<-
                dbWriteTable(conn,"classBmi",classBmi);'
    MPRINT(UTL_SUBMIT_R64):   pgm=' library(RJDBC); library(rJava); library(dplyr); library(haven); source("c:/r400/etc/Rprofile.site");
    class<-read_sas("c:/temp/class.sas7bdat"); head(class); classBmi <- mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT)); classBmi<-
    dbWriteTable(conn,"classBmi",classBmi);';
    MPRINT(UTL_SUBMIT_R64):   put pgm;
    MPRINT(UTL_SUBMIT_R64):   putlog pgm;
    MPRINT(UTL_SUBMIT_R64):   run;

    NOTE: The file R_PGM is:
          Filename=c:\txt\r_pgm.txt,
          RECFM=V,LRECL=32766,File Size (bytes)=0,
          Last Modified=14Sep2021:08:11:23,
          Create Time=30Mar2021:12:07:44

    library(RJDBC); library(rJava); library(dplyr); library(haven); source("c:/r400/etc/Rprofile.site"); class<-read_sas("c:/temp/class.sas7bdat"); head(class); classBmi <- mu
    tate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT)); classBmi<- dbWriteTable(conn,"classBmi",classBmi);
    NOTE: 1 record was written to the file R_PGM.
          The minimum record length was 272.
          The maximum record length was 272.
    NOTE: DATA statement used (Total process time):
          real time           0.02 seconds
          user cpu time       0.00 seconds
          system cpu time     0.03 seconds
          memory              358.84k
          OS Memory           25600.00k
          Timestamp           09/14/2021 08:11:23 AM
          Step Count                        1341  Switch Count  0


    MLOGIC(UTL_SUBMIT_R64):  %LET (variable name is __LOC)
    MPRINT(UTL_SUBMIT_R64):   * pipe file through R;
    SYMBOLGEN:  Macro variable __LOC resolves to c:\txt\r_pgm.txt
    MPRINT(UTL_SUBMIT_R64):   filename rut pipe "C:\R400\bin\R.exe --vanilla --quiet --no-save < c:\txt\r_pgm.txt";
    MPRINT(UTL_SUBMIT_R64):   data _null_;
    MPRINT(UTL_SUBMIT_R64):   file print;
    MPRINT(UTL_SUBMIT_R64):   infile rut recfm=v lrecl=32756;
    MPRINT(UTL_SUBMIT_R64):   input;
    MPRINT(UTL_SUBMIT_R64):   put _infile_;
    MPRINT(UTL_SUBMIT_R64):   putlog _infile_;
    MPRINT(UTL_SUBMIT_R64):   run;

    NOTE: The infile RUT is:
          Unnamed Pipe Access Device,
          PROCESS=C:\R400\bin\R.exe --vanilla --quiet --no-save < c:\txt\r_pgm.txt,
          RECFM=V,LRECL=32756

    > library(RJDBC); library(rJava); library(dplyr); library(haven); source("c:/r400/etc/Rprofile.site"); class<-read_sas("c:/temp/class.sas7bdat"); head(class); classBmi <-
    mutate(class, BMI=703 * WEIGHT / (HEIGHT * HEIGHT)); classBmi<- dbWriteTable(conn,"classBmi",classBmi);
    # A tibble: 5 x 5
      NAME    SEX     AGE HEIGHT WEIGHT
      <chr>   <chr> <dbl>  <dbl>  <dbl>
    1 Alfred  M        14   69     112.
    2 Alice   F        13   56.5    84
    3 Barbara F        13   65.3    98
    4 Carol   F        14   62.8   102.
    5 Henry   M        14   63.5   102.
    >
    NOTE: 11 lines were written to file PRINT.
    Stderr output:
    Loading required package: DBI
    Loading required package: rJava
    Warning messages:
    1: package 'RJDBC' was built under R version 4.0.5
    2: package 'DBI' was built under R version 4.0.4
    3: package 'rJava' was built under R version 4.0.5

    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':
