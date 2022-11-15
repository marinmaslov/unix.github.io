# Vježba 3 - rješenja
U nastavku se nalaze rješenja vježbe3.

# Program `prvi.c`
U prvom prgoramu ispisujemo argumente s naredbenog retka koje korisnik unese.

```c
#include <stdio.h>
int main(int argc, char *argv[]) {
  int i;
  
  for (i=1; i<argc; i++)
    printf(" %s\n", argv[i]);
    
  return(0);
}
```

# Program `drugi.c`
U drugom prgoramu ispisujemo sadržaj datoteka koje korisnik unese kao argumente.

```c
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
  int i;
  for(i = 1; i < argc; i++) {                    
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
  }
  exit(0);
}
```

# Program `treci.c`
U trecem prgoramu ispisujemo ili sadržaj datoteka ili argumente naredbenog retka, ako ti argumenti nisu bili datoteke.

``` c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdbool.h>
#include <string.h>

int main(int argc, char *argv[]) {
  int fd1, n;
  char sl;
   
  if (argc < 2) {
    exit(0);
  }
  
  int i;
  bool uvjet = false;
  for (i = 1; i < argc; i++) {
    char *ext = strrchr(argv[i], '.');
    if (ext) {
      uvjet = true;
    }
    else {
      uvjet = false;
    }
  } 
  
  if (uvjet) {
    int j;
    for(j = 1; j < argc; j++) {                    
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
    }
  }
  else {
      int k;
      for (k=1; k<argc; k++) {
        printf("%s", argv[k]);
      }
    
  }
  exit(0);
}
```

# Program `cetvrti.c`
U cetvrtom prgoramu radimo isto što i u trećem, samo su funkcije za ispisivanje u drugoj `.c` datoteci.

`cetvrti.c`
```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdbool.h>
#include <string.h>
#include "ispisi.h"

int main(int argc, char *argv[]) {
  int fd1, n;
  char sl;
   
  if (argc < 2) {
    exit(0);
  }
  
  int i;
  bool uvjet = false;
  for (i = 1; i < argc; i++) {
    char *ext = strrchr(argv[i], '.');
    if (ext) {
      uvjet = true;
    }
    else {
      uvjet = false;
    }
  } 
  
  if (uvjet) {
    ispisi_datoteke(argc, argv);
  }
  else {
    ispisi_argumente(argc, argv);
  }
  exit(0);
}
```

`ispisi.c`
```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdbool.h>
#include <string.h>

void ispisi_argumente(int argc, char *argv[]) {
    int j;
    for (j=1; j<argc; j++) {
      printf("%s\n", argv[j]);
    }    
}

void ispisi_datoteke(int argc, char *argv[]) {
    char sl;
    int j, fd1, n;
    for(j = 1; j < argc; j++) {                    
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
    }
}
```

`ispisi.h`
```c
#ifndef HEADER_FILE
#define HEADER_FILE

void ispisis_arguemtne(int argc, char *argv[]);

void ispisis_datoteke(int argc, char *argv[]);

#endif
```
