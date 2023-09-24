# 🚀 Vježba 3: Standardni ulaz i izlaz
## Sažetak 📃

___

## Zadatak 📋

Zadatak ove vježbe je napraviti 4 programa.

<b>Prvi program `prvi.c`</b> se pokreće bez argumenata naredbenog retka, a sve što korisnik upiše ispisuje se na terminalu.

<b>Drugi program `drugi.c`</b> se pokreće tako da korisnik unese jednu ili više datoteka kao argumente naredbenog retka. Program zatim otvara redom te datoteke te njihov sadržaj ispisuje na terminal.

<b>Treći program `treci.c`</b> napišite kao kombinaciju prva dva programa i to na način da ako korisnik unese jednu ili više datoteka, program sadržaj tih datoteka redom ispisuje na terminal, a u slučaju da argv(i) ne budu datoteke program ispisuje sve što je korisnik utipkao.

<b>Četvrti program `cetvrti.c`</b> napišite tako da treći program prepravite na način da je funkcija koja ispisuje sadržaj datoteka napisana u zasebnoj datoteci izvornog koda `ispisi.c` (dakle, koristit ćete `header` datoteku `ispisi.h` preko koje ćete tu funkciju uključit u vaš program).

Nakon toga napišite `Makefile` datoteku s <b>pravilima</b> za prevođenje i povezivanje za sva 4 programa.

U konačnici direktorij `vjezba3` u kojem se nalaze svi vaši programi i `Makefile` datoteka komprimirajte u `.tar` datoteku te istu učitajte na elearning.

___

## Upute [🧭](rjesenja/README.md)

Ova vježba je spoj svih dosadašnjih vježbi (neki zadaci su čak i isti) pa se slobodno koristite i uputama od prethodnih vježbi!

Što će vam od uputa bit potrebno:
- Prvi program je sličan kao i [prvi primjer iz vježbe 2](https://marinmaslov.github.io/unix.github.io/vjezba_2/primjeri), samo je potrebno preurediti funkciju `printf(...)`
- Drugi program je isti kao i četvrti program u vježbi 2. Sjetite se koju naredbu možete koristiti da program, s novim imenom, kopirate u vaš novi direktorij `vjezba3`.
- Kada napišete prvi i drugi program, kombinacijom njihovih kodova možete dobiti treći pregram. **Hint:** Ključ je u grananju!
- Četvrti program koristi znanje stečeno u [vježbi 1](https://marinmaslov.github.io/unix.github.io/vjezba_1/)

<a href="rjesenja/README.md" style="visibility:hidden;">RJEŠENJA</a>

___

Sve što vam preostaje je da kao i u prošloj vježbi napravit `.tar` datoteku od direktorija `vjezba3` te istu učitate na elearning (hint: `.tar` datoteku ćete prebaciti na lokalno računalo pomoću WinSCP programa). 
