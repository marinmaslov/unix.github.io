# Vježba 3 - rješenja
U nastavku se nalaze rješenja vježbe3.

# Program `prvi.c`
U prvom prgoramu ispisujemo argumente s naredbenog retka koje korisnik unese.

```c
#include <stdio.h>

int main() {
    char buffer[1000];

    printf("Unesite tekst:\n");

    while (fgets(buffer, sizeof(buffer), stdin) != NULL) {
        printf("Ponovni ispis: %s", buffer);
    }

    return 0;
}
```

Ovaj program je napisan u jeziku C i ima jednostavnu svrhu - čita tekst koji korisnik unese sa tastature (standardni ulaz, stdin) i zatim ispisuje taj tekst na ekran. Evo detaljnog objašnjenja koda:

```c
#include <stdio.h>
```

Ova linija uključuje standardnu biblioteku za ulazno/izlazne operacije, uključujući funkcije za rad sa standardnim ulazom (`stdin`) i ispisom na ekran (`printf`).

```c
int main() {
```

Ovo označava početak funkcije `main`, glavnog programa koji se izvršava kada se program pokrene.

```c
    char buffer[1000];
```

Ovdje se deklarira niz karaktera (`char buffer[1000]`) sa nazivom "buffer" koji može sadržavati do 1000 karaktera. Ovaj niz će se koristiti za čuvanje teksta koji korisnik unese.

```c
    printf("Unesite tekst:\n");
```

Koristi se `printf` funkcija za ispisivanje poruke "Unesite tekst:\n" na ekran.

```c
    while (fgets(buffer, sizeof(buffer), stdin) != NULL) {
```

Ovo je petlja (`while`) koja se izvršava sve dok funkcija `fgets` vraća nešto različito od NULL. `fgets` čita red teksta iz standardnog ulaza (`stdin`) i sprema ga u `buffer`. `sizeof(buffer)` predstavlja veličinu niza `buffer`, što osigurava da nećemo premašiti granice niza prilikom čitanja teksta. Ako je `fgets` uspješno pročitala red teksta, petlja se izvršava.

```c
        printf("Ponovni ispis: %s", buffer);
```

Unutar petlje, koristi se `printf` za ispisivanje poruke "Ponovni ispis: " i zatim sadržaj niza `buffer` (tekst koji je korisnik unio).

```c
    }

    return 0;
```

Na kraju programa, `return 0;` označava uspješan završetak programa, tj. nula se vraća operativnom sistemu kao kod uspješnog završetka programa.

Ova kombinacija funkcija omogućava korisniku da unese tekst s tastature, a zatim se taj tekst ispisuje na ekran. Petlja se izvršava sve dok korisnik ne unese EOF (End-of-File), obično se postiže kombinacijom tipki poput Ctrl+D (u Unix/Linux okolinama) ili Ctrl+Z (u Windows okolinama).

---

