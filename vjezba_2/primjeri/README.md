## 🚀 Vježba 2: Primjeri programskog koda

# Funkcija main i argumenti naredbenog retka 💻

Kod pokretanja programa, ljuska iz koje se program pokreće može funkciji main proslijediti skup argumenata narebenog retka. Ove argumente program prima putem argumenata funkcije main:
- `int argc`: broj argumenata naredbenog retka, uključujući i ime programa
- `char *argv[ ]`: argumenti naredbenog retka

Kako ne bi gubili vrijeme na prepisivanje danih primjera, možete ih kopirati u svoj direktorij `vježba2` (dakle naredbe izvršite dok se nalazite u vašem direktoriju `vjazba2`) pomoću naredbi:
- `cp /home/mmaslo00/vjezba2/primjeri/prvi.c prvi.c`
- `cp /home/mmaslo00/vjezba2/primjeri/drugi.c drugi.c`
- `cp /home/mmaslo00/vjezba2/primjeri/treci.c treci.c`

Također kopirajte i `test.txt` koju ćete koristiti u programu `drugi.c`:
- `cp /home/mmaslo00/vjezba2/primjeri/test.txt test.txt`

___
# Primjer 1. 📋
Sljedeći program ispisuje cijelu listu argumenata naredbenog retka


```c
#include <stdio.h>
int main(int argc, char *argv[]) {
  int i;
  
  for (i=0; i<argc; i++)
    printf("argv[%d]: %s\n", i, argv[i]);
    
  return(0);
}
```
___
# Primjer 2. 📋
Sljedeći program čita sadržaj datoteke i ispisuje ga. Ime datoteke se zadaje kao argument naredenog retka.

``` c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
  int fd1, n;
  char sl;
   
  if (argc < 2) {
    printf("Koristenje: citaj <ime_datoteke>\n");
    exit(0);
  }
                    
  fd1=open(argv[1], O_RDONLY);
  if (fd1 < 0) {
    perror("open");
    exit(-1);
  }
                                    
  while((n=read(fd1, &sl, 1)) > 0) {
    write(STDOUT_FILENO, &sl, 1);
  } 
                                              
  if (n == -1) {
    perror("read");
  }
                                                          
  close(fd1);
  exit(0);
}
```

___
# Primjer 3. 📋
Sljedeći program ima funkcionalnost UNIX naredbe cat. Program ispisuje sadržaj svih datoteka čija su imena zadana kao argumenti naredbenog retka. Ukoliko niti jedna datoteka nije zadana, program kopira standardni ulaz na standardni izlaz.

``` c
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>

int rw(int fdin, int fdout) {
  int n;
  char c;
  
  while ((n=read(fdin, &c, 1)) > 0)
    write(fdout, &c, 1);
    
  return n;
}

int main(int argc, char *argv[]) {
  int fd, k;
 
  for (k=1; k<argc; k++) {
    fd = open(argv[k], O_RDONLY);
    if (fd == -1) {
      perror("open");
      return -1;
    }
    rw(fd, STDOUT_FILENO);
    close(fd);
  }

  if (k == 1)
    rw(STDIN_FILENO, STDOUT_FILENO);

  return 0;
}
```
