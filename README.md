# 🚀 Vježba 1: Prevođenje i povezivanje programa

## Zadatak 📋
U okviru vježbe potrebno je napisati program koji ispisuje pozdravnu poruku, na dva načina.

U prvom slučaju poruka se ispisuje iz glavnog programa. U drugom slučaju, funkcija koja ispisuje samu poruku nalazi se u zasebnoj datoteci izvornog koda. Izvorni kod svih programa (datoteke .c) napišite korištenjem joe editora.

Vježbu napravite na slijedeći način:


## Pregled vježbe 🧭

Za početak se pozicionirajmo u `unix` direktorij.

To činimo na sljedeći način:
```bash
cd unix
```
Napomena: `unix` direktorij se nalazi u vašem `home` direktoriju (putanja: `home/username`, kako biste se mogli pozicionirati u `unix` direktorij morate bili u vašem `home` direktoriju.

_____

U nastavku stvaramo novi direktorij imena `vjezba1` i to na sljedeći način:

``` bash
mkdir vjezba1
```
Te se zatim pozicioniramo u isti:

``` bash
cd vjezba1
```
___

Zadatak je da napišemo program (u c jeziku) koji ispisuje pozdravnu poruku na dva načina:
<br>&nbsp;&nbsp;&nbsp;<b>1. način:</b> Pozdravna poruka se ispisuje iz glavnog programa
<br>&nbsp;&nbsp;&nbsp;<b>2. način:</b> Pozdravna poruka se ispisuje iz funkcije koja se nalazi u drugom programu

___

### 1. NAČIN

Pomoću `joe` editora stvaramo datoteku `prvi.c` u kojoj pišemo naš program:

``` c
#include <stdio.h>

int main() {
   printf("Hello, World!\n");
   return 0;
}
```

Nakon pisanja programa spremamo datoteku naredbama `CTRL` + `K` + `X`.

___

Sada nam se u driektoriju `vjezba1` nalazi datoteka `prvi.c`. Ona nije spremna za izvršavanje jer ju prvo treba kompajlirati, a to radimo na sljedeći način:

``` bash
gcc prvi.c -Wall
```
Argument `-Wall` uključuje ispisivanje grešaka na naredbenu traku u slučaju da u vašem programu postoje greške.

Nakon izvršavanja gornje naredbe dobit ćemo izvršnu datoteku `a.out` (a je defaultno ime, da smo htjeli postaviti neko svoje ime toj datoteci koristili bi argument `-o naše_ime`).

___

Kako bismo izvršili naš program potrebno je napraviti sljedeće:

``` bash
./a.out
```
___

### 2. NAČIN

Drugi način je da se pozdravna poruka ispiše iz funkcije koja je u drugoj datoteci.

Za počeeak pomoću `joe` editora stvorimo novu datoteku `drugi.c` i u nju upišimo:

``` c
#include <stdio.h>
#include "hello.c"

int main() {
   hello();
   return 0;
}
```

Vidite odmah da se u glavnoj (main) funkciji poziva funkcija koju nismo definirali u tom programu, no također vidite i da smo na vrhu uključili i jednu `.c` datoteku. Upravo iz te datoteke pozivamo funkciju `hello()`.

___

Sada ćemo napisati hello program:

``` c
#include <stdio.h>

void hello() {
   printf("Hello, World!");
}
```
___

Međutim, nećemo tek tako uključiti `hello.c` datoteku u naš glavni program već ćemo napisati `header` datoteku (`hello.h`) i to na slijedeći način:

``` c
#ifndef HEADER_FILE /* Include guard */
#define HEADER_FILE

void hello(); /* Function declaration */

#endif
```

___

Sada prepravimo `drugi.c` i `hello.c` na sljedeći način:

`drugi.c`:
```c
#include <stdio.h>
#include "hello.h"  /* Include the header here, to obtain the function declaration */

int main() {
   hello(); /* Use the function here */
   return 0;
}
```

`hello.c`:
```c
#include <stdio.h>
#include "hello.h" /* Include the header (not strictly necessary here) */

void hello() { /* Function definition */
   printf("Hello, World!");
}
```
___

Kompajliranje je slično kao i u prvom slučaju:

``` bash
gcc -o drugi drugi.c hello.c -Wall
```
___

I na kraju pokrenemo program s:

``` bash
./drugi.out
```
___

Sve što nam preostaje je stvoriti `Makefile` datoteku u kojoj ćemo pravilima povezati prevođenje i izvršavanje ovih dvaju programa te na taj način omogućiti automatsko prevođenje i izvršavanje, bez da svaku put posebno unosimo naredbe za prevođenje i naredbe za izvršavanje.

Za stvaranje makefile datoteke upisujemo sljedeću naredbu:

``` bash
joe makefile
```
___

U jednoj `Makefile` datoteci može biti više pravila, pa ćemo ih sve zapisati u istu, no prije toga prođimo malo kroz `make` sintaksu.

___

#### Make varijable

Varijable se definiraju korištenjem znaka `$` ispred imena varijable koje se nalazi u zagradama.

``` bash
$(VARIJABLA) = nekakav_string
```
___

#### Make pravila

Pravila za prevodenje i povezivanje dana su u formi:

``` bash
naziv_pravila: ovisnosti
   naredbe
   ...
```

- `naziv_pravila` je najčešće isti kao i ime datoteke koja nastaje kao produkt izvođenja pravila
- `ovisnosti` (dependencies) predstavljaju datoteke koje su potrebne za izvršavanje pravila
- `maredbe` su skup postupaka koji se izvršavaju kada je pozvano određeno pravilo (redak u kojem je pravilo mora bit uvučen [`TAB`])

Postoje još i `implicitna pravila` koja se koriste za opisivanje nekih postupaka koji se izvode vrlo često.

Na primjer:

``` bash
.c.o:
  $(CC) $(CFLAGS) -c $<
```

je implicitno pravilo koje se koristi za dobivanje objektne datoteke (`.o`) iz datoteke `C` izvornog koda.

___

U našoj makefile datoteci ćemo prvo definirati varijable:

``` bash
CC = /usr/bin/gcc
CFLAGS = -Wall
TARGETS = prvi prvi2 drugi drugi2
```

- `CC` je putanja do `gcc` prevodioca
- `CFLAGS` opcije za `gcc` prevodioca (sadrži `-Wall`, koja omogućuje ispis grešaka na zaslonu)
- `TARGETS` imena izvršnih datoteka (koristi se u pravilima `all` i `clean`)

___

Nakon definiranja varijabli, potrebno je definirati defaultno (zadano) pravilo koje se izvršava ako `make` rutinu pozovemo bez imena pravila:

``` bash
default: drugi
all: $(TARGETS)
```

___

Sada ćemo definirati pravilo za sljedeće:
- prevođenje i povezivanje porgrama prvi i drugi korištenjem datoteka objektnog koda [`.o` datoteke]
- prevođenje i povezivanje programa prvi i drugi korištenjem datoteka izvornog koda (ulaz u `gcc` prevodioca [`.c` datoteke])







``` bash
CC = /usr/bin/gcc
CFLAGS = -Wall
TARGETS = prvi prvi2 drugi drugi2

default: drugi
all: $(TARGETS)

prvi: prvi.o
  $(CC) $(CFLAGS) prvi.o -o prvi

prvi2: prvi.c
  $(CC) $(CFLAGS) prvi.c -o prvi2

drugi: drugi.o hello.o
  $(CC) $(CFLAGS) drugi.o hello.o -o drugi

drugi2: drugi.c hello.c
  $(CC) $(CFLAGS) drugi.c hello.c \
  -o $(BIN)/drugi2

clean:
  rm -f *.o *~ a.out $(TARGETS)
.c.o:
  $(CC) $(CFLAGS) -c $<
```
___

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba1` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa).
