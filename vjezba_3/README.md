# 🚀 Vježba 3: Standardni ulaz i izlaz

## Zadatak 📋

Zadatak ove vježbe je napraviti 4 programa.

<b>Prvi program `prvi.c`</b> se pokreće bez argumenata naredbenog retka, a sve što korisnik upiše ispisuje se na terminalu.

<b>Drugi program `drugi.c`</b> se pokreće tako da korisnik unese jednu ili više datoteka kao argumente naredbenog retka. Program zatim otvara redom te datoteke te njihov sadržaj ispisuje na terminal.

<b>Treći program `treci.c`</b> napišite kao kombinaciju prva dva programa i to na način da ako korisnik unese jednu ili više datoteka, program sadržaj tih datoteka redom ispisuje na terminal, a u slučaju da argv(i) ne budu datoteke program ispisuje sve što je korisnik utipkao.

<b>Četvrti program `cetvrti.c`</b> napišite tako da treći program prepravite na način da je funkcija koja ispisuje sadržaj datoteka napisana u zasebnoj datoteci izvornog koda `ispisi.c` (dakle, koristit ćete `header` datoteku `ispisi.h` preko koje ćete tu funkciju uključit u vaš program).

Nakon toga napišite `Makefile` datoteku s <b>pravilima</b> za prevođenje i povezivanje za sva 4 programa.

U konačnici direktorij `vjezba3` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute [🧭](rjesenja/README.md)

### 1. Standardni ulaz i izlaz

U C jeziku, standardni ulaz (stdin) i standardni izlaz (stdout) predstavljaju osnovne tokove podataka za unos i izlaz. Ovi tokovi podataka su povezani s tipkovnicom i zaslonom, što omogućava programima da čitaju korisnički unos i ispisuju rezultate na zaslon.

Evo osnovnih informacija i primjera korištenja standardnog ulaza i izlaza u C jeziku:

#### 1.1 Standardni ulaz (stdin)
- `stdin` je predefinirani pokazivač na FILE strukturu koja predstavlja standardni ulaz.
- Za čitanje podataka sa standardnog ulaza, koristi se funkcija `scanf` ili `fgets`.

Primjer čitanja cijelog reda sa standardnog ulaza:
```c
#include <stdio.h>

int main() {
    char buffer[100];
    printf("Unesite neki tekst: ");
    fgets(buffer, sizeof(buffer), stdin);
    printf("Uneseni tekst: %s", buffer);
    return 0;
}
```

#### 1.2 Standardni izlaz (stdout)
- `stdout` je predefinirani pokazivač na FILE strukturu koja predstavlja standardni izlaz.
- Za ispisivanje podataka na standardni izlaz koristi se funkcija `printf` ili `puts`.

Primjer ispisivanja poruke na ekran:
```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

#### 1.3 Standardna greška (stderr)
- `stderr` je predefinirani pokazivač na FILE strukturu koja predstavlja standardnu grešku.
- Za ispisivanje grešaka koristi se funkcija `fprintf` s `stderr` kao argument.

Primjer ispisivanja greške:
```c
#include <stdio.h>

int main() {
    fprintf(stderr, "Ovo je poruka o grešci!\n");
    return 1; // Povratna vrijednost različita od nule označava grešku
}
```

Osim uputa za standardni ulaz i izlaz, prisjetite se i: [Datoteke u C jeziku](../dodatno/datoteke.md).

---

```
💡 Ova vježba je spoj svih dosadašnjih vježbi (neki zadaci su čak i isti) pa se slobodno koristite i uputama od prethodnih vježbi!
```

### 2. Pomoć za prvi zadatak

Prvi program je sličan kao i [prvi primjer iz vježbe 2](https://marinmaslov.github.io/unix.github.io/vjezba_2/primjeri), samo je potrebno preurediti funkciju `printf(...)` na način da iterirate kroz argumente naredbenog retka te iste ispisujete.

```
💡 Broj argumenata naredbenog retka se nalazi u argc, a sami argumenti su u nizu argv.
```

---

### 3. Pomoć za drugi zadatak

Drugi program je isti kao i četvrti program u vježbi 2. Sjetite se koju naredbu možete koristiti da program, s novim imenom, kopirate u direktorij `vjezba3`.

---

### 4. Pomoć za treći zadatak

Kada napišete prvi i drugi program, kombinacijom njihovih kodova možete dobiti treći pregram.

```
💡 Hint: Ključ je u grananju!
```

---

### 5. Pomoć za četvrti zadatak

Za četvrti program koristi znanje stečeno u [vježbi 1](https://marinmaslov.github.io/unix.github.io/vjezba_1/)

---

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba3` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
