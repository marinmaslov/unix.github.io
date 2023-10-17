# 🚀 Vježba 5: Pokretanje novog programa

## Zadatak 📋

Zadatak ove vježbe je napraviti direktorij `vjezba5` i u njemu napisati program koji koristi kombinaciju funkcija `fork` i `exec` za pokretanje novog programa u `CHILD` procesu. Pri tom se za program koji se poziva može navesti proizvoljan broj argumentata naredbenog retka. Primjer: `pokreni ls -al`.

Obješnjenje primjera:
`pokreni ls -al` pokreće naredbu `ls` s opcijom `-al`. Naredba ls se pokreće u `CHILD `procesu. Nakon završetka rada pozvane naredbe `PARENT` proces poziva funkciju wait i ispisuje `process ID`, način izlaska i izlazni status pokrenutog `CHILD` procesa.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba5` i u njemu napravite datoteku `program.c`.

### `program.c`

Sljedeći program kao argument naredbenog retka uzima izvršnu datoteku koju želimo pokrenuti. Funkcijom fork() stvara se novi proces, nakon čega se memorijska slika `Child` procesa funkcijom `execl()` zamjenjuje novim programom. Procesorsko vrijeme `Child` procesa je ograničeno funkcijom `setrlimit()`. `Parrent` proces funkcijom `waitpid()` preuzima izlazni status `Child` procesa i ispituje ga.

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
    if (argc < 2) {
        fprintf(stderr, "Usage: %s <program> [arguments...]\n", argv[0]);
        return 1;
    }

    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je CHILD proces

        // Pokretanje programa u CHILD procesu
        execvp(argv[1], &argv[1]);

        // Ova linija izvršit će se samo ako execvp ne uspije
        perror("execvp");
        return 1;
    } else {
        // Ovo je PARENT proces

        // Čekanje na završetak CHILD procesa
        int status;
        pid_t child_pid = waitpid(pid, &status, 0);

        // Ispis informacija o CHILD procesu
        printf("Process ID CHILD procesa: %d\n", child_pid);

        if (WIFEXITED(status)) {
            printf("CHILD proces je završio normalno s kodom izlaza: %d\n", WEXITSTATUS(status));
        } else if (WIFSIGNALED(status)) {
            printf("CHILD proces je završen signalom: %d\n", WTERMSIG(status));
        }

        return 0;
    }
}
```

#### (i) Provjera broja argumenata

Ako program nije pozvan s dovoljno argumenata (barem dva - ime programa i barem jedan argument), ispisuje se poruka o korištenju i program se završava s greškom.

#### (ii) Stvaranje `Child` procesa

Koristi se `fork()` kako bi se stvorio novi proces.

#### (iii) `Child` proces

Ako je pid jednak nuli, to znači da smo u `Child` procesu.
Koristi se `execvp()` kako bi se zamijenila memorijska slika `Child` procesa s novim programom.
Ako `execvp()` ne uspije, ispisuje se poruka o pogrešci pomoću `perror()` i `Child` proces se završava s greškom.

#### (iv) `Parent` proces

Ako smo u `Parent` procesu, čekamo na završetak `Child` procesa pomoću `waitpid()`.
Ispisujemo informacije o `Child` procesu, uključujući njegov `PID`.
Provjeravamo način završetka `Child` procesa pomoću makroa `WIFEXITED` i `WIFSIGNALED` iz <sys/wait.h>.

---

Sve što vam preostaje je napisati `Makefile` datoteku s pravilima za prevođenje i povezivanje ovog programa te da kao i u prošloj vježbi napravite `.tar` datoteku od direktorija `vjezba5` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
