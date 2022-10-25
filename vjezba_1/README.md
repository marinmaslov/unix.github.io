# 🚀 Vježba 1: Prevođenje i povezivanje programa

# Zadatak 📋
U okviru vježbe potrebno je napisati program koji ispisuje pozdravnu poruku, na dva načina.

U prvom slučaju poruka se ispisuje iz glavnog programa. U drugom, funkcija koja ispisuje samu poruku nalazi se u zasebnoj datoteci izvornog koda (koristite se `.h` datotekom da ih povežete). Izvorni kod svih programa (datoteke .c) napišite korištenjem `joe` editora.

Nakon što ste napislati ta dva programa potrebno je napisati `makefile` u kojem ćete automatizirati proces prevođenja i izvršavanja vaših programa i to na dva načina:
1. Prevođenje i povezivanje prvog (i drugog; dakle bit će 2 pravila, jedno za svaki program) porgrama korištenjem datoteka objektnog koda (`.o` datoteke)
2. Prevođenje i povezivanje prvog (i drugog; dakle bit će 2 pravila, jedno za svaki program) programa korištenjem datoteka izvornog koda (ulaz u `gcc` prevodioca, tj. `.c` datoteke)

Sve što napišete testirajte, a eventualne grešake otklonite. U konačnici direktorij u kojem ste odradili vježbu komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute 🧭

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
1. Pozdravna poruka se ispisuje iz glavnog programa
2. Pozdravna poruka se ispisuje iz funkcije koja se nalazi u drugom programu

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

Sada nam se u driektoriju `vjezba1` nalazi datoteka `prvi.c`. Ona nije spremna za izvršavanje jer je prvo treba prevesti pomoću `gcc` prevodioca, a to radimo na sljedeći način:

``` bash
gcc -Wall prvi.c 
```
Argument `-Wall` uključuje ispisivanje upozorenja na naredbenu traku u slučaju da u vašem programu postoje greške. `gcc` predstavlja "GNU Compiler Collection", što je skup programerskih prevoditelja koji je nastao iz GNU projekta.

Nakon izvršavanja gornje naredbe dobit ćemo izvršnu datoteku `a.out`. `a` je zadano (default) ime, da smo htjeli postaviti neko svoje ime toj datoteci koristili bismo:

``` bash
gcc -Wall prvi.c -o prvi
```
`a.out` i `prvi` su identične datoteke.

Valja naglasiti se ovdje postupak prevođenja i povezivanja izveo zajedno, ako pak želimo ta dva postupka možemo razdvojiti na sljedeći načina:

``` bash
gcc -Wall -c prvi.c
gcc -Wall prvi.o -o prvi
```

Kod stvaranje izvršne datoteke (executable), automatski se postavljaju prava pristupa koja omogućavaju pokretanje datoteke, ako izvršite naredbu `ls -a` mozete vidjeti koja su to prava.

___

Kako bismo izvršili naš program potrebno je napraviti sljedeće:

``` bash
./a.out
```

ili

``` bash
./prvi
```
___

### 2. NAČIN

Drugi način je da se pozdravna poruka ispiše iz funkcije koja je u drugoj datoteci.

Za počeeak pomoću `joe` editora stvorimo novu datoteku `drugi.c` i u nju upisujemo:

``` c
#include <stdio.h>
#include "hello.h"

int main() {
   hello();
   return 0;
}
```

Vidite odmah da se u glavnoj (main) funkciji poziva funkcija koju nismo definirali u tom programu, no također vidite i da smo na vrhu uključili i jednu `.h` datoteku. Upravo nam ta datoteka omogućuje korištenje funkcije `hello()`.

___

Sada ćemo napisati hello program:

``` c
#include <stdio.h>

void hello() {
   printf("Hello, World!\n");
}
```
___

Mi smo u naš program uključili datoteku zaglavlja `hello.h`. Ona nam omogućuje da u `drugi.c` programu koristimo funkciju `hello()` definiranu u `hello.c` datoteci. Pišemo je na sljedeći način:

``` c
#ifndef HEADER_FILE /* Include guard */
#define HEADER_FILE

void hello(); /* Function declaration */

#endif
```
Na početku uključujemo nešto što se zove guard (linije `#ifndef`, `#define` i `#endif`). On u srži radi sljedeće: Ako ono što je obgrljeno ovim linijama nije već definirano tj. uključeno u naš `c` program, uključit sav kod koji ne nalazi između linija `#ifndef`, `#define` i `#endif`. Na ovaj način je osigurano da se `.h` datoteka ne uključi (include) u program više puta, a to znači da možemo i ne moramo koristiti te tri linije, no imajte na umu da ne korištenje istih može izazvati greške u vašem kodu ako slučajno više puta uključite isto zaglavlje.

Unutar zaglavlja je dovoljno samo deklarirati funkciju, zbog čega tu samo i piše: `void hello();`.

Sada kada smo napisali funkciju `hello.c` i zaglavlje `hello.h` pokazat ćemo kako isto zaglavlje uključiti u program iz prethodnog potpoglavlja (vidi kod ispod).

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
   printf("Hello, World!\n");
}
```

Primijetimo da ovdje `.h` datoteka nije obgrljena znakovima `<` i `>` već se nalazi u navodnicima. Razlog tome je što se naše `hello.h` zaglavlje ne nalazi u standardnom direktoriju svih `c/c++` zaglavlja.
___

Kompajliranje je slično kao i u prvom slučaju, samo što ovdje nabrajamo sve `.c` datoteke:

``` bash
gcc -Wall drugi.c hello.c -o drugi
```
___

I na kraju pokrenemo program s:

``` bash
./drugi
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

Varijable se definiraju VELIKIM slovima.

``` bash
VARIJABLA = nekakav_string
```

Pozivanje varijable vrši se korištenjem znaka `$` ispred imena varijable koje se nalazi u zagradama:

``` bash
$(VARIJABLA)
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

___

Dopišimo (u datoteku makefile) za početak pravila za prevođenje i povezivanje porgrama prvi i drugi korištenjem datoteka objektnog koda:

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

___

Dopišimo i pravila za prevođenje i povezivanje programa prvi i drugi korištenjem datoteka izvornog koda:

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
   
prvi1: prvi.c
   $(CC) $(CFLAGS) prvi.c -o prvi2
   
drugi2: drugi.c hello.c
   $(CC) $(CFLAGS) drugi.c hello.c \
   -o $(BIN)/drugi2
```

___

Na kraju moramo dodati proceduru za brisanje privremenih, objektnih i izvršnih datoteka, jer ne želimo nikakav `trash` (smeće) u našoj mapi koje bi nam moglo poremetiti buduća izvršavanja:

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
   
prvi1: prvi.c
   $(CC) $(CFLAGS) prvi.c -o prvi2
   
drugi2: drugi.c hello.c
   $(CC) $(CFLAGS) drugi.c hello.c \
   -o $(BIN)/drugi2
   
clean:
   rm -f *.o*~ a.out $(TARGETS)
```

te implicitno pravilo za prevođenje datoteka `C` izvornog koda u objektne datoteke `.o`:

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
   
prvi2: prvi.c
   $(CC) $(CFLAGS) prvi.c -o prvi2
   
drugi2: drugi.c hello.c
   $(CC) $(CFLAGS) drugi.c hello.c \
   -o drugi2
   
clean:
   rm -f *.o*~ a.out $(TARGETS)
   
.c.o:
   $(CC) $(CFLAGS) -c $<
```
___

Sada pokušajte izvršiti neko pravilo korištenjem sljedeće naredbe:

``` bash
make ime_pravila_kojeg_smo_definirali
```
___

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba1` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
