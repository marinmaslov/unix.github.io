# 📃 Datoteke u C jeziku

Rad s datotekama u C jeziku uključuje otvaranje datoteka, čitanje sadržaja i ispisivanje podataka.

## 1. Otvaranje datoteke
- `open()` funkcija se koristi za otvaranje datoteke.
- Prima dva argumenta: naziv datoteke i zastavice koje određuju način otvaranja datoteke (npr. samo za čitanje, samo za pisanje, ili i za čitanje i pisanje).
- Ako je otvaranje uspješno, `open()` vraća file descriptor (file opisnik), inače vraća -1.
  
Primjer koda:
```c
#include <fcntl.h>

int fd = open("datoteka.txt", O_RDONLY);
if (fd == -1) {
    perror("open");
    // Obrada greške
} else {
    // Rad s otvorenom datotekom
    // ...
    close(fd); // Nakon završetka rada, datoteku treba zatvoriti
}
```

---

## 2. Čitanje sadržaja datoteke

### 2.1 Funkcija `fgets()`
```c
#include <stdio.h>

int main() {
    FILE *file;
    char buffer[100];

    file = fopen("naziv_datoteke.txt", "r");
    if (file == NULL) {
        printf("Greška pri otvaranju datoteke!\n");
        return 1;
    }

    while (fgets(buffer, sizeof(buffer), file) != NULL) {
        printf("%s", buffer);
    }

    fclose(file);
    
    return 0;
}
```

- `fgets(buffer, sizeof(buffer), file)`: Ova funkcija čita redak iz datoteke u niz `buffer`. Ako nema više redaka za čitanje, vraća `NULL`.
- `while (fgets(buffer, sizeof(buffer), file) != NULL) { ... }`: Petlja čita redak po redak iz datoteke i ispisuje ga na ekran.

Osim za datoteke, funkcija `fgets()` se može koristiti i za dohvaćanje sadržaja sa standardnog ulaza:

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

### 2.2 Funkcija `read()`
- `read()` se koristi za čitanje podataka iz otvorene datoteke.
- Prima tri argumenta: file descriptor, buffer u koji će se pročitani podaci smjestiti i broj bajtova koje treba pročitati.
- Vraća broj pročitanih bajtova ili -1 ako dođe do greške.

Primjer koda:
```c
#include <unistd.h>

char buffer[100];
int bytesRead = read(fd, buffer, sizeof(buffer));
if (bytesRead == -1) {
    perror("read");
    // Obrada greške
} else {
    // Rad s pročitanim podacima
}
```

## 3. Ispisivanje podataka u datoteku
```c
#include <stdio.h>

int main() {
    FILE *file;

    file = fopen("izlaz.txt", "w");
    if (file == NULL) {
        printf("Greška pri otvaranju datoteke!\n");
        return 1;
    }

    fprintf(file, "Ovo je tekst koji se ispisuje u datoteku.\n");

    fclose(file);
    
    return 0;
}
```

- `fprintf(file, "Ovo je tekst koji se ispisuje u datoteku.\n");`: Ova funkcija ispisuje formatirane podatke u datoteku. U ovom slučaju, ispisuje se tekst u otvorenu datoteku.

---

## 4. Rad s binarnim datotekama
```c
#include <stdio.h>

struct Student {
    char ime[30];
    int godine;
};

int main() {
    FILE *file;
    struct Student student;

    file = fopen("studenti.dat", "wb");
    if (file == NULL) {
        printf("Greška pri otvaranju datoteke!\n");
        return 1;
    }

    fwrite(&student, sizeof(struct Student), 1, file);

    fclose(file);

    return 0;
}
```

- `struct Student`: Definira strukturu podataka koja predstavlja informacije o studentu (u ovom slučaju ime i godine).

- `fwrite(&student, sizeof(struct Student), 1, file)`: Pisanje strukture podataka (`student`) u binarnu datoteku (`studenti.dat`). Funkcija `fwrite` uzima adresu strukture, veličinu strukture (`sizeof(struct Student)`) i broj struktura koje se pišu (u ovom slučaju 1).

---

Ove funkcije čine osnovu za rad s datotekama u C jeziku. U praksi, uvijek treba paziti na rukovanje greškama i osigurati pravilno zatvaranje datoteka nakon završetka rada.
