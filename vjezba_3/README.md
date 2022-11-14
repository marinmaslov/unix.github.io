# 🚀 Vježba 3: Standardni ulaz i izlaz

# Zadatak 📋

Zadatak ove vježbe je napraviti 4 programa.

<b>Prvi program `prvi.c`</b> se pokreće bez argumenata naredbenog retka, a sve što korisnik upiše ispisuje se na terminalu.

<b>Drugi program `drugi.c`</b> se pokreće tako da korisnik unese jednu ili više datoteka kao argumente naredbenog retka. Program zatim otvara redom te datoteke te njihov sadržaj ispisuje na terminal.

<b>Treći program `treci.c`</b> napišite kao kombinaciju prva dva programa i to na način da ako korisnik unese jednu ili više datoteka, program sadržaj tih datoteka redom ispisuje na terminal, a u slučaju da argument(i) ne budu datoteke program ispisuje sve što je korisnik utipkao.

<b>Četvrti program `cetvrti.c`</b> napišite tako da treći program prepravite na način da je funkcija koja ispisuje sadržaj datoteka napisana u zasebnoj datoteci izvornog koda `ispisi.c` (dakle, koristit ćete `header` datoteku `ispisi.h` preko koje ćete tu funkciju uključit u vaš program).

Nakon toga napišite `Makefile` datoteku sa <b>pravilima</b> za prevođenje i povezivanje za sva 4 programa.

U konačnici direktorij `vjezba3` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

Slobodno se koristite i uputama od prethodnih vježbi!
___

## Upute 🧭

### 1. korak: Stvaranje `Makefile`datoteke s pravilima za prevođenje i povezivanje

Potrebno je napraviti 3 pravila: `prvi`, `drugi` i `treci`. `Makefile` napišite sami!

Poslužite se slobodno uputama za [vježbu 1](../vjezba_1/README.md).

### 2. korak: Izvršavanje pravila `prvi`, `drugi` i `treci`

Nakon što ste napisali pravila u vašu `Makefile` datoteku, vrijeme je da ih izvršite i time prevedete i povežete vaše programe.

Ako je sve prošlo uspješno programe možete izvršiti i to na sljedeći način:

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
Vidimo da su nam se stvorile izvršne datoteke `prvi`, `drugi` i `treci`. U nastavku ćemo ih izvršiti (naravno, s argumentima).

### 3. korak: Izvršavanje programa `prvi`, `drugi` i `treci`

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

### 4. korak: Prepravite drugi program i `Makefile` datoteku na sljedeći način:

Drugi primjer preradite na način da se kao argument može zadati više datoteka. Program otvara datoteke redom kojim su navedene i ispisuje ih. Dopunite Makefile datoteku sa pravilom za prevođenje ovog primjera.

Napomena: Ne uređujte datoteku `drugi.c` već kopirajte istu te uređujte kopiju (kopiju nazovite `drugi2.c`).

___

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba1` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
