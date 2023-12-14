# 💻 Obitelj `exec` funkcija

## 1. Popis funkcija

Funkcije iz `exec` obitelji u C-u/Linuxu zamjenjuju trenutnu sliku procesa novom slikom procesa. Mogu se koristiti za pokretanje C programa pomoću drugog C programa. Postoje različite verzije ovih funkcija, a ovdje su neke od njih:

- `execl`: Ova funkcija prima listu argumenata kao zasebne argumente funkcije.
- `execv`: Ova funkcija prima listu argumenata kao polje pokazivača na stringove.
- `execle`: Slično kao `execl`, ali omogućuje postavljanje okoline za novi proces.
- `execve`: Slično kao `execv`, ali omogućuje postavljanje okoline za novi proces.
- `execlp`: Pretražuje putanju okoline za program koji se izvršava.
- `execvp`: Pretražuje putanju okoline za program koji se izvršava.

## 2. Značenje slova koja slijede prefiks "exec"

Slova koja slijede prefiks "exec" imaju sljedeća značenja:
- `l`: Lista argumenata se prenosi kao zasebni argumenti funkcije.
- `v`: Lista argumenata se prenosi kao polje pokazivača na stringove.
- `e`: Omogućuje postavljanje okoline za novi proces.
- `p`: Pretražuje putanju okoline za program koji se izvršava[3].

Ove funkcije koriste se za zamjenu trenutnog procesa novim procesom, pri čemu novi proces može biti drugi program napisan u C-u.
