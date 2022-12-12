# 🚀 Vježba 5: Pokretanje novog programa

# Zadatak 📋

Zadatak ove vježbe je napraviti direktorij `vjezba5` i u njemu napisati program koji koristi kombinaciju funkcija `fork` i `exec` za pokretanje novog programa u `CHILD` procesu. Pri tom se za program koji se poziva može navesti proizvoljan broj argumentata naredbenog retka. Primjer: `pokreni ls -al`.

Obješnjenje primjera:
`pokreni ls -al` pokreće naredbu `ls` s opcijom `-al`. Naredba ls se pokreće u `CHILD `procesu. Nakon završetka rada pozvane naredbe `PARENT` proces poziva funkciju wait i ispisuje `process ID`, način izlaska i izlazni status pokrenutog `CHILD` procesa.

Također je potrebno napisati `Makefile` datoteku s <b>pravilima</b> za prevođenje i povezivanje programa.

U konačnici direktorij `vjezba5` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba5` i u njemu napravite dvije datoteke: `program1.c` i `program2.c`, te u njih zalijepite sljedeći programski kod:

### `program1.c`
Sljedeći program kao kao argument naredbenog retka uzima izvršnu datoteku koju želimo pokrenuti. Funkcijom fork() stvara se novi proces, nakon čega se memorijska slika child procesa funkcijom execl() zamjenjuje novim programom. Procesorsko vrijeme chil procesa je ograničeno funkcijom setrlimit(). Parrent proces funkcijom waitpid() preuzima izlazni status child procesa i ispituje ga.

``` c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/time.h>
#include <sys/resource.h>

int main(int argc, char *argv[]) {
  int cpid, wstatus;
  struct rlimit plimit;

  if (argc < 2) {
    printf("Korištenje: pokreni /putanja/na/program\n");
    exit(0);
  }

  cpid=fork();
  if (cpid < 0) { /* greska */
    perror("fork");
    exit(0);
  } else if (cpid == 0) { /* CHILD  proces */
    /* limit na procesorsko vrijeme za CHILD proces */
    plimit.rlim_max=5;
    plimit.rlim_cur=5;
    if(setrlimit(RLIMIT_CPU, &plimit) < 0) {
      perror("setrlimit");
      exit(0);
    }
    execl(argv[1], argv[1], (char *)NULL);
  } else { /* PARRENT proces */
    waitpid(cpid, &wstatus, 0);
    /* Provjera izlaznog statusa CHILD procesa */
    if (WIFEXITED(wstatus)) {
      printf("Izlazni status CHILD procesa: %d\n", WEXITSTATUS(wstatus));
      fflush(stdout);
    } else if (WIFSIGNALED(wstatus)) {
      fprintf(stderr, "CHILD proces prekinut signalom: %d\n", \
	      WTERMSIG(wstatus));
      fflush(stderr);
    }
  }
  
  exit(0);
}
```
Osim gornjeg programa potrebno je napisati (sami) i `hello.c` program koji u glavnoj funkciji ispisuje "Hello World!".

### `program2.c`

``` c
#include <stdio.h>
#include <unistd.h>
#include <wait.h>

int main(int argc, char *argv[]) {
  int pid, cpid;

  if (argc < 2) {
    printf("Koristenje: %s NAREDBA [argumenti] ...\n", argv[0]);
    return 0;
  }

  pid = fork();
  if (pid == -1) {              // Error
    perror("fork");
    return 0;
  } else if (pid != 0) {        // Parent
    cpid = wait(NULL);
    printf("[PID %5d]: Terminated\n", cpid);
  } else {
    execvp(argv[1], &argv[1]);
  }

  return 0;

}
```
___

U nastavku napišite `Makefile` za prevođenje i povezivanje svih gornjih programa (program1.c, program2.c i hello.c).

Nakon izvršavanja `make` pravila, testirajte vaše programe s:

`program1`
``` bash
./program1 pokreni hello
```

`program2`
``` bash
./program1 ls -al
```


Sve što vam preostaje je da kao i u prošloj vježbi napravite `.tar` datoteku od direktorija `vjezba5` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
