# Standardni ulaz `stdin` i standardni izlaz `stdout`

U C jeziku, standardni ulaz (`stdin`) i izlaz (`stdout`) predstavljaju osnovni način za interakciju s korisnikom i za ispisivanje rezultata izvođenja programa. Standardni ulaz predstavlja unos podataka, obično s tipkovnice, dok standardni izlaz predstavlja ispis rezultata na ekran.

---

## 1. Standardni Ulaz (`stdin`)

Standardni ulaz (`stdin`) omogućuje vašem programu da prima ulazne podatke iz različitih izvora. Uobičajeni izvor je tipkovnica, ali može biti i drugi proces ili datoteka.

### 1.1 Čitanje unosa korisnika

```c
#include <stdio.h>

int main() {
    int broj;

    printf("Unesite cijeli broj: ");
    scanf("%d", &broj);

    printf("Unijeli ste: %d\n", broj);

    return 0;
}
```

U ovom primjeru, `scanf` se koristi za čitanje cijelog broja (`%d`) s tipkovnice.

---

## 2. Standardni Izlaz (`stdout`)

Standardni izlaz (`stdout`) omogućuje ispisivanje podataka na ekran.

### 2.1 Ispisivanje na zaslon

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");

    int broj = 42;
    printf("Vrijednost broja: %d\n", broj);

    return 0;
}
```

U ovom primjeru, `printf` se koristi za ispisivanje poruka na ekran. Formatirani stringovi omogućuju elegantno kombiniranje teksta i varijabli.

---

## 3. Kombiniranje s naredbenim retkom

C programi mogu koristiti argumente naredbenog retka kako bi dinamički utjecali na izvođenje programa. Argumenti se proslijeđuju funkciji `main`.

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Koristenje: %s <ime>\n", argv[0]);
        return 1;
    }

    printf("Hello, %s!\n", argv[1]);

    return 0;
}
```

Ovaj program očekuje da korisnik unese ime kao argument naredbenog retka (`argv[1]`).

---

## 4. Rad s datotekama

U C jeziku, možete koristiti standardne funkcije za rad s datotekama, poput `fopen`, `fprintf`, `fscanf`, `fclose` za rad s datotekama umjesto standardnog ulaza i izlaza.

### 4.1 Čitanje iz Datoteke

```c
#include <stdio.h>

int main() {
    FILE *file = fopen("datoteka.txt", "r");
    if (file == NULL) {
        perror("Otvaranje datoteke nije uspjelo");
        return 1;
    }

    char redak[100];
    while (fgets(redak, sizeof(redak), file) != NULL) {
        printf("%s", redak);
    }

    fclose(file);

    return 0;
}
```
