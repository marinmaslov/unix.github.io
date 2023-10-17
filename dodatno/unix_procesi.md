# 💻 UNIX procesi

## 1. Uvod u procese u UNIX sustavima
UNIX operacijski sustavi koriste model procesa kao temeljnu jedinicu izvršavanja programa. Proces je program u izvršavanju, a UNIX sustavi omogućuju stvaranje i upravljanje procesima pomoću raznih sustavnih poziva i funkcija.

---

## 2. Funkcija `fork()`
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces
    } else {
        // Ovo je parent proces
    }

    return 0;
}
```

- `fork()` stvara novi proces. Ako je `pid` pozitivan, to znači da smo u parent procesu, a ako je nula, to znači da smo u child procesu.
- Parent proces dobiva PID child procesa, dok child proces dobiva PID 0.

---

## 3. Funkcije za informacije o procesu
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces
        printf("Child proces:\n");
        printf("PID: %d\n", getpid());
        printf("PPID: %d\n", getppid());
        printf("UID: %d\n", getuid());
    } else {
        // Ovo je parent proces
        printf("Parent proces:\n");
        printf("PID: %d\n", getpid());
        printf("PPID: %d\n", getppid());
        printf("UID: %d\n", getuid());
    }

    return 0;
}
```

- `getpid()`: Vraća PID trenutnog procesa.
- `getppid()`: Vraća PID roditeljskog procesa.
- `getuid()`: Vraća UID vlasnika procesa.

---

## 4. Čekanje na završetak `Child` procesa
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces
    } else {
        // Ovo je parent proces
        wait(NULL); // Čekanje na završetak child procesa
    }

    return 0;
}
```

- `wait(NULL)`: Parent proces čeka na završetak bilo kojeg child procesa.

### 4.1 Funkcija `waitpid()`

Osim funkcije `wait()` postoji i `waitpid()` koja omogućuje čekanje određenog `Child` procesa.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces
        printf("Child proces s PID-om %d\n", getpid());
        return 0;
    } else {
        // Ovo je parent proces
        printf("Parent proces s PID-om %d\n", getpid());

        // Čekanje na završetak određenog child procesa
        int status;
        pid_t child_pid = waitpid(pid, &status, 0);

        if (WIFEXITED(status)) {
            printf("Child proces %d je završio s kodom izlaza %d\n", child_pid, WEXITSTATUS(status));
        } else if (WIFSIGNALED(status)) {
            printf("Child proces %d je završio signalom %d\n", child_pid, WTERMSIG(status));
        } else if (WIFSTOPPED(status)) {
            printf("Child proces %d je zaustavljen signalom %d\n", child_pid, WSTOPSIG(status));
        }

        return 0;
    }
}
```

- `waitpid(pid, &status, 0)`: Parent proces čeka na završetak određenog child procesa čiji je PID predan kao argument.
- `&status`: Ova varijabla sadrži informacije o završetku child procesa.
- `WIFEXITED(status)`, `WEXITSTATUS(status)`: Provjerava je li child proces normalno završio i dobiva kod izlaza.
- `WIFSIGNALED(status)`, `WTERMSIG(status)`: Provjerava je li child proces završen signalom.
- `WIFSTOPPED(status)`, `WSTOPSIG(status)`: Provjerava je li child proces zaustavljen signalom.

Korištenje `waitpid` omogućuje parent procesu praćenje određenog child procesa, što je posebno korisno kada postoji više child procesa u isto vrijeme.

---

## 5. Primjer stvaranja više `Child` procesa
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    for (int i = 0; i < 3; i++) {
        pid_t pid = fork();

        if (pid < 0) {
            perror("fork");
            return 1;
        } else if (pid == 0) {
            // Ovo je child proces
            printf("Child proces s indeksom %d i PID-om %d\n", i, getpid());
            return 0; // Child proces završava izvođenje
        }
    }

    // Samo parent proces čeka na završetak svih child procesa
    for (int i = 0; i < 3; i++) {
        wait(NULL);
    }

    return 0;
}
```

- Petlja stvara tri child procesa koji ispisuju svoj indeks i PID.
- Parent proces čeka na završetak svih child procesa.

---

## 6. Stvaranje novog `Child` procesa bez nasljeđivanja `parent process ID`-a
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces

        // Stvaranje novog procesa unutar child procesa
        execlp("ps", "ps", "-ef", NULL);
        
        // Ova linija izvršit će se samo ako execlp ne uspije
        perror("execlp");
        return 1;
    } else {
        // Ovo je parent proces
    }

    return 0;
}
```

