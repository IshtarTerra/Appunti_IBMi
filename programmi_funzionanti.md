**FREE

ctl-opt datfmt(*iso);

dcl-s user_input char(10);
dcl-s time_instant date(*iso);
dcl-s time_amount_to_add int(10);
dcl-s dmy char(1);

dsply 'Inserire una data ' ' ' user_input;
time_instant = %date(user_input);

reset user_input;

dsply time_instant;

dsply 'Quanto tempo vuoi aggiungere? ' ' ' user_input;

time_amount_to_add = %int(user_input);

exsr choice;

dsply 'Data con aggiunta: ' ' ' time_instant;

*inLR = *on;

begsr choice;
  dou dmy = 'd' or dmy = 'm' or dmy = 'y';
    dsply 'Giorni (d), mesi (m) o anni (y)?' ' ' user_input;

    dmy = %char(user_input);

    select;
      when dmy = 'd';
        time_instant += %days(time_amount_to_add);
        leave;
      when dmy = 'm';
        time_instant += %months(time_amount_to_add);
        leave;
      when dmy = 'y';
        time_instant += %years(time_amount_to_add);
        leave;
      other;
        dsply 'Reinserire';
    ENDSL;
  enddo;

  reset dmy;
ENDSR;
 
