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
#ifndef HEADER_FILE
#define HEADER_FILE

void hello();

#endif
```

___

Sada prepravimo `drugi.c` i `hello.c` na sljedeći način:

`drugi.c`:
```c
#include <stdio.h>
#include "hello.h"

int main() {
   hello();
   return 0;
}
```

`hello.c`:
```c
#include <stdio.h>
#include "hello.h"

void hello() {
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

Sve što nam preostaje je stvoriti `Makefile` datoteku u kojoj ćemo pravilima povezati prevođenje i izvršavanje ovih dvaju programa.

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
