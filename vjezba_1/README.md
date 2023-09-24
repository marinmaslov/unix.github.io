# 🚀 Vježba 1: Prevođenje i povezivanje programa

## Sažetak 📃

___

## Zadatak 📋
U okviru vježbe potrebno je napisati program koji ispisuje pozdravnu poruku, na dva načina.

U prvom slučaju poruka se ispisuje iz glavnog programa. U drugom, funkcija koja ispisuje samu poruku nalazi se u zasebnoj datoteci izvornog koda (koristite se `.h` datotekom da ih povežete). Izvorni kod svih programa (`.c` datoteke) napišite korištenjem `joe` editora.

Nakon što ste napisali ta dva programa potrebno je napisati `Makefile` s 2 pravila:
1. Prevođenje i povezivanje prvog programa (korištenjem datoteka objektnog koda [`.o` datoteke])
2. Prevođenje i povezivanje drugog programa korištenjem datoteka izvornog koda (ulaz u `gcc` prevodioca, tj. `.c` datoteke)

Sve što napišete testirajte, a eventualne grešake otklonite. U konačnici, direktorij u kojem ste odradili vježbu komprimirajte u `.tar` datoteku te istu učitajte na Merlin.

___

## Upute 🧭

### 1. Pisanje `.c` datoteka 📃

Pomoću `joe` editora stvaramo datoteku `prvi.c` u koju upisujemo naš kod:
``` c
#include <stdio.h>

int main() {
   printf("Hello, World!\n");
   return 0;
}
```
Datoteku spremamo te izlazimo iz `joe` editora pritiskanjem `ctrl` + `K` + `X`.

---

### 2. Pisanje `.h` datoteka i povezivanje istih s `.c` datotekama 🕸️

Pomoću `joe` editora stvaramo datoteku `hello.c` u koju upisujemo naš kod:
``` c
#include <stdio.h>

void hello() {
   printf("Hello, World!\n");
}
```
Paralelno s ovim napravite i `drugi.c` s istim kodom kao i `prvi.c`.

Sada otvorite `drugi.c` te zamijenite `printf("Hello, World!\n");` s `hello();`.

Kako bi se programi `drugi.c` i `hello.c` povezali tj. kako bismo funkciju `hello();` definiranu u `hello.c` mogli koristiti u `drugi.c` moramo napisati `.h` datoteku na sljedeći način:

``` c
#ifndef HEADER_FILE /* Include guard */
#define HEADER_FILE

void hello(); /* Function declaration */

#endif
```
Ova .h datoteka predstavlja zaglavlje (header file) za C ili C++ programski jezik. Zaglavlje se koristi kako bi se definirale funkcije, varijable, ili druge deklaracije koje će se koristiti u drugim dijelovima programa, kao što su izvorne datoteke ili druge .c datoteke. Uz to, ovakva datoteka često uključuje "include guard" kako bi se spriječila višestruka uključenost istog zaglavlja u programu.

Objašnjenje svake linije u datoteci:

- `#ifndef HEADER_FILE`: ovo je predprocesorska naredba koja provjerava postojanje definicije simbola HEADER_FILE, ako simbol HEADER_FILE nije definiran (što će biti slučaj prvi put kad se uključi ovo zaglavlje), tada će se izvršiti sljedeći blok koda
- `#define HEADER_FILE`: ovdje se definira simbol HEADER_FILE, ovaj simbol će biti iskorišten za provjeru kasnije u programu kako bi se spriječila višestruka uključenost istog zaglavlja
- `void hello();`: je deklaracija funkcije hello, deklaracija govori kompajleru da će funkcija `hello` biti dostupna negdje u programu, ali ne daje detalje o njenom tijelu (implementaciji) - to omogućava drugim dijelovima programa da koriste funkciju `hello` bez da znaju kako je definirana
- `#endif`: ovo zatvara blok koda koji je započet s `#ifndef`, kada se uključi ovo zaglavlje u nekom drugom dijelu programa, predprocesor (predkompajler) će provjeriti je li simbol `HEADER_FILE` definiran, ako je, to znači da je ovo zaglavlje već bilo uključeno, pa će se sadržaj između `#ifndef` i `#endif` preskočiti

