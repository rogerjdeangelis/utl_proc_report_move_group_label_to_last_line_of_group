# utl_proc_report_move_group_label_to_last_line_of_group
Proc report Move group label to last line of group.  
    SAS Forum: Proc report Move group label to last line of group

    github
    https://goo.gl/WF8sH9
    https://github.com/rogerjdeangelis/utl_proc_report_move_group_label_to_last_line_of_group

      SAS/WPS (exactly the same results)

    You will never get painted into a corner using this technique.
    I wish ODS would better honor report output?

    https://goo.gl/5hxZfB
    https://communities.sas.com/t5/Base-SAS-Programming/Show-at-the-quot-end-quot-not-quot-first-quot-when-proc-report/m-p/429457

    Original topic: Show at the "end",not "first" when proc report and group


    HAVE
    =====

       REPORT Default Behavior

                                                  Product Sales
                                       Men's Casual           Women's Casual
         REGION     SUBSIDIARY           Sum        Mean         Sum        Mean
         Canada     Montreal         $53,929     $53,929     $24,497     $24,497
                    Ottawa           $19,210     $19,210     $18,712     $18,712
                    Toronto          $15,403     $15,403     $63,492     $63,492
                    Vancouver       $353,361    $353,361    $304,106    $304,106
         Pacific    Auckland               .           .     $18,189     $18,189
                    Canberra         $24,733     $24,733     $15,032     $15,032
                    Jakarta         $373,908    $373,908     $18,761     $18,761
                    Kuala Lumpur    $106,657    $106,657     $36,110     $36,110
                    Manila          $128,309    $128,309    $131,794    $131,794
                    Singapore        $28,761     $28,761           .           .
                                  $1,104,271    $122,697    $630,693     $70,077

        RULES ( Move Canada and Pacific to then last subsidiary line)

                                                 Product Sales
                                      Men's Casual           Women's Casual
          REGION   SUBSIDIARY           Sum        Mean         Sum        Mean
                   Montreal         $53,929     $53,929     $24,497     $24,497
                   Ottawa           $19,210     $19,210     $18,712     $18,712
                   Toronto          $15,403     $15,403     $63,492     $63,492
          Canada   Vancouver       $353,361    $353,361    $304,106    $304,106
                   Auckland               .           .     $18,189     $18,189
                   Canberra         $24,733     $24,733     $15,032     $15,032
                   Jakarta         $373,908    $373,908     $18,761     $18,761
                   Kuala Lumpur    $106,657    $106,657     $36,110     $36,110
                   Manila          $128,309    $128,309    $131,794    $131,794
          Pacific  Singapore        $28,761     $28,761           .           .
                                 $1,104,271    $122,697    $630,693     $70,077


     PROCESS (all the code)
     =======================

          * output the first report to a SAS dataset;
          proc report data=shoes nowd out=have;
          title 'REPORT Default Behavior';
            column region subsidiary product,(sales sales=savg);

            define region / group
                   style(column)=Header;
            define subsidiary / group
                   style(column)=Header;
            define product / across 'Product Sales';
            define sales / sum 'Sum' f=dollar10.;
            define savg / mean 'Mean' f=dollar10.;
            rbreak after / summarize style=Header;
          run;

          * move region line;
          data intermediate;
            set have(drop=_B:);
            by region notsorted;
            if not last.region then region='';
          run;quit;

          * produce report;
          proc report data=intermediate nowd;
          format _numeric_ dollar10.;
          cols region subsidiary
           ("Product Sales"
              ("Men's Casual"
                 _C3_ _C4_)
              ("Women's Casual"
                _C5_ _C6_)
           );
          run;quit;

     OUTPUT
     ======                                      Product Sales
                                     Men's Casual           Women's Casual
         REGION   SUBSIDIARY           Sum        Mean         Sum        Mean
                  Montreal         $53,929     $53,929     $24,497     $24,497
                  Ottawa           $19,210     $19,210     $18,712     $18,712
                  Toronto          $15,403     $15,403     $63,492     $63,492
         Canada   Vancouver       $353,361    $353,361    $304,106    $304,106
                  Auckland               .           .     $18,189     $18,189
                  Canberra         $24,733     $24,733     $15,032     $15,032
                  Jakarta         $373,908    $373,908     $18,761     $18,761
                  Kuala Lumpur    $106,657    $106,657     $36,110     $36,110
                  Manila          $128,309    $128,309    $131,794    $131,794
         Pacific  Singapore        $28,761     $28,761           .           .
                                $1,104,271    $122,697    $630,693     $70,077

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;


    *
    __      ___ __  ___
    \ \ /\ / / '_ \/ __|
     \ V  V /| |_) \__ \
      \_/\_/ | .__/|___/
             |_|
    ;

    %utl_submit_wps64('
    libname wrk sas7bdat "%sysfunc(pathname(work))";
    libname hlp sas7bdat "C:\Program Files\SASHome\SASFoundation\9.4\core\sashelp";

    proc sort data=hlp.shoes out=shoes;
    by region subsidiary product;
    where region in ("Canada" "Pacific") and
          product contains "Casual";
    run;


    * output the first report to a SAS dataset;
    proc report data=shoes nowd out=have;
    title "REPORT Default Behavior";
      column region subsidiary product,(sales sales=savg);

      define region / group
             style(column)=Header;
      define subsidiary / group
             style(column)=Header;
      define product / across "Product Sales";
      define sales / sum "Sum" f=dollar10.;
      define savg / mean "Mean" f=dollar10.;
      rbreak after / summarize style=Header;
    run;

    * move region line;
    data intermediate;
      set have(drop=_B:);
      by region notsorted;
      if not last.region then region="";
    run;quit;

    * produce report;
    proc report data=intermediate nowd;
    format _numeric_ dollar10.;
    cols region subsidiary
     ("Product Sales"
        ("Mens Casual"
           _C3_ _C4_)
        ("Womens Casual"
          _C5_ _C6_)
     );
    run;quit;
    ');

    *
     ___  __ _ ___
    / __|/ _` / __|
    \__ \ (_| \__ \
    |___/\__,_|___/

    ;


    proc datsets lib=work kill;
    run;quit;

    * output the first report to a SAS dataset;
    proc report data=shoes nowd out=have;
    title 'REPORT Default Behavior';
      column region subsidiary product,(sales sales=savg);

      define region / group
             style(column)=Header;
      define subsidiary / group
             style(column)=Header;
      define product / across 'Product Sales';
      define sales / sum 'Sum' f=dollar10.;
      define savg / mean 'Mean' f=dollar10.;
      rbreak after / summarize style=Header;
    run;

    * move region line;
    data intermediate;
      set have(drop=_B:);
      by region notsorted;
      if not last.region then region='';
    run;quit;

    * produce report;
    proc report data=intermediate nowd;
    format _numeric_ dollar10.;
    cols region subsidiary
     ("Product Sales"
        ("Men's Casual"
           _C3_ _C4_)
        ("Women's Casual"
          _C5_ _C6_)
     );
    run;quit;