# Program `drugi.c`
U drugom prgoramu ispisujemo sadržaj datoteka koje korisnik unese kao argumente.

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Koristenje: %s <ime_datoteke1> <ime_datoteke2> ... <ime_datotekeN>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    for (int i = 1; i < argc; i++) {
        FILE *file = fopen(argv[i], "r");
        if (file == NULL) {
            perror("fopen");
            exit(EXIT_FAILURE);
        }

        char buffer[1000];
        while (fgets(buffer, sizeof(buffer), file) != NULL) {
            printf("%s", buffer);
        }

        fclose(file);
    }

    return 0;
}
```

Ovaj program čini sljedeće:

1. **Uključuje potrebne biblioteke:**
   ```c
   #include <stdio.h>
   #include <stdlib.h>
   ```
   Ove biblioteke su uključene kako bi program mogao koristiti funkcije za ulazno/izlazne operacije (`printf`, `fopen`, `fclose`, `fgets`) i funkcije za rad s dinamičkom memorijom (`exit`).

2. **Definira `main` funkciju:**
   ```c
   int main(int argc, char *argv[]) {
   ```
   - `argc` je broj argumenata naredbenog retka.
   - `argv` je niz pokazivača na stringove koji predstavljaju argumente naredbenog retka.

3. **Provjera broja argumenata:**
   ```c
   if (argc < 2) {
       printf("Koristenje: %s <ime_datoteke1> <ime_datoteke2> ... <ime_datotekeN>\n", argv[0]);
       exit(EXIT_FAILURE);
   }
   ```
   Ako program nije pozvan s dovoljno argumenata (barem dva - ime izvršne datoteke i ime datoteke čiji sadržaj će ispisati), ispisuje se uputa za korištenje te program završava s greškom pomoću funkcije `exit(EXIT_FAILURE)`.

4. **Petlja za obradu svake datoteke:**
   ```c
   for (int i = 1; i < argc; i++) {
   ```
   Petlja kreće od indeksa 1 jer je `argv[0]` ime izvršne datoteke, a želimo raditi samo s argumentima koji predstavljaju imena datoteka čiji sadržaj je potrebno ispisati.

5. **Otvorenje datoteke i provjera uspjeha:**
   ```c
   FILE *file = fopen(argv[i], "r");
   if (file == NULL) {
       perror("fopen");
       exit(EXIT_FAILURE);
   }
   ```
   Otvara datoteku s imenom danom u trenutnom argumentu naredbenog retka u režimu čitanja ("r"). Ako otvaranje nije uspjelo, ispisuje se greška pomoću funkcije `perror` i program završava s greškom.

6. **Čitanje i ispisivanje sadržaja datoteke:**
   ```c
   char buffer[1000];
   while (fgets(buffer, sizeof(buffer), file) != NULL) {
       printf("%s", buffer);
   }
   ```
   Čita liniju po liniju iz datoteke pomoću `fgets` funkcije i ispisuje je na zaslon pomoću `printf`.

7. **Zatvaranje datoteke nakon završetka čitanja:**
   ```c
   fclose(file);
   ```
   Zatvara trenutno otvorenu datoteku.

8. **Završetak programa:**
   ```c
   return 0;
   ```
   Program završava sa statusom "uspješno" (`0`).

---

# Program `treci.c`
U trecem prgoramu ispisujemo ili sadržaj datoteka ili unos korisnika, ako nema argumenata.

``` c
#include <stdio.h>
#include <stdlib.h>

int ispisi_sadrzaj_datoteke(char *ime_datoteke) {
    FILE *file = fopen(ime_datoteke, "r");
    if (file == NULL) {
        perror("fopen");
        return -1;
    }

    char buffer[1000];
    while (fgets(buffer, sizeof(buffer), file) != NULL) {
        printf("%s", buffer);
    }

    fclose(file);
    return 0;
}

int main(int argc, char *argv[]) {
    if (argc < 2) {
        // Ako nema datoteka navedenih kao argumenti, koristi se ponovni ispis unosa korisnika
        return system("./ponovni_ispis");
    }

    for (int i = 1; i < argc; i++) {
        if (ispisi_sadrzaj_datoteke(argv[i]) == -1) {
            exit(EXIT_FAILURE);
        }
    }

    return 0;
}
```

Ovaj program čini nekoliko stvari. Evo detaljnog objašnjenja:

1. **Uključivanje potrebnih biblioteka:**
   ```c
   #include <stdio.h>
   #include <stdlib.h>
   ```
   Ove biblioteke su neophodne za korištenje funkcija za rad sa standardnim ulazno/izlaznim operacijama (`printf`, `fopen`, `fclose`, `fgets`) i funkcija za upravljanje procesima (`system`, `exit`).

2. **Definiranje funkcije `ispisi_sadrzaj_datoteke`:**
   ```c
   int ispisi_sadrzaj_datoteke(char *ime_datoteke) {
   ```
   - Funkcija prima ime datoteke kao argument.

3. **Otvorenje datoteke i provera uspeha:**
   ```c
   FILE *file = fopen(ime_datoteke, "r");
   if (file == NULL) {
       perror("fopen");
       return -1;
   }
   ```
   Funkcija pokušava otvoriti datoteku čije ime je proslijeđeno kao argument. Ako otvaranje nije uspjelo, ispisuje se poruka o grešci pomoću `perror` i funkcija vraća -1 kao indikator neuspjeha.

4. **Čitanje i ispisivanje sadržaja datoteke:**
   ```c
   char buffer[1000];
   while (fgets(buffer, sizeof(buffer), file) != NULL) {
       printf("%s", buffer);
   }
   ```
   Funkcija čita datoteku liniju po liniju pomoću `fgets` funkcije i ispisuje sadržaj na zaslon pomoću `printf`.

5. **Zatvaranje datoteke nakon završetka čitanja:**
   ```c
   fclose(file);
   ```
   Zatvara trenutno otvorenu datoteku.

6. **Povratak rezultata iz funkcije:**
   ```c
   return 0;
   ```
   Funkcija se završava s povratnom vrijednošću 0 kako bi označila uspješno izvršenje.

7. **Definiranje `main` funkcije:**
   ```c
   int main(int argc, char *argv[]) {
   ```
   - `argc` je broj argumenata naredbenog retka.
   - `argv` je niz pokazivača na stringove koji predstavljaju argumente naredbenog retka.

8. **Provjera broja argumenata:**
   ```c
   if (argc < 2) {
       // Ako nema datoteka navedenih kao argumenti, koristi se ponovni ispis unosa korisnika
       return system("./ponovni_ispis");
   }
   ```
   Ako nema dovoljno argumenata (barem dva - ime izvršne datoteke i ime datoteka čiji sadržaj će se ispisati), program poziva vanjski program `ponovni_ispis` pomoću funkcije `system`.

9. **Petlja za obradu svake datoteke:**
   ```c
   for (int i = 1; i < argc; i++) {
       if (ispisi_sadrzaj_datoteke(argv[i]) == -1) {
           exit(EXIT_FAILURE);
       }
   }
   ```
   Petlja prolazi kroz sve argumente komandne linije, koji predstavljaju imena datoteka. Za svaku datoteku poziva se funkcija `ispisi_sadrzaj_datoteke`. Ako ta funkcija vrati -1, program završava s greškom pomoću funkcije `exit`.

10. **Završetak programa:**
    ```c
    return 0;
    ```
    `main` funkcija se završava s povratnom vrijednošću 0, što označava uspješno izvršenje programa.

---

# Program `cetvrti.c`

U četvrtom prgoramu radimo isto što i u trećem, samo su funkcije za ispisivanje u drugoj `.c` datoteci.

1. **header_datoteka.h**

```c
// header_datoteka.h
#ifndef HEADER_DATOTEKA_H
#define HEADER_DATOTEKA_H

int ispisi_sadrzaj_datoteke(char *ime_datoteke);

#endif
```

2. **ispis_sadrzaja.c**

```c
// ispis_sadrzaja.c
#include <stdio.h>
#include "header_datoteka.h"

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Koristenje: %s <ime_datoteke1> <ime_datoteke2> ... <ime_datotekeN>\n", argv[0]);
        return 1;
    }

    for (int i = 1; i < argc; i++) {
        if (ispisi_sadrzaj_datoteke(argv[i]) == -1) {
            return 1;
        }
    }

    return 0;
}
```

3. **ispis_sadrzaja.h**

```c
// ispis_sadrzaja.h
#ifndef ISPIS_SADRZAJA_H
#define ISPIS_SADRZAJA_H

int ispisi_sadrzaj_datoteke(char *ime_datoteke);

#endif
```

4. **ispis_sadrzaja_definicija.c**

```c
// ispis_sadrzaja_definicija.c
#include <stdio.h>
#include "ispis_sadrzaja.h"

int ispisi_sadrzaj_datoteke(char *ime_datoteke) {
    FILE *file = fopen(ime_datoteke, "r");
    if (file == NULL) {
        perror("fopen");
        return -1;
    }

    char buffer[1000];
    while (fgets(buffer, sizeof(buffer), file) != NULL) {
        printf("%s", buffer);
    }

    fclose(file);
    return 0;
}
```

U ovom primjeru, `header_datoteka.h` sadrži deklaraciju funkcije `ispisi_sadrzaj_datoteke`, a zatim se ta deklaracija koristi u `ispis_sadrzaja.c` kako bi se omogućila povezanost između `.c` datoteka. Također, definicija funkcije je odvojena u zasebnu `.c` datoteku `ispis_sadrzaja_definicija.c`.

Kada koristite header datoteke i odvojene definicije funkcija, pobrinite se da uključite sve relevantne datoteke u naredbu za prevođenje i povezivanje!