Povezivanje `.c` i `.h` datoteka je jednostavno! U otvorite `drugi.c` te na vrhu datoteke (ispod mjesta gdje ste pozvali `#include <stdio.h>`) uključite `.h` datoteku na sljedeći način:
``` c
#include "hello.h"
```
Na ovaj način smo u program `drugi.h` uključili datoteku zaglavlja `hello.h` i na taj način nam je omogućeno da u programu `drugi.c` pozivamo funkciju `hello()` definiranu u `hello.c`

---

### 3. Prevođenje i povezivanje 🤖

Naše `.c` datoteke nisu spremne za izvršavanje! Potrebno ih je prevesti i povezati, a to se vrši korištenjem skupa programerskih pšrevoditelja koji je nastao iz GNU projekta - "GNU Compiler Collection" tj. `gcc`.

Prevođenje i povezivanje se vrši na sljedeći način:

``` bash
gcc -Wall prvi.c 
```
Argument -Wall uključuje ispisivanje upozorenja na naredbenom retku u slučaju da u vašem programu postoje greške.

Nakon izvršavanja gornje naredbe dobit ćemo izvršnu datoteku `a.out`. `a` je zadano (default) ime, da smo htjeli postaviti neko svoje ime toj datoteci koristili bismo:
``` bash
gcc -Wall prvi.c -o prvi
```
❗ Napomena: `a.out` i `prvi` su identične datoteke.

Gore je prikazano prevođenje i povezivanje u jednoj liniji. Ono se može razbiti na dva dijela.
Prevođenje:
``` bash
gcc -Wall -c prvi.c
```
Kao rezultat ćemo dobiti datoteku `prvi.o` koja se koristi u nastavku.

Povezivanje:
``` bash
gcc -Wall prvi.c -o prvi
```
Kao rezultat ćemo dobiti izvršnu (executable) datoteku `prvi`.

❗ Napomena: Kod stvaranje izvršne datoteke (executable), automatski se postavljaju prava pristupa koja omogućavaju pokretanje datoteke, ako izvršite naredbu ls -a mozete vidjeti koja su to prava.

---

#### 3.1. Prevođenje i povezivanje kad postoji `.h` datoteka

Kompajliranje je slično kao i u prvom slučaju, samo što ovdje nabrajamo sve .c datoteke:
``` bash
gcc -Wall drugi.c hello.c -o drugi
```
❗ Napomena: Osigurajte da se sve `.c` i `.h` datoteke nalaze u istom direktoriju (ili definirajte putanje do istih u naredbi za prevođenje i povezivanje).

---

### 4. Pisanje `Makefile` pravila 📝
Makefile je datoteka koja se koristi za automatizaciju procesa prevođenja i povezivanja u programskom jeziku C i drugim sličnim programskim jezicima.

Za stvaranje makefile datoteke upisujemo sljedeću naredbu:
``` bash
joe Makefile
```
U jednoj `Makefile` datoteci može biti više pravila, pa ćemo ih sve zapisati u istu, no prije toga prođimo kroz `Make` sintaksu.

---

#### 4.1. Make varijable

Varijable se definiraju VELIKIM slovima.

``` makefile
VARIJABLA = nekakav_string
```

Pozivanje varijable vrši se korištenjem znaka `$` ispred imena varijable koje se nalazi u zagradama:

``` makefile
$(VARIJABLA)
```

#### 4.2. Make pravila
Pravila za prevodenje i povezivanje dana su u formi:

``` makefile
naziv_pravila: ovisnosti
   naredbe
```
❗ Napomena: indentacija (uvlačenje redova) je od iznimne važnosti (kao i u Pythonu)!

- `naziv_pravila` je najčešće isto kao i ime datoteke koja nastaje kao produkt izvođenja pravila
- `zavisnosti (dependencies)` predstavljaju datoteke koje su potrebne za izvršavanje pravila
- `naredbe` su skup postupaka koji se izvršavaju kada je pozvano određeno pravilo (redak u kojem je pravilo mora bit uvučen [TAB])

