# Signali

Signali omogućavaju jednostavan način komunikacije među procesima na način da jedan proces šalje signal drugom procesu. Proces prima signal korištenjem funkcije za obradu signala, tzv. signal handler. Ukoliko proces ne definira funkciju za obradu signala, izvršava se uobičajena akcija povezana sa određenim signalom, što često znači prikid procesa.

Signal hadler postavljamo funkcijom `signal()` koja kao argumente uzima signal za koji handler želimo postaviti i pokazivač na funkciju koja obrađuje signal.

## Primjer

U danom primjeru postavljaju se signal handleri za tri signala, `SIGUSR1`, `SIGUSR2` i `SIGTERM`. Signali `SIGUSR1` i `SIGUSR2` obrađuju se funkcijom `usr_handler()` koja samo ispisuje koji je signal primljen. Signal `SIGTERM` obrađuje se funkcijom `term_handler()` koja ispisuje poruku, ali i postavlja globalnu varijablu `exit_flag` na vrijednost 0 što rezultira prekidom izvršavanja programa. Na ovaj način, primanje signala `SIGTERM` je i dalje povezano sa uobičajenim postupkom prekida programa, ali u ovom slučaju programer ima kontrolu nad načinom kako program završava izvršavanje.

``` c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <signal.h>

int exit_flag = 1;

static void term_handler(int signo) {
  if (signo == SIGTERM) {
    printf("Primljen signal SIGTERM\n");
    fflush(stdout);
    exit_flag = 0;
  }
}

static void usr_handler(int signo) {
  switch (signo) {
    case SIGUSR1:
      printf("Primljen signal SIGUSR1\n");
      fflush(stdout);
      break;
    case SIGUSR2:
      printf("Primljen signal SIGUSR2\n");
      fflush(stdout);
      break;
    default:
      printf("Primljen signal %d\n", signo);
      fflush(stdout);
  }
}

int main() {
  signal (SIGUSR1, usr_handler);
  signal (SIGUSR2, usr_handler);
  signal (SIGTERM, term_handler);

  while(exit_flag) 
    pause();

  printf("Program prekinut signalom SIGTERM\n");
  exit(0);
}
```

Objašnjenje koda:

### 1. Definicija globalne varijable
   ```c
   int exit_flag = 1;
   ```
   - Varijabla `exit_flag` koristi se za kontrolu petlje u `main` funkciji. Ako primimo signal SIGTERM, postavit će se na 0 i program će izaći iz petlje.

### 2. Definicija signal handler funkcija
   ```c
   static void term_handler(int signo) {
     if (signo == SIGTERM) {
       printf("Primljen signal SIGTERM\n");
       fflush(stdout);
       exit_flag = 0;
     }
   }

   static void usr_handler(int signo) {
     switch (signo) {
       case SIGUSR1:
         printf("Primljen signal SIGUSR1\n");
         fflush(stdout);
         break;
       case SIGUSR2:
         printf("Primljen signal SIGUSR2\n");
         fflush(stdout);
         break;
       default:
         printf("Primljen signal %d\n", signo);
         fflush(stdout);
     }
   }
   ```
   - `term_handler` obrađuje signal SIGTERM. Kada program primi ovaj signal, ispisuje poruku, postavlja `exit_flag` na 0 i završava.
   - `usr_handler` obrađuje signale SIGUSR1 i SIGUSR2. Ovisno o primljenom signalu, ispisuje odgovarajuću poruku.

### 3. Postavljanje signal handlera u `main` funkciji
   ```c
   signal (SIGUSR1, usr_handler);
   signal (SIGUSR2, usr_handler);
   signal (SIGTERM, term_handler);
   ```
   - `signal` funkcija se koristi za postavljanje funkcija koje će se izvršiti kada se primi određeni signal.

### 4. Petlja koja čeka na signale
   ```c
   while(exit_flag) 
     pause();
   ```
   - Program ulazi u beskonačnu petlju koja čeka na signale.
   - `pause()` suspendira izvođenje procesa dok ne primi signal. Kada primi signal, poziva se odgovarajuća signal handler funkcija.

### 5. Završetak programa
   ```c
   printf("Program prekinut signalom SIGTERM\n");
   exit(0);
   ```
   - Kada petlja završi (kada primimo SIGTERM), ispisuje se poruka i program završava.

Ovaj program pokazuje osnovnu upotrebu signal handlera za hvatanje i obradu različitih signala. Može se koristiti kao primjer za osnovnu signal obradu u C programima.
