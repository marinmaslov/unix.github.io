## 🚀 Vježba 2: Primjeri programskog koda

# Funkcija main i argumenti naredbenog retka 💻

Kod pokretanja programa, ljuska iz koje se program pokreće može funkciji main proslijediti skup argumenata narebenog retka. Ove argumente program prima putem argumenata funkcije main:
- `int argc`: broj argumenata naredbenog retka, uključujući i ime programa
- `char *argv[ ]`: argumenti naredbenog retka

Kako ne bi gubili vrijeme na prepisivanje danih primjera, možete ih kopirati u svoj direktorij `vježba2` (dakle naredbe izvršite dok se nalazite u vašem direktoriju `vjazba2`) pomoću naredbi:
- `cp /home/mmaslo00/vjezba2/primjeri/prvi.c prvi.c`
- `cp /home/mmaslo00/vjezba2/primjeri/drugi.c drugi.c`
- `cp /home/mmaslo00/vjezba2/primjeri/treci.c treci.c`

Također kopirajte i `test.txt` koju ćete koristiti u programu `drugi.c`:
- `cp /home/mmaslo00/vjezba2/primjeri/test.txt test.txt`

___

Za bolje razumjevanje primjera, pročitajte i: (Datoteke u C jeziku)[../../dodatno/datoteke.md]

# Primjer 1. 📋
Sljedeći program ispisuje cijelu listu argumenata naredbenog retka


```c
#include <stdio.h>
int main(int argc, char *argv[]) {
  int i;
  
  for (i=0; i<argc; i++)
    printf("argv[%d]: %s\n", i, argv[i]);
    
  return(0);
}
```

Objašnjenje koda:

- `#include <stdio.h>`: Ova linija uključuje zaglavlje (header) `<stdio.h>`, koje omogućuje korištenje funkcija za ulaz i izlaz, kao što su `printf`.
- `int main(int argc, char *argv[])`: Ovo je definicija glavne funkcije programa, `main()`. Funkcija `main()` ima dva argumenta:
  - `int argc`: Predstavlja broj argumenata koji su proslijeđeni programu iz naredbene linije. `argc` (što znači "argument count") je cijeli broj (integer) i obično je barem 1, jer je prvi argument uvijek biti naziv programa.
  - `char *argv[]`: Predstavlja niz (array) pokazivača na stringove (nizove znakova - `char`). Ovi znakovni nizovi predstavljaju argumente programa, uključujući i naziv programa koji se nalazi u `argv[0]`.
- `int i;`: Ovdje se deklarira varijabla `i` koja će se koristiti kao brojač u `for` petlji.
- `for (i=0; i<argc; i++)`: Ovo je petlja `for` koja prolazi kroz sve argumente programa. Počinje s `i` postavljenim na 0 i povećava se za svaku iteraciju dok ne postane jednak `argc` (broju argumenata).
- `printf("argv[%d]: %s\n", i, argv[i]);`: Ovdje se koristi funkcija `printf` kako bi se ispisao svaki argument. `%d` se koristi za ispisivanje integer vrijednosti `i`, a `%s` se koristi za ispisivanje znakovnog niza (string) `argv[i]`, što predstavlja određeni argument programa. `argv[i]` omogućava pristup pojedinom argumentu prema njihovom indeksu `i`.
-- `return(0);`: Ova linija označava kraj glavne funkcije `main()` i vraća vrijednost 0 kao status izlaza programa. Vrijednost 0 obično označava da je program završio bez grešaka.

___
# Primjer 2. 📋
Sljedeći program čita sadržaj datoteke i ispisuje ga. Ime datoteke se zadaje kao argument naredenog retka.

``` c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
  int fd1, n;
  char sl;
   
  if (argc < 2) {
    printf("Koristenje: citaj <ime_datoteke>\n");
    exit(0);
  }
                    
  fd1=open(argv[1], O_RDONLY);
  if (fd1 < 0) {
    perror("open");
    exit(-1);
  }
                                    
  while((n=read(fd1, &sl, 1)) > 0) {
    write(STDOUT_FILENO, &sl, 1);
  } 
                                              
  if (n == -1) {
    perror("read");
  }
                                                          
  close(fd1);
  exit(0);
}
```

Objašnjenje koda:

- `#include` redovi: Ovi redovi uključuju različita zaglavlja (headers) potrebna za korištenje funkcija za rad s datotekama (`<sys/types.h>`, `<sys/stat.h>`, `<fcntl.h>`), funkcije za unos i izlaz (`<stdio.h>`), funkcije za upravljanje procesima (`<stdlib.h>`) i funkcije za rad s datotekama i descriptorima (`<unistd.h>`).
- `int main(int argc, char *argv[])`: Ovo je definicija glavne funkcije programa, `main()`. Funkcija `main()` ima dva argumenta:
  - `int argc`: Broj argumenata koji su proslijeđeni programu iz naredbene linije.
  - `char *argv[]`: Niz pokazivača na znakovne nizove (strings) koji predstavljaju argumente programa.
