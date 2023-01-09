# 🚀 Vježba 7: Datoteke i direktoriji

# Zadatak 📋

Zadatak ove vježbe je napisati program koji ima djelomičnu funkcionalnost naredbe `ls -al`. Program se poziva imenom direktorija ili bez njega.

Ako je kao argument naredbenog retka zadano ime direktorija, za sve datoteke u navedenom direktoriju ispisuju se svojstva. Pri tome je potrebno ispisati sve informacije o datotekama i direktorijima koje bi se dobile pozivanjem naredbe `ls -al <ime_direktorija>`.

Ako kao argument naredbenog retka nije zadano ime direktorija, potrebno je ispisati svojstva svih datoteka i direktorija u trenutnom direktoriju. Pri tome je potrebno ispisati sve informacije o datotekama i direktorijima koje bi se dobile pozivanjem naredbe `ls -al .`.

Također, napišite `Makefile` datoteku za prevođenje i povezivanje vašeg programa.

___

## Upute 🧭

Za početak stvroite direktorij `vjezba7` i u njemu napravite datoteku `zadatak.c`.

Otvorite `zadatak.c` u Joe editoru te zalijepite sljedeći kod:

``` c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <dirent.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <pwd.h>
#include <grp.h>

char *file_type(struct stat buf) {
    if (S_ISREG(buf.st_mode))       return "reg";
    else if (S_ISDIR(buf.st_mode))  return "dir";
    else if (S_ISCHR(buf.st_mode))  return "csp";
    else if (S_ISBLK(buf.st_mode))  return "bsp";
    else if (S_ISFIFO(buf.st_mode)) return "fif";
    else if (S_ISLNK(buf.st_mode))  return "lnk";
    else if (S_ISSOCK(buf.st_mode)) return "sck";
    else return " - ";
}

char *get_user(struct stat buf) {
    struct passwd *uinfo = getpwuid(buf.st_uid);
    return uinfo->pw_name;
}

char *get_group(struct stat buf) {
    struct group *ginfo = getgrgid(buf.st_gid);
    return ginfo->gr_name;
}

void list(char *dir) {
    DIR *dp;
    struct dirent *dent;
    char fname[512];
    struct stat buf;

	dp = opendir(dir);
    if (dp == NULL) {
		perror("opendir");
		exit(0);
    }

    while ((dent = readdir(dp)) != NULL) {
		sprintf(fname, "%s/%s", dir, dent->d_name);
		if (lstat(fname, &buf) < 0) {
			perror("lstat");
		}
		printf(" %s \t %s.%s \t %9d \t %s \n", file_type(buf), get_user(buf), get_group(buf), (int)buf.st_size, dent->d_name);
    }
    
    closedir(dp);
}

int main(int argc, char *argv[]) {

	char cwd[PATH_MAX];

	if (argc == 2 && getcwd(cwd, sizeof(cwd)) != NULL) {
		// ls -al <current_dir>
		list(cwd);
	} else if (argc == 3) {
		// ls -al <dir>
		list(argv[2]);
	} else {
		printf("An error has occurred!\nUsage: list_dir <pathname>\n");
	}

    return 0;
}
```
___

U nastavku napišite `Makefile` i pravilo `zadatak` za prevođenje i povezivanje gornjeg programa.

Nakon izvršavanja `make` pravila, testirajte vaš program:

``` bash
./zadatak list_dir <ime_direktorija>
ili
./zadatak list_dir
```
___

Ako je sve u redu, gotovi ste s vježbom!

___

Preostaje vam samo da napravite `.tar` datoteku od direktorija `vjezba7` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
