

dichiarazione ctl opt
Ctl-Opt ALWNULL(*USRCTL);
//dichiarazioni variabili programma
Dcl-S SqlStr char (2000);
Dcl-S Esci  ind;
Dcl-S totrighe packed(4:0);
Dcl-F COMUNILS0V WORKSTN SFILE(SF1:s1nrec);
Dcl-DS Result QUALIFIED DIM(9999);
       codcom char(4);
       dencom char(35);
       denpro char(16);
       denreg char(15);
End-DS;

//MAIN
exsr clrsf1;

Esci=*Off;
DOU Esci=*ON;
    EXFMT CR1;
    Select;
      WHEN *In03=*on;
          Esci=*ON;
          leave;
      WHEN *In05=*ON;
          exsr Refresh;
      WHEN *In06=*ON;
          exsr SqlString;
          exsr clrsf1;
          exsr CaricaSubF;
      OTHER;
          IF CODREG <> '' OR CODPRO <> '' OR DEMCOM <> '';
              exsr SqlString;
              exsr clrsf1;
              exsr CaricaSubF;
          ENDIF;
      ENDSL;
    //ci sarà da implementare parte creazione qua
ENDDO;
//on su indicatore per uscire
*InLR= *ON;
return;

begsr Refresh; //pulisco campi
     Clear CODREG;
     Clear CODPRO;
     Clear DEMPRO;
     Clear DEMREG;
     Clear DEMCOM;
     exsr clrsf1;
ENDSR;

//sub per lancio select e carico SubF
begsr CaricaSubF;
      clear £CXMSG;
      clear totrighe;
      s1nrec=0;
      EXEC SQL PREPARE pippo FROM :SqlStr;
      EXEC SQL DECLARE cur INSENSITIVE scroll CURSOR FOR pippo;
      EXEC SQL OPEN cur;
      totrighe=SQLERRD(2); //numero risultati
      IF totrighe>0 and sqlcod=0;
            clear Result;
            EXEC SQL FETCH FROM cur FOR :totrighe ROWS INTO :Result;
      ELSE;
           £CXMSG='Non sono stati trovati risultati per la ricerca';
           *in81=*on;
           write Hdr1;
      ENDIF;
      EXEC SQL CLOSE cur;
      DOW s1nrec<totrighe;
           *in87=*off;
           s1nrec=s1nrec+1;
           S1CCOM=Result(s1nrec).codcom;
           S1DENC=Result(s1nrec).dencom;
           S1DENP=Result(s1nrec).denpro;
           S1DENR=Result(s1nrec).denreg;
           write sf1;
      ENDDO;
      if s1nrec > 0;
          s1nrec = 1;
          DEMREG = Result(s1nrec).denreg;
          DEMPRO = Result(s1nrec).denpro;
      ENDIF;
ENDSR;

//sub per creare stringa sql
begsr SqlString;
      clear SqlStr;
      SqlStr='SELECT com.codice, com.denominazione, +
               prov.denominazione, reg.denominazione +
               FROM AS2401db.TABELLA_COMUNI as com +
               left join AS2401db.TABELLA_PROVINCE  +
               as prov ON com.provincia=prov.sigla  +
               left join AS2401db.TABELLA_REGIONI as REG +
               on prov.codregione=reg.codice';
      IF CODPRO <> *BLANKS;
            SqlStr= %Trim(SqlStr) + ' WHERE prov.sigla = '''+CODPRO+'''';
        ELSEIF CODREG <> *BLANKS;
            SqlStr= %Trim(SqlStr) + ' WHERE reg.codice = '''+CODREG+'''';
      ENDIF;
      IF CODPRO = *BLANKS AND CODREG = *BLANKS AND DEMCOM <> *BLANKS;
              SqlStr= %Trim(SqlStr) + ' WHERE UPPER(com.denominazione) like ''%'+%Trim(DEMCOM)+'%''';
      ELSEIF (CODPRO <> *BLANKS OR CODREG <> *BLANKS) AND DEMCOM <> *BLANKS;
              SqlStr= %Trim(SqlStr) + 'and UPPER(com.denominazione) like ''%'+%Trim(DEMCOM)+'%''';
      ENDIF;
ENDSR;

begsr clrsf1;
     clear SF1;         //svuota subfile
     write Hdr1;
     *in87 = *ON;       //imposta gli indicatori per impedire la visualizzazione
     *in83 = *ON;
     write CR1;         //emette il record di controllo senza emettere il subfile
     *in83 = *off;
     s1nrec=*zeros;     //Azzera il valore della chiave corrente del subfile
ENDSR; 















# Learn Markdown

You may have heard about Markdown, if you have it's a good thing.

Markdown is a plain text formatting syntax designed so that it can optionally be converted to HTML.

In this book, you'll learn how to write document using the markdown syntax.

![Example of markdown with associated output document on the right](./assets/preview.png)
