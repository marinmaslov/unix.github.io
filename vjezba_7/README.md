# 🚀 Vježba 7: Datoteke i direktoriji

## Zadatak 📋

Zadatak ove vježbe je napisati program koji ima djelomičnu funkcionalnost naredbe `ls -al`. Program se poziva imenom direktorija ili bez njega.

- Ako je kao argument naredbenog retka zadano ime direktorija, za sve datoteke u navedenom direktoriju ispisuju se svojstva. Pri tome je potrebno ispisati sve informacije o datotekama i direktorijima koje bi se dobile pozivanjem naredbe `ls -al <ime_direktorija>`.

- Ako je kao argument naredbenog retka zadano ime datoteke, program ispisuje svojstva samo za tu datoteku. Pri tome je potrebno ispisati sve informacije o datoteci koje bi se dobile pozivanjem naredbe `ls -al <ime_datoteke>`.

- Ako kao argument naredbenog retka nije zadano ime direktorija, potrebno je ispisati svojstva svih datoteka i direktorija u trenutnom direktoriju. Pri tome je potrebno ispisati sve informacije o datotekama i direktorijima koje bi se dobile pozivanjem naredbe `ls -al .`.

Također, napišite `Makefile` datoteku za prevođenje i povezivanje vašeg programa.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba7` i u njemu napravite datoteku `program.c`.

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <dirent.h>
#include <string.h>
#include <pwd.h>
#include <grp.h>
#include <time.h>

// Funkcija za ispis svojstava datoteke
void print_file_properties(const char *filename, const struct stat *file_stat) {
    struct passwd *user_info = getpwuid(file_stat->st_uid);
    struct group *group_info = getgrgid(file_stat->st_gid);

    // Ispis vrste datoteke
    printf((S_ISDIR(file_stat->st_mode)) ? "d" : "-");
    printf((file_stat->st_mode & S_IRUSR) ? "r" : "-");
    printf((file_stat->st_mode & S_IWUSR) ? "w" : "-");
    printf((file_stat->st_mode & S_IXUSR) ? "x" : "-");
    printf((file_stat->st_mode & S_IRGRP) ? "r" : "-");
    printf((file_stat->st_mode & S_IWGRP) ? "w" : "-");
    printf((file_stat->st_mode & S_IXGRP) ? "x" : "-");
    printf((file_stat->st_mode & S_IROTH) ? "r" : "-");
    printf((file_stat->st_mode & S_IWOTH) ? "w" : "-");
    printf((file_stat->st_mode & S_IXOTH) ? "x" : "-");

    // Ispis broja povezivanja
    printf(" %ld", (long)file_stat->st_nlink);

    // Ispis imena vlasnika i grupe
    printf(" %s %s", user_info->pw_name, group_info->gr_name);

    // Ispis veličine datoteke
    printf(" %lld", (long long)file_stat->st_size);

    // Ispis vremena zadnjeg pristupa
    char time_buffer[20];
    strftime(time_buffer, 20, "%b %d %H:%M", localtime(&file_stat->st_atime));
    printf(" %s", time_buffer);

    // Ispis imena datoteke
    printf(" %s\n", filename);
}

// Funkcija za obradu direktorija
void process_directory(const char *dirname) {
    DIR *dir = opendir(dirname);
    if (!dir) {
        perror("opendir");
        exit(EXIT_FAILURE);
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        char path[PATH_MAX];
        snprintf(path, sizeof(path), "%s/%s", dirname, entry->d_name);

        struct stat file_stat;
        if (lstat(path, &file_stat) == -1) {
            perror("lstat");
            continue;
        }

        // Preskači skrivene datoteke/direktorije
        if (entry->d_name[0] == '.')
            continue;

        print_file_properties(entry->d_name, &file_stat);
    }

    closedir(dir);
}

int main(int argc, char *argv[]) {
    const char *path;

    // Ako je naveden argument, koristi se taj put
    if (argc > 1) {
        path = argv[1];
    } else {
        // Ako nije naveden argument, koristi se trenutni direktorij
        path = ".";
    }

    struct stat path_stat;
    if (lstat(path, &path_stat) == -1) {
        perror("lstat");
        exit(EXIT_FAILURE);
    }

    // Provjeri vrstu stvorenog entiteta (datoteka ili direktorij)
    if (S_ISDIR(path_stat.st_mode)) {
        process_directory(path);
    } else {
        print_file_properties(path, &path_stat);
    }

    return EXIT_SUCCESS;
}
```

Ovaj program ispisuje svojstva datoteka i direktorija u navedenom direktoriju ili u trenutnom direktoriju, ovisno o argumentima naredbenog retka. Ukoliko nije naveden direktorij, program koristi trenutni direktorij.

Objašnjenje koda:

