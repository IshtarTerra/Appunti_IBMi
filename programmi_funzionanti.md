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
// ------------------------------------------------------------------------------------ programma 2 ------------------------------------------------------------------------------------
**FREE

// Operandi. 10 cifre di cui 7 decimali. Arrotondamento per eccesso al decimo, centesimo o millesimo
//
dcl-s first_value Zoned(10:4);
dcl-s second_value Zoned(10:4);
dcl-s result Zoned(10:4);
dcl-s result_adjusted Zoned(10:4);

// Operatore matematico
dcl-s operator char(1);

// Valore inserito utente
dcl-s user_input char(10);

// Discriminante arrotondamento
dcl-s check_adjustment char(1);

// La dsply prende come input un char, per cui se il valore dev'essere processato come numero dev'essere convertito
dsply 'Primo valore numerico = ' ' ' user_input;
first_value = %dec(user_input: 10: 4);

dsply 'Secondo valore numerico = ' ' ' user_input;
second_value = %dec(user_input: 10: 4);

dsply 'Operatore matematico = ' ' ' operator;

select;  // La select in RPG è l'equivalente dello switch
  when operator = '+';
    result = first_value + second_value;
  when operator = '-';
    result = first_value - second_value;
  when operator = 'x';
    result = first_value * second_value;
  when operator = '/';
    result = first_value / second_value;
  other;
    dsply ('Operatore non riconosciuto');
  ENDSL;

dsply ('Valore del risultato: ' + %char(result));

dsply 'Arrotondamento: 1 - dec; 2 - cent; 3 - mill' ' ' check_adjustment;

select;
  when check_adjustment = '1';
    result_adjusted = %dech(result: 10: 1);
  when check_adjustment = '2';
    result_adjusted = %dech(result: 10: 2);
  when check_adjustment = '3';
    result_adjusted = %dech(result: 10: 3);
  other;
    dsply ('I valori ammessi sono 1, 2 e 3');
ENDSL;

dsply ('Valore arrotondato: ' + %char(result_adjusted));

*inLR = *on; 