Postoje još i `implicitna pravila` koja se koriste za opisivanje nekih postupaka koji se izvode vrlo često.

Primjer implicitnog pravila:
``` makefile
.c.o:
  $(CC) $(CFLAGS) -c $<
```
Obješnjenje: ovo implicitno pravilo se koristi za dobivanje objektne datoteke `.o` iz datoteke C izvornog koda.

---

#### 4.3. Pisanje `Makefile` datoteke

U našoj makefile datoteci ćemo prvo definirati varijable:

``` bash
CC = /usr/bin/gcc
CFLAGS = -Wall
TARGETS = prvi prvi2 drugi drugi2
```

- `CC` je putanja do `gcc` prevodioca
- `CFLAGS` opcije za `gcc` prevodioca (sadrži `-Wall`, koja omogućuje ispis grešaka na zaslonu)
- `TARGETS` - ciljevi, su imena izvršnih datoteka (koristi se u pravilima `all` i `clean`)

Nakon definiranja varijabli, potrebno je definirati zadano (default) pravilo koje se izvršava ako make rutinu pozovemo bez imena pravila:

``` bash
default: drugi
all: $(TARGETS)
```

Sada ćemo definirati pravila za sljedeće:

- prevođenje i povezivanje porgrama prvi i drugi korištenjem datoteka objektnog koda [`.o` datoteke]
- prevođenje i povezivanje programa prvi i drugi korištenjem datoteka izvornog koda (ulaz u gcc prevodioca [`.c` datoteke])

Dopišimo (u datoteku `Makefile`) za početak pravila za prevođenje i povezivanje programa prvi i drugi korištenjem datoteka objektnog koda:

``` bash
CC = /usr/bin/gcc
CFLAGS = -Wall
TARGETS = prvi prvi2 drugi drugi2

default: drugi
all: $(TARGETS)

prvi: prvi.o
   $(CC) $(CFLAGS) prvi.o -o prvi
   
drugi: drugi.o hello.o
   $(CC) $(CFLAGS) drugi.o hello.o -o drugi
```

Zatim dopišimo pravila za prevođenje i povezivanje programa prvi i drugi korištenjem datoteka izvornog koda:

``` bash
prvi1: prvi.c
   $(CC) $(CFLAGS) prvi.c -o prvi2
   
drugi2: drugi.c hello.c
   $(CC) $(CFLAGS) drugi.c hello.c \
   -o $(BIN)/drugi2
```
❗ Napomena: Kod pravila drugi2, dio `-o $(BIN)/drugi2` je napisan u novom redu, to je moguće ako se (kao što je vidljivo) u prethodnom redu koristi znak `/`.

Na kraju dodajmo proceduru za brisanje privremenih, objektnih i izvršnih datoteka, jer ne želimo nikakav `trash` (smeće) u našoj mapi koje bi nam moglo poremetiti buduća izvršavanja:

``` bash
clean:
   rm -f *.o*~ a.out $(TARGETS)
```
Objašnjenje `clean` pravila:

- `*.o`: ovaj uzorak odgovara svim objektnim datotekama (datoteke s ekstenzijom `.o`)
- `*~`: ovaj uzorak odgovara rezervnim datotekama koje stvaraju neki tekstualni uređivači, te datoteke imaju znak ~ (tilde) dodan na njihova imena i često se automatski generiraju kao sigurnosne kopije vaših izvornih datoteka
- `a.out`: ovo je zadano ime izvršne datoteke koju kompajler generira kada nije navedeno posebno ime izlazne datoteke, pravilo clean će izbrisati ovu datoteku
- `$(TARGETS)`: je varijabla koja sadrži popis imena ciljeva (u ovom slučaju, prvi, prvi2, drugi i drugi2), pravilo clean će ukloniti izvršne datoteke koje odgovaraju tim ciljevima

---

#### 4.4. Izvršavanje `Makefile` pravila

Pravila se izvršavanju korištenjem sljedeće naredbe:

``` bash
make <neko_od_imena_pravila_koje_smo_definirali>
```