- `int fd1, n;`: Ovdje se deklariraju dvije varijable. `fd1` će se koristiti kao file descriptor za otvaranje datoteke, a `n` za čitanje i provjeru rezultata čitanja.
- `char sl;`: Ovdje se deklarira varijabla `sl` koja će se koristiti za čitanje jednog znaka iz datoteke.
- Grana `if (argc < 2)`: Ova linija provjerava broj argumenata predanih programu. Ako je manji od 2, program će ispisati poruku i zatvoriti se. To znači da program zahtijeva barem jedan argument koji predstavlja ime datoteke koju treba čitati.
- `fd1=open(argv[1], O_RDONLY);`: Ova linija otvara datoteku čije ime je proslijeđeno kao prvi argument programa (`argv[1]`). Argument `O_RDONLY` predstavlja zasatavicu koja funkciji govori da datoteku otvori u `read-only` načinu, više o otvaranju datoteka u c jeziku možete pročitati [ovdje](https://www.geeksforgeeks.org/input-output-system-calls-c-create-open-close-read-write/). Funkcija `opne(...)` vraća `-1` ako se prilikom čitanja dogodila greška, zbog čega se u grani `if (fd1 < 0)` se provjerava je li datoteka uspješno otvorena.
  - Ako otvaranje nije uspjelo, program će ispisati poruku o grešci pomoću `perror()` funkcije i zatvoriti se s negativnim statusom izlaza `exit(-1)`.
- `while((n=read(fd1, &sl, 1)) > 0)`: Ova petlja čita sadržaj datoteke znak po znak koristeći funkciju `read()`. Svaki pročitani znak se ispisuje na standardni izlaz pomoću funkcije `write()`. Petlja se izvršava sve dok `read()` vraća pozitivan broj znakova (što znači da ima još znakova za čitanje). Više o funkcijama `read()` i `write()` možete pročitati [ovdje](https://www.geeksforgeeks.org/input-output-system-calls-c-create-open-close-read-write/).
- `if (n == -1)`: Nakon završetka petlje, program provjerava rezultat čitanja. Ako `read()` vrati -1, to znači da je došlo do greške tijekom čitanja, pa program ispisuje odgovarajuću poruku o grešci pomoću `perror()` funkcije.
- `close(fd1);`: Nakon što je datoteka u potpunosti pročitana, program zatvara file descriptor pomoću funkcije `close()`.
- `exit(0);`: Na kraju programa, koristi se `exit()` funkcija kako bi se program zatvorio i vratio status izlaza 0, što znači da je program završio bez grešaka. Umjesto funkcije `exit()` moglo se koristiti i return `0` ili `-1`.

___
# Primjer 3. 📋
Sljedeći program ima funkcionalnost UNIX naredbe cat. Program ispisuje sadržaj svih datoteka čija su imena zadana kao argumenti naredbenog retka. Ukoliko niti jedna datoteka nije zadana, program kopira standardni ulaz na standardni izlaz.

``` c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>

int rw(int fdin, int fdout) {
  int n;
  char c;
  
  while ((n=read(fdin, &c, 1)) > 0)
    write(fdout, &c, 1);
    
  return n;
}

int main(int argc, char *argv[]) {
  int fd, k;
 
  for (k=1; k<argc; k++) {
    fd = open(argv[k], O_RDONLY);
    if (fd == -1) {
      perror("open");
      return -1;
    }
    rw(fd, STDOUT_FILENO);
    close(fd);
  }

  if (k == 1)
    rw(STDIN_FILENO, STDOUT_FILENO);

  return 0;
}
```

Objašnjenje koda:

Ovaj C program čini nekoliko stvari pomoću sustavskih poziva za rad s datotekama i unosom/ispisom:

#### 1. Uključuje potrebna zaglavlja za sustavske pozive i funkcionalnosti:

```c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>
```

#### 2. Definira funkciju `rw` koja kopira sadržaj iz jednog file deskriptora (`fdin`) u drugi file deskriptor (`fdout`). File deskriptor može biti otvoren za čitanje (`O_RDONLY`) ili pisanje (`STDOUT_FILENO` predstavlja standardni izlaz):

```c
int rw(int fdin, int fdout) {
  int n;
  char c;
  
  while ((n = read(fdin, &c, 1)) > 0)
    write(fdout, &c, 1);
    
  return n;
}
```

- `read(fdin, &c, 1)` čita jedan znak iz `fdin` i sprema ga u varijablu `c`.
- `write(fdout, &c, 1)` piše taj znak u `fdout`.
- Petlja se izvršava dok se ne dosegne kraj datoteke (`read` vraća 0), a broj pročitanih znakova se vraća iz funkcije.

#### 3. U glavnoj funkciji `main` program radi sljedeće stvari:

- Iterira kroz argumente naredbenog retka (`argv`) počevši od drugog argumenta (`k=1`), što su nazivi datoteka koje treba kopirati.
- Svaku datoteku otvara za čitanje koristeći `open` sustavski poziv. Ako otvaranje datoteke ne uspije (`fd == -1`), ispisuje se poruka o pogrešci pomoću `perror` i program se završava s kodom -1.
- Zatim se poziva funkcija `rw` kako bi se kopirao sadržaj datoteke u standardni izlaz (`STDOUT_FILENO`) i zatvara se file deskriptor.
- Ako ne postoje argumenti naredbenog retka (`k == 1`), program čita sa standardnog ulaza i ispisuje na standardni izlaz.

#### 4. Program završava s povratnim kodom 0 ako sve prođe bez grešaka.

Ovaj program omogućuje kopiranje sadržaja datoteka na standardni izlaz ili kopiranje sadržaja s standardnog ulaza na standardni izlaz, ovisno o argumentima naredbenog retka koje mu se pruže.
