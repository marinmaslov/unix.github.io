# 🚀 Vježba 2: Argumenti naredbenog retka i rad s datotekama

## Sažetak 📃

---

## Zadatak 📋

Stvorite novi direktorij `vjezba4`. U njemu ćete stvoriti dvije `c` datoteke u kojima ćete napisati sljedeće programe.

Prvi program napišite tako da koristite funkciju fork za stvaranje novog procesa. Nakon poziva funkcije fork, iz oba procesa ispišite slijedeće informacije:
- `process ID`
- `parent process ID`
- `user ID` (vlasnik procesa)

```
💡 Dohvaćanje ID-a procesa vrši se pomoću: getpid(), getppid() i getuid()
```

Drugi program napišite tako da stvara novi proces ali na način da `parent process ID` novog procesa nije `process ID` izvornog procesa. Pri tom koristite činjenicu da nakon završetka izvršavanja procesa (nakon poziva funkcije exit) sve njegove CHILD procese nasljeđuje INIT. Također, iz oba procesa ispišite gore navedene informacije. **Koji je parent process ID novog procesa?**

Napišite i `Makefile` datoteku s pravilima za prevođenje i povezivanje vaših zadataka!

---

Osim uputa u nastavku, pročitajte i: [UNIX procesi](../dodatno/unix_procesi.md)

## Upute 🧭

### Program `prvi.c`

Prvi program koristi `fork` kako bi stvorio novi proces. Prema uputama ([UNIX procesi](../dodatno/unix_procesi.md)), ako je `pid` pozitivan broj, onda je riječ o `Parent` procesu, ako je 0, onda j e riječ o `Child` procesu.

### 

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

- `fork()`: Stvara novi proces. Ako je `pid` pozitivan, to znači da smo u parent procesu, a ako je nula, to znači da smo u child procesu.
- `Child` proces ispisuje svoj `PID`, `PPID` i `UID` korištenjem funkcija `getpid()`, `getppid()` i `getuid()`.
- `Parent` proces također ispisuje svoj `PID`, `PPID` i `UID`.

---

### Program `drugi.c`:

Drugi program stvara novi proces na način da `parent process ID` novog procesa nije `process ID` izvornog procesa. Pri čemu kada završi `Parent` proces, sve njegove `Child` procese nasljeđuje `INIT`.

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/wait.h>

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

        // Sleep kako bi child proces završio prije nego što roditelj izađe
        sleep(2);

        // Ispis informacija o procesu
        printf("Parent proces:\n");
        printf("PID: %d\n", getpid());
        printf("PPID: %d\n", getppid());
        printf("UID: %d\n", getuid());
    }

    return 0;
}
```

- `fork()`: Stvara novi proces. Ako je `pid` pozitivan, to znači da smo u parent procesu, a ako je nula, to znači da smo u child procesu.
- **Child proces:**
  - Koristi `execlp` kako bi zamijenio svoj trenutni proces s novim procesom koji izvršava `ps -ef` naredbu. 
  - Ako `execlp` uspije, linije ispod te naredbe se ne izvršavaju. 
  - U suprotnom, ispisuje poruku o pogrešci pomoću `perror` i završava s greškom (`return 1`).
- **Parent proces:**
  - Nakon stvaranja child procesa, parent proces čeka 2 sekunde prije nego što završi s izvođenjem. Ovo je dodano kako bi se osiguralo da child proces ima dovoljno vremena završiti svoje izvođenje prije nego što parent proces izađe.
  - Ispisuje informacije o procesu, uključujući `PID` (`process ID`), `PPID` (`parent process ID`) i `UID` (`user ID`).

Ovo je primjer situacije gdje je parent proces svjestan child procesa i namjerno čeka da child proces završi izvođenje prije nego što parent završi. Kroz ovo čekanje, child proces postaje `orphan` proces i nasljeđuje ga `INIT` proces (`PID` 1).

---

Sve što vam preostaje je da kao i u prošloj vježbi napravit .tar datoteku od direktorija vjezba2 te istu učitate na Merlin (hint: .tar datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa).