- `execlp("ps", "ps", "-ef", NULL)`: Zamjenjuje trenutni proces novim procesom. U ovom primjeru, zamjenjuje se izvršavanjem naredbe `ps -ef`.
- Nakon izvršavanja `execlp`, sve što se nalazi ispod te linije neće se izvršiti u child procesu, osim ako `execlp` ne uspije. 
- Child proces preuzima PID inicijalnog procesa (obično procesa s PID-om 1, koji je INIT proces), a ne PID parent procesa.

---

## 7. Razbijanje veze parent-child

UNIX sustavi pružaju mogućnost razbijanja veze između parent i child procesa kroz stvaranje novog `session`-a i `process group`-e. Ovo je korisno kada želimo da child proces postane nezavisan od roditeljskog terminala, što sprečava slanje signala (npr., SIGINT) iz terminala child procesu. To se postiže stvaranjem novog sesijskog lidera (procesa koji nije član nijedne procesne grupe).

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je child proces
        printf("Child proces s PID-om %d\n", getpid());

        // Razbijanje veze s roditeljskim terminalom
        setsid();

        // Ovdje možete izvršavati zadatke child procesa koji nije povezan s terminalom
        // ...

        return 0;
    } else {
        // Ovo je parent proces
        printf("Parent proces s PID-om %d\n", getpid());

        // Očekuje završetak child procesa
        waitpid(pid, NULL, 0);

        return 0;
    }
}
```

- `setsid()`: Funkcija stvara novu sesiju i postavlja trenutni proces kao sesijskog lidera, a time i lidera nove procesne grupe. To rezultira time da proces neće primati signale poslane roditeljskoj terminalnoj sesiji.
  
Ovo je korisno u scenarijima gdje želimo da child proces bude potpuno nezavisan, na primjer, kada pišemo daemon procese ili procese koji rade u pozadini.

---

## 8. Zamjena memorijske slike `Child` procesa s novim programom (`execl()`)

Kada želimo zamijeniti memorijsku sliku child procesa s drugim programom, koristimo funkciju `execl()` i slične funkcije (kao što su `execlp()`, `execle()`, `execv()` itd.). Ova zamjena se odnosi na promjenu izvršnog koda i podataka child procesa na temelju novog izvršnog koda.

Primjer upotrebe `execl()`:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("Ovo je prije poziva execl().\n");

    execl("/bin/ls", "ls", "-l", NULL);

    // Ova linija izvršit će se samo ako execl ne uspije
    perror("execl");
    return 1;
}
```

U ovom primjeru, program zamjenjuje svoju memorijsku sliku s programom `/bin/ls` koristeći `execl()`. Argumenti funkcije `execl()` uključuju putanju do programa koji želimo izvršiti (`/bin/ls`), ime programa (`ls`), i završni NULL.

---

## 9. Ograničavanje procesorskog vremena `Child` procesa (setrlimit())

Funkcija `setrlimit()` se koristi za postavljanje ograničenja resursa za proces. Ograničavanje procesorskog vremena može biti korisno za sprječavanje dugotrajnih operacija koje troše previše vremena.

Primjer ograničavanja procesorskog vremena na 2 sekunde:

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/resource.h>

int main() {
    struct rlimit limit;
    limit.rlim_cur = 2;  // Ograničenje na 2 sekunde
    limit.rlim_max = 2;  // Maksimalno ograničenje također na 2 sekunde

    if (setrlimit(RLIMIT_CPU, &limit) != 0) {
        perror("setrlimit");
        return 1;
    }

    // Dugotrajna operacija
    while (1) {
        // Beskonačna petlja
    }

    return 0;
}
```

U ovom primjeru, `setrlimit()` postavlja ograničenje procesorskog vremena na 2 sekunde (`RLIMIT_CPU`). Nakon toga slijedi simulacija dugotrajne operacije beskonačnom petljom. Kada se ograničenje procesorskog vremena premaši, proces će biti automatski prekinut.
