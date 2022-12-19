# 🚀 Vježba 6: Preusmjeravanje, signali

# Zadatak 📋

Zadatak ove vježbe je napraviti direktorij `vjezba6` i u njega kopirati `program2.c` iz prošle vježbe. Isti je potrebno preimenovati u `program.c`. Zatim je potrebno prepraviti program na način da se standardni izlaz naredbe koja se pokreće preusmjerava u datoteku. Neka se ime datoteke zadaje kao prvi argument naredbenog retka, a iza imena datoteke slijedi sama naredba i njeni argumenti.

Primjer:
`./program ls.out ls -al`

Objašnjenje primjera:
`./program log.out ls -al` pokreće naredbu ls sa opcijom -al. Naredba ls se pokreće u CHILD procesu, a njezin standardni izlaz se prusmerava u datoteku ls.out.
U PARENT procesu implementirajte signal handler za signal SIGCHLD (primjer za imlementaciju signal handlera). Nakon primanja signala, PARENT proces treba pokupiti izlazni status CHILD procesa.

Kao i u prošloj vježbi potrbno je napisati `Makefile` datoteku te pokrenitu `make` pravilo za prevođenje i povezivanje programa.

U konačnici direktorij `vjezba6` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba6`.

Zatim kopirajte `program2.c` iz prošle vježbe (iz direktorija `vjezba5`) u novonastali direktorij. Preimenovanje datoteke u `program.c` možete obaviti odmah u naredbi `cp` ili nakon kopiranja naredbom `mv`.

Nakon uspješnog kopiranja programa potrebno je program prepravit, no prvo se prisjetimo samog programa:

Program kao argumente naredbenog retka prima proizvoljnu naredbu sa njenim argumentima. Funkcija `fork()` stvara novi proces, nakon čega se memorijska slika `CHILD` procesa zamjenjuje novim programom pozivom funkcije `execvp()`. Funkcija `execvp()` argumente naredbenog retka preuzima u obliku polja pokazivača, na način na koji ih funkcija `main()` u novopokrenutom programu i prima. U našem slučaju, ime programa je drugi argument naredbenog retka (argv[1] - odmah iza naredbe kojom pozivamo program), a polke pokazivača koji se proslijeđuju kao argumenti funkcije main novopokrenutog programa započinje na adresi &argv[1]. Na ovaj način, novopokrenutom programu se proslijeđuje lista argumenata naredbenog retka bez obzira na njihov broj.

### `program.c`
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

Program se sada može prepraviti tako da `PARENT` proces nastavlja s radom neovisno o `CHILD` procesu. Izlazni status `CHILD` procesa `PARENT` proces preuzima funkcijom `wait()` koja se poziva iz `signal handler` funkcije `child_handler`. Signal handler postavljen je funkcijom `signal()`.

``` c
#include <stdio.h>
#include <unistd.h>
#include <wait.h>


int cpid = 0;

static void chld_handler(int signo) {
  cpid = wait(NULL);
}

int main(int argc, char **argv) {
  int pid;

  if (argc < 2) {
    printf("Koristenje: %s NAREDBA [argumenti] ...\n", argv[0]);
    return 0;
  }

  signal(SIGCHLD, chld_handler);

  pid = fork();
  if (pid == -1) {              // Error
    perror("fork");
    return 0;
  } else if (pid == 0) {        // Child
    execvp(argv[1], &argv[1]);
  }

  while (!cpid) {
    pause();
  }
  printf("[PID %5d]: Terminated\n", cpid);

  return 0;
}
```
___

Ono što je preostalo je napraviti redirekciju ispisa u datoteku, a to radimo na sljedeći način:


___

U nastavku napišite `Makefile` i pravilo `program` za prevođenje i povezivanje izmjenjenog programa.

Nakon izvršavanja `make` pravila, testirajte vaš program s:

`program2`
``` bash
./program ls.out ls -al
```


Sve što vam preostaje je da kao i u prošloj vježbi napravite `.tar` datoteku od direktorija `vjezba5` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
