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

Drugi program napišite tako da stvara novi proces ali na način da `parent process ID` novog procesa nije `process ID` izvornog procesa. Pri tom koristite činjenicu da nakon završetka izvršavanja procesa (nakon poziva funkcije exit) sve njegove CHILD procese nasljeđuje INIT. Također, iz oba procesa ispišite gore navedene informacije. Koji je parent process ID novog procesa?

---

Osim uputa u nastavku, pročitajte i: [UNIX procesi](../dodatno/unix_procesi.md)

## Upute 🧭

Evo C programa koji koristi funkciju `fork` za stvaranje novog procesa i ispisuje tražene informacije:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    // Stvaranje novog procesa
    pid_t pid = fork();

    if (pid < 0) {
        // Greška prilikom fork-a
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

        // Dodatni ispis za prepoznavanje novog procesa
        printf("Novi proces ID: %d\n", pid);

        // Čekanje da child proces završi (pogodno za demonstraciju u komandnoj liniji)
        wait(NULL);
    }

    return 0;
}
```

Neka ključna objašnjenja:

- Funkcija `fork` stvara novi proces. Ako je `pid` pozitivan, to znači da smo u parent procesu, a ako je nula, to znači da smo u child procesu.
- U oba procesa ispisujemo PID, PPID i UID koristeći funkcije `getpid`, `getppid` i `getuid`.
- Parent proces također čeka na završetak child procesa pomoću funkcije `wait`.

Kada pokrenete ovaj program, možete primijetiti da će PID-ovi biti različiti, ali PPID parent procesa bit će inicijalno PPID-ova procesa koji je pokrenuo ovaj program. Nakon što parent proces završi, PPID child procesa bit će postavljen na PID inicijalnog procesa (obično procesa s PID-om 1, koji je INIT proces).





Osim uputa raspisanih u nastavku, korisno je pročitati i: [Datoteke u C jeziku](../dodatno/datoteke.md).