### (i) Uključivanje biblioteka
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <dirent.h>
#include <string.h>
#include <pwd.h>
#include <grp.h>
#include <time.h>
```
Ovaj dio uključuje različite biblioteke potrebne za rad s datotekama, direktorijima, vremenom i drugim operacijama.

- `stdio.h`: Standardna biblioteka za ulazno/izlazne operacije.
- `stdlib.h`: Standardna biblioteka za opće funkcije, uključujući alokaciju memorije.
- `sys/types.h` i `sys/stat.h`: Biblioteke za rad s tipovima podataka i informacijama o statusu datoteke.
- `unistd.h`: Biblioteka za POSIX operacije, uključujući rad s simboličkim vezama.
- `dirent.h`: Biblioteka za rad s direktorijima i strukturama direktorija.
- `string.h`: Standardna biblioteka za manipulaciju nizovima karaktera.
- `pwd.h`: Biblioteka za rad s informacijama o korisniku (passwd struktura).
- `grp.h`: Biblioteka za rad s informacijama o grupama (group struktura).
- `time.h`: Standardna biblioteka za rad s vremenom.

### (ii) Funkcija `print_file_properties`
```c
void print_file_properties(const char *filename, const struct stat *file_stat) {
    struct passwd *user_info = getpwuid(file_stat->st_uid);
    struct group *group_info = getgrgid(file_stat->st_gid);

    // Ispis vrste datoteke
    printf((S_ISDIR(file_stat->st_mode)) ? "d" : "-");
    printf((file_stat->st_mode & S_IRUSR) ? "r" : "-");
    printf((file_stat->st_mode & S_IWUSR) ? "w" : "-");
    printf((file_stat->st_mode & S_IXUSR) ? "x" : "-");
    printf((file_stat->st_mode & S_IRGRP) ? "r" : "-");
    printf((file_stat->st_mode & S_IWGRP) ? "w" : "-");
    printf((file_stat->st_mode & S_IXGRP) ? "x" : "-");
    printf((file_stat->st_mode & S_IROTH) ? "r" : "-");
    printf((file_stat->st_mode & S_IWOTH) ? "w" : "-");
    printf((file_stat->st_mode & S_IXOTH) ? "x" : "-");

    // Ispis broja povezivanja
    printf(" %ld", (long)file_stat->st_nlink);

    // Ispis imena vlasnika i grupe
    printf(" %s %s", user_info->pw_name, group_info->gr_name);

    // Ispis veličine datoteke
    printf(" %lld", (long long)file_stat->st_size);

    // Ispis vremena zadnjeg pristupa
    char time_buffer[20];
    strftime(time_buffer, 20, "%b %d %H:%M", localtime(&file_stat->st_atime));
    printf(" %s", time_buffer);

    // Ispis imena datoteke
    printf(" %s\n", filename);
}
```
Ova funkcija prima naziv datoteke (`filename`) i strukturu `stat` koja sadrži informacije o datoteci. Funkcija koristi `getpwuid` i `getgrgid` funkcije za dohvaćanje informacija o vlasniku i grupi datoteke. Zatim ispisuje različite informacije o datoteci, uključujući vrstu, dozvole pristupa, broj povezivanja, vlasnika, grupu, veličinu, vrijeme zadnjeg pristupa i ime datoteke.

### (iii) Funkcija `process_directory`
```c
void process_directory(const char *dirname) {
    DIR *dir = opendir(dirname);
    if (!dir) {
        perror("opendir");
        exit(EXIT_FAILURE);
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        char path[PATH_MAX];
        snprintf(path, sizeof(path), "%s/%s", dirname, entry->d_name);

        struct stat file_stat;
        if (lstat(path, &file_stat) == -1) {
            perror("lstat");
            continue;
        }

        // Preskači skrivene datoteke/direktorije
        if (entry->d_name[0] == '.')
            continue;

        print_file_properties(entry->d_name, &file_stat);
    }

    closedir(dir);
}
```
Ova funkcija prima putanju do direktorija (`dirname`). Otvara taj direktorij pomoću `opendir` funkcije, a zatim prolazi kroz sve stavke unutar direktorija pomoću `readdir`. Za svaku stavku, funkcija gradi potpunu putanju do te stavke, dobavlja informacije o stavki pomoću `lstat` funkcije i poziva funkciju `print_file_properties` za ispis tih informacija. Također, preskače skrivene datoteke i direktorije (one koje počinju s '.'). Na kraju, zatvara otvoreni direktorij pomoću `closedir`.

### (iv) Funkcija `main`
```c
int main(int argc, char *argv[]) {
    const char *path;

    // Ako je naveden argument, koristi se taj put
    if (argc > 1) {
        path = argv[1];
    } else {
        // Ako nije naveden argument, koristi se trenutni direktorij
        path = ".";
    }

    struct stat path_stat;
    if (lstat(path, &path_stat) == -1) {
        perror("lstat");
        exit(EXIT_FAILURE);
    }

    // Provjeri vrstu stvorenog entiteta (datoteka ili direktorij)
    if (S_ISDIR(path_stat.st_mode)) {
        process_directory(path);
    } else {
        print_file_properties(path, &path_stat);
    }

    return EXIT_SUCCESS;
}
```
Ovo je glavna funkcija programa. Provjerava se broj argumenata prilikom pokretanja programa. Ako je naveden argument, koristi se ta putanja; inače se koristi trenutni direktorij (`"."`). Dohvaća se informacija o putanji pomoću `lstat` funkcije, a zatim se provjerava vrsta stvorenog entiteta (datoteke ili direktorija). Ako je riječ o direktoriju, poziva se funkcija `process_directory`; inače se poziva funkcija `print_file_properties` za pojedinu datoteku. Na kraju, program vraća `EXIT_SUCCESS` ako je uspješno izvršen.

___

U nastavku napišite `Makefile` i pravilo za prevođenje i povezivanje gornjeg programa.
Preostaje vam samo da napravite `.tar` datoteku od direktorija `vjezba7` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
