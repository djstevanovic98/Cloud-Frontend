Cloud Frontend
##

RAF Cloud
Projekat

1.	Pregled zadatka
Cilj rada je implementacija WEB aplikacije koja će predstavljati imitaciju našeg cloud provajdera. Aplikacija treba da stvori utisak da je korisnik u stanju da kreira i kontroliše stanje fizičke mašine i vezanih resursa.
Projekat će biti podeljen po glavnim domenskim objektima koje je potrebno implementirati: upravljanje korisnicima i upravljanje mašinama.
Upravljanje korisnicima je definisano u trećem domaćem zadatku. Dodatak su nove permisije za upravljanje mašinama.
Takođe, potrebno je omogućiti zakazivanje operacija na mašini (START, STOP ili RESTART) kao i evidenciju grešaka nad zakazanim operacijama.
2.	Zahtevi
Mašina kao entitet sadrži sledeće obavezne atribute:
Ime	Tip	Opis
id	Long	Jedinstvena identifikacija entiteta
status	Enum(STOPPED, RUNNING)	Trenutni status mašine.
createdBy	FK_UserTable(id)	Referenca na korisnika koji je napravio ovu mašinu.
active	Boolean	Da li je mašina obrisana ili ne, soft-delete.
Ovo su osnovni atributi koje je potrebno podržati. Dozvoljeno je njihovo proširivanje i modifikacija po potrebi. Svaka mašina pripada svom kreatoru i kreator ima uvid samo u svoje mašine.
Akcije koje se mogu izvršiti na mašini:
●	SEARCH
●	START
●	STOP
●	RESTART
●	CREATE
●	DESTROY 
Svaka akcija je ispraćena permisijom. Potrebno je proširiti skup permisija i podržati još 6 novih, za svaku od mogućih akcija na mašini: can_search_machines, can_start_machines, can_stop_machines, can_restart_machines, can_create_machines, can_destroy_machines. Shodno tome, korisnik bez specifične permisije nema pristup, ni mogućnost za izvršavanja odgovarajuće akcije.
Operacije START, STOP i RESTART nisu instant i za njihovo izvršavanje je potreban neki vremenski period. Dok se neka od tih operacija izvršava nad mašinom, obezbediti da nije moguće izvršiti ni jednu drugu operaciju nad tom istom mašinom.
Svaki uspešni poziv ka nekoj od funkcionalnosti mora da vrati odgovor odmah sa statusom 2xx, a ne nakon X sekundi - operacije START, STOP i RESTART nakon poziva odmah vraćaju odgovor, a njihovo izvršavanje na odabranoj mašini će se nastaviti u pozadini. Nakon završetka izvršavanja bilo koje od ovih akcija, potrebno je obavestiti frontend o promeni stanja mašine korišćenjem web socket-a. Frontend reaguje na to obaveštenje i adekvatno ažurira prikaz tako da bude u skladu sa stanjem u kojim se mašina našla. 
Akcije/operacije na mašini:
SEARCH
Iz baze vraća niz aktivnih mašina za ulogovanog korisnika.
Sledeca tabela ne predstavlja entitet već moguće parametre pretrage. Svi parametri su opcioni.
Ime	Tip	Opis
name	String	Vratiti mašine čije ime je jednako ili sadrži vrednost ovog parametra. Case insensitive komparacija.
status	List<String>	Vratiti mašine čiji je status u jednom od poslatih.
dateFrom	Date (datum bez vremena)	Predstavlja početak vremenskog opsega.
Samo ukoliko je i dateTo definisan onda će vratiti mašine čiji je momenat kreiranja između dateFrom i dateTo.
dateTo	Date (datum bez vremena)	Predstavlja kraj vremenskog opsega. Pročitati dateTo.

START
Mašina koja je u bilo kom stanju koje nije STOPPED ne može biti startovana. Proces starta traje 10+ sekundi sa implementiranom vremenskom devijaciom po nahođenju.
Nakon proteklog navedenog vremena mašinu prebaciti u stanje RUNNING.

STOP
Mašina koja je u bilo kom stanju koje nije RUNNING se ne može isključiti. Proces isključivanja traje 10+ sekundi sa implementiranom vremenskom devijaciom po nahođenju.
Nakon proteklog navedenog vremena mašinu prebaciti u stanje STOPPED.

RESTART
Mašina koja je u bilo kom stanju koje nije RUNNING ne može biti restartovana. Proces restarta traje 10+ sekundi sa implementiranom vremenskom devijaciom po nahođenju.
Nakon protekle polovine navedenog vremena mašinu prebaciti u stanje STOPPED, a po završetku druge polovine prebaciti ponovo u RUNNING.

CREATE
Ova akcija je instant i služi za kreiranje nove mašine. Prilikom kreiranja mašine potrebno je proslediti sve obavezne parametre. Rezultat je mašina koja je u stanju STOPPED. 
Pri kreiranju mašine potrebno je povezati ulogovanog korisnika sa njom.

DESTROY
Destroy akcija je instant i može se izvršiti samo na mašinama koje su u stanju STOPPED. Ova akcija samo markira mašinu kao obrisanu, ali je ne briše iz baze.

Zakazivanje operacije na mašini:
Operacije START, STOP i RESTART se mogu zakazati - korisnik može da odredi datum i vreme kada će željena operacije da se izvrši. U odgovarajuće vreme, potrebno je da sistem samostalno pokuša da izvrši operaciju, ukoliko je usov za njeno izvršavanje ispunjen. Ukoliko operacija nije uspela, potrebno je to evidentirati u novoj tabeli - ErrorMessage. Entitet ErrorMessage mora da sadrži datum, id mašine, operaciju koja je bila zakazana i poruku o greški koja se dogodila.

