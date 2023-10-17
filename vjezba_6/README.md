# 🚀 Vježba 6: Preusmjeravanje, signali

## Zadatak 📋

Zadatak iz prethodne vježbe preradite na način da se standardni izlaz naredbe koja se pokreće preusmjerava u datoteku. Neka se ime datoteke zadaje kao prvi argument naredbenog retka, a iza imena datoteke slijedi sama naredba i njeni argumenti.

Primjer:
`./program ls.out ls -al`

Objašnjenje primjera:
`./program log.out ls -al` pokreće naredbu ls sa opcijom -al. Naredba ls se pokreće u `Child` procesu, a njezin standardni izlaz se prusmerava u datoteku `ls.out`.
U `Parent` procesu implementirajte signal handler za signal `SIGINT` ([primjer za imlementaciju signal handlera](../dodatno/signali.md)). Proces treba obraditi signal na način da ga uhvati i pošalje `SIGTERM` `Child` procesu u kojem je naredba pokrenuta (koristiti sustavski poziv `kill`), nakon čega `Parent` proces završava s radom. 

Kao i u prošloj vježbi potrbno je napisati `Makefile` datoteku te pokrenitu `make` pravilo za prevođenje i povezivanje programa.

U konačnici direktorij `vjezba6` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba6` i unutar njega dvije datoteke, `program.c` i `Makefile`. Datoteka `program.c` može slobodno biti kopija programa iz prošle vježbe.

### program.c

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <signal.h>

pid_t child_pid;

// Signal handler za SIGINT
void sigint_handler(int signo) {
    // Pošalji SIGTERM CHILD procesu
    if (child_pid > 0) {
        kill(child_pid, SIGTERM);
    }

    // Ova linija izvršit će se samo nakon završetka CHILD procesa
    printf("Parent proces je primio SIGINT i poslao SIGTERM CHILD procesu.\n");
    exit(0);
}

int main(int argc, char *argv[]) {
    // Postavljanje signal handlera za SIGINT
    signal(SIGINT, sigint_handler);

    // Provjera broja argumenata
    if (argc < 3) {
        fprintf(stderr, "Usage: %s <output_file> <program> [arguments...]\n", argv[0]);
        return 1;
    }

    // Stvaranje CHILD procesa
    pid_t pid = fork();

    if (pid < 0) {
        // Greška pri fork() pozivu
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // Ovo je CHILD proces

        // Otvori datoteku za pisanje
        FILE *output_file = fopen(argv[1], "w");
        if (output_file == NULL) {
            perror("fopen");
            return 1;
        }

        // Preusmjeri standardni izlaz u datoteku
        dup2(fileno(output_file), STDOUT_FILENO);
        fclose(output_file);

        // Pokretanje programa u CHILD procesu
        execvp(argv[2], &argv[2]);

        // Ova linija izvršit će se samo ako execvp ne uspije
        perror("execvp");
        return 1;
    } else {
        // Ovo je PARENT proces
        child_pid = pid;

        // Čekanje na završetak CHILD procesa
        int status;
        pid_t child_pid = waitpid(pid, &status, 0);

        // Ispis informacija o CHILD procesu
        printf("Process ID CHILD procesa: %d\n", child_pid);

        // Provjera načina završetka CHILD procesa
        if (WIFEXITED(status)) {
            printf("CHILD proces je završio normalno s kodom izlaza: %d\n", WEXITSTATUS(status));
        } else if (WIFSIGNALED(status)) {
            printf("CHILD proces je završen signalom: %d\n", WTERMSIG(status));
        }

        return 0;
    }
}
```

### Makefile

```make
CC = gcc
CFLAGS = -Wall

all: program

program: program.c
    $(CC) $(CFLAGS) -o program program.c

clean:
    rm -f program
```

2. Spremite ove datoteke unutar direktorija "vjezba5". Zatim, u terminalu, idite u taj direktorij i pokrenite naredbu `make` kako biste preveli program. Nakon toga, možete pokrenuti program sa željenim argumentima naredbenog retka, na primjer:

```bash
./program ls.out ls -al
```

Ovaj program će pokrenuti naredbu `ls -al` u CHILD procesu, a PARENT proces će čekati na završetak CHILD procesa i ispisati odgovarajuće informacije. Također, ako PARENT proces primi SIGINT (CTRL+C), poslat će SIGTERM CHILD procesu prije nego što završi.
___

Sve što vam preostaje je da kao i u prošloj vježbi napravite `.tar` datoteku od direktorija `vjezba6` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
