# 🚀 Vježba 2: Argumenti naredbenog retka i rad s datotekama

## Zadatak 📋

U okviru vježbe proučite [primjere](primjeri/README.md) programskog koda (c jezik) za preuzimanje argumenata naredbenog retka i čitanje datoteke.

Otvorite svoj `home` direktorij te se pozicionirajte u `unix` direktorij (stvoren u prethodnim vježbama) te stvorite direktorij imena `vjezba2`. U direktoriju `vjezba2` isprogramirajte zadane primjere te za iste napišite `Makefile` datoteku s pravilima za prevođenje i povezivanje.

Prepravite kod drugog primjera na način da se kao argument može zadati više datoteka. Program otvara datoteke redom kojim su navedene i ispisuje ih. Dopunite zatim `Makefile` datoteku s pravilom za prevođenje trećeg primjera.

Slobodno se koristite i uputama od prethodne vježbe.

___

## Upute 🧭

Osim uputa raspisanih u nastavku, korisno je pročitati i: [Datoteke u C jeziku](../dodatno/datoteke.md).

### 1. Stvaranje `Makefile` datoteke s pravilima `prvi`, `drugi` i `treci`

Potrebno je napraviti 3 pravila: `prvi`, `drugi` i `treci`. Za pisanje `Makefile` pravila, poslužite se prethodnom vježbom ([4. Pisanje Makefile pravila]([primjeri/README.md](https://marinmaslov.github.io/unix.github.io/vjezba_1/#4-pisanje-makefile-pravila-)))!

___

### 2. Izvršavanje pravila `prvi`, `drugi` i `treci`

Nakon što ste napisali pravila u vašu `Makefile` datoteku, vrijeme je da ih izvršite i time prevedete i povežete vaše programe.

___

#### 📗 Pravilo `prvi`

Naredba:
```bash
make prvi
```

Ispis:
```bash
/usr/bin/gcc -Wall -c prvi.c
/usr/bin/gcc -Wall prvi.c -o prvi
```

___

#### 📘 Pravilo `drugi`

Naredba:
```bash
make drugi
```

Ispis:
```bash
/usr/bin/gcc -Wall -c drugi.c
/usr/bin/gcc -Wall drugi.c -o drugi
```

___

#### 📙 Pravilo `treci`

Naredba:
```bash
make treci
```

Ispis:
```bash
/usr/bin/gcc -Wall -c treci.c
/usr/bin/gcc -Wall treci.c -o treci
```

Nakon uspješnog izvršavanja svih pravila, u direktoriju ćete imati sljedeće datoteke:
```bash
-rwxr-xr-x 1 mmaslo00 stud 7711 Oct 31 18:36 drugi
-rw-r--r-- 1 mmaslo00 stud  667 Oct 31 18:31 drugi.c
-rw-r--r-- 1 mmaslo00 stud 2120 Oct 31 18:36 drugi.o
-rw-r--r-- 1 mmaslo00 stud  317 Oct 31 18:34 makefile
-rwxr-xr-x 1 mmaslo00 stud 6832 Oct 31 18:34 prvi
-rw-r--r-- 1 mmaslo00 stud  153 Oct 31 18:30 prvi.c
-rw-r--r-- 1 mmaslo00 stud 1552 Oct 31 18:34 prvi.o
-rw-r--r-- 1 mmaslo00 stud   74 Oct 31 18:31 test.txt
-rwxr-xr-x 1 mmaslo00 stud 7542 Oct 31 18:36 treci
-rw-r--r-- 1 mmaslo00 stud  546 Oct 31 18:31 treci.c
-rw-r--r-- 1 mmaslo00 stud 2032 Oct 31 18:36 treci.o
```

Vidimo da su se stvorile izvršne datoteke `prvi`, `drugi` i `treci`. U nastavku ćemo ih izvršiti (naravno, s argumentima).

___

### 3. Pokretanje programa

#### 📗 Izvršavanje programa `prvi`

Prvi program ispisuje sve argumente koji mu se proslijede. Izvršite ga tako da mu proslijedite neke vaše proizvoljne argumente, npr.:

```bash
./prvi argument1 argument2 argument3
```

Ispis za ovaj primjer će biti sljedeći:

```bash
argv[0]: ./prvi
argv[1]: argument1
argv[2]: argument2
argv[3]: argument3
```

Primijetite da se kao 0. argument ispisuje `./prvi` jer je i on argument naredbenog retka.

___


#### 📘 Izvršavanje programa `drugi`

Drugi program čita sadržaj datoteke i ispisuje ga. Ime datoteke se zadaje kao argument naredenog retka.

```bash
./drugi test.txt
```

Ispis za ovaj primjer će biti sljedeći:

```bash
Ovaj program
ispisuje
linije
datoteke
koja mu se
proslijedi
kao argument!
```

___


#### 📙 Izvršavanje programa `treci`

Treći program ima funkcionalnost UNIX naredbe cat. Program ispisuje sadržaj svih datoteka čija su imena zadana kao argumenti naredbenog retka. Ukoliko niti jedna datoteka nije zadana, program kopira standardni ulaz na standardni izlaz.

Iz direktorija s primjerima ste kopirali `test.txt`, dodatne datoteke (minimalno dvije) napravite sami.

```bash
./treci test.txt
```

Ispis za ovaj primjer će biti sljedeći:

```bash
Ovaj program
ispisuje
linije
datoteke
koja mu se
proslijedi
kao argument!

+ sadržaj vaših datoteka
```

___

### 4. Prepravljanje drugog primjera

Drugi primjer je potrebno prepraviti tako da se kao argument može zadati više datoteka koje program otvara redom kojim su navedene i ispisuje njihov sadržaj. Također je potrebno i nadopuniti `Makefile` datoteku sa pravilom za prevođenje ovog primjera, tj. kopirajte `drugi.c` u novu datoteku `cetvrti.c` i zatim napišite pravilo `cetvrti`.

Za kopiranje koristit naredbu `cp`:
``` bash
cp drugi.c cetvrti.c
```

**Pomoć:**
Kako biste čitali i ispisivali sadržaje više datoteka poslužite se `for` petljom na način da iterirate kroz niz pokazivača na znakovne nizove (strings) od indeksa 1 (jer je na indeksu 0 ime programa). Unutar `for` petlje vršite čitanje i ispis trenutne datoteke `argv[i]` gdje je `i` brojač kojeg ćete koristiti u `for` petlji. Što znači da bi vam otvaranje datoteke `open(...)`, čitanje sadržaja datoteke `read(...)`, ispisivanje datoteke na naredbenu liniju `write(...)`, provjera je li datoteka dobro pročitana `if (n == -1) {` i zatvaranje datoteke `close(...)` trebalo biti u tijelu `for` petlje.

___

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba2` te istu učitate na Merlin (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