Frontend
Na frontendu, pored grafičkog interfejsa za upravljanjem korisnicima iz domaćeg 3, potrebno je implementirati dve nove stranice: 
1.	Stranicu za pretragu mašina
2.	Stranicu za kreiranje mašina
3.	Stranicu sa istorijom grešaka
1. Pretraga mašina:
Implementirati da se upotrebom SEARCH-a prikažu sve mašine bez prosleđenih parametara kada se stranica učita. Pored toga, implemenitari formu iznad tabele koja sadrži potrebna polja da pokrije sve funkcionalnosti za pretragu koje nudi Backend. Filteri će se primeniti submitom te forme.

2. Kreiranje mašine
Napraviti jednostavnu formu koja će sadržati ime koje je potrebno da bi se mašina kreirala na Backend strani. Ova forma neće sadržati id, status, niti korisnika, to će Backend zaključiti sam.
3. Stranica sa istorijom grešaka
Tabela u kojoj je potrebno prikazati greške koje su se dogodile pri izvršavanju zakazane operacije. Potrebno je prikazati samo greške koje su se dogodile nad mašinama ulogovanog korisnika.
Korisnik bez specifične permisije nema pristup, ni mogućnost za izvršavanja odgovarajuće akcije na frontend-u. 
Ukoliko korisnik nema nijednu permisiju, nakon uspešnog login-a, obavestiti ga alertom. 

3.	Tehnologije
Backend:
●	Spring 
●	Relaciona baza podataka
Frontend:
●	Angular 2+

Dodatak:

User management

1.	Pregled zadatka
Zadatak je napraviti web aplikaciju za upravljanje korisnicima i njihovim permisijama. Potrebno je napraviti podršku za CRUD operacije nad korisnikom i obezbediti autorizaciju zahteva korisnika uz pomoć definisanog seta permisija.
Permisije koje je potrebno obezbediti i koje se mogu dodeliti korisniku su: can_create_users, can_read_users, can_update_users, can_delete_users. Zahtevi za create/read/update/delete korisnika će biti mogući samo ukoliko autentifikovani korisnik ima odgovarajuću permisiju za taj zahtev.
Autentifikaciju korisnika je potrebno implementirati korišćenjem JWT-a.

2.	Zahtevi
Korisnik kao entitet sadrži ime, prezime, email adresu, lozinku i set permisija. Email adresa je jedinstvena i nije moguće kreirati više korisnika sa istom email adresom. Lozinku čuvati upotrebom nekog hash algoritma po izboru, nikako kao plain tekst. Svi atributi, osim permisija, su obavezni.
Autentifikacija korisnika se vrši slanjem email adrese i lozinke. Ukoliko su kredencijali validni, odgovor je JWT kog korisnik treba poslati uz svaki sledeći zahtev kako bi bio autentifikovan.
Ukoliko korisnik nema permisiju za određeni zahtev, potrebno je vratiti status code 403 Forbidden i korisnika obavestiti o nedozvoljenoj aktivnosti.
Nakon login-a na frontendu, dobijeni token je potrebno čuvati u localstorage-u i slati ga pri svakom sledećem zahtevu.
Na frontendu je potrebno implementirati 4 stranice: 
1.	Stranicu za login
2.	Stranicu za prikaz korisnika - tabela sa svim korisnicima u kojoj se prikazuje ime, prezime, email i permisije za svakog od korisnika
3.	Stranicu za dodavanje korisnika - forma koja sadrži polja za ime, prezime, email, lozinku i permisije koje će novi korisnik imati. Sva polja su obavezna i u slučaju greške pri kreiranju, potrebno je obavestiti korisnika.
4.	Klikom na korisnika u tabeli otvara se stranica za edit koja sadrži polja za ime, prezime, email i permisije sa vrednostima koje odabrani korisnik ima.
Frontend mora biti svestan permisija i na osnovu njih da dozvoli prikaz/akciju. 
Ponašanje frontend-a u zavisnosti od permisije:
Permisija	Ponašanje kada korisnik ima permisiju	Ponašanje kada korisnik nema permisiju
can_read_users	- Dozvoljen pristup stranici sa tabelom svih korisnika.	- Prikazati poruku da korisnik nema permisiju
can_create_users	- Prikaz linka koji vodi na stranicu za dodavanje korisnika.
- Dozvoljen pristup stranici za kreiranje user-a.	- Link za pristup stranici za dodavanje korisnika je isključeno ili nevidljivo.
- Zabranjen pristup stranici za kreiranje user-a.
can_update_users	- Email adresa korisnika u tabeli je klikabilna. 
- Dozvoljen pristup stranici za edit korisnika.	- Email adresa korisnika u tabeli nije klikabilna.
- Zabranjen pristup stranici za edit korisnika.
can_delete_users	- U tabeli za svakog korisnika postoji dugme za njegovo brisanje.	- Dugme za brisanje korisnika je isključeno ili nevidljivo.
Ukoliko korisnik nema nijednu permisiju, nakon uspešnog login-a, obavestiti ga alertom. 

3.	Tehnički zahtevi
Backend:
●	Spring
●	Relaciona baza podataka
Frontend:
●	Angular 2+




