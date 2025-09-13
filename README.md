# Relações de Parentesco em Prolog
## - Parte teórica
Usando fatos e regras em Prolog, é possível montar uma árvore genealógica que represente relações como pais, filhos, irmãos, avós, netos, etc., e consultá-la para verificar essas relações.  
  
No exemplo abaixo, podemos perceber o quão fácil é construir essas relações.

---
## Fatos
Aqui, é definido os homens e mulheres
```prolog
female(ana).
female(maria).
female(sofia).

male(joao).
male(caio).
male(pedro).
```
E abaixo, foi definido o parentesco.
```prolog
parent(ana, caio).
parent(ana, maria).
parent(joao, caio).
parent(joao, maria).

% Filhos dos filhos
parent(caio, pedro).
parent(caio, sofia).
```
A partir dessas informações, são definidas regras para que o programa compreenda como as relações são formadas.

---
## Regras
Com as informações acima, um ser humano já é capaz deduzir o parentesco de cada pessoa, porém, para que o programa consiga, é necessária a definição de regras.
```prolog
% X é mãe/pai de Y
father(X,Y) :- parent(X,Y), male(X).
mother(X,Y) :- parent(X,Y), female(X).

% X é avô/avó de Y
grandfather(X,Y) :- parent(X,Z), parent(Z,Y), male(X).
grandmother(X,Y) :- parent(X,Z), parent(Z,Y), female(X).

sister(X,Y) :- parent(Z,X), parent(Z,Y), female(X), X \= Y.
brother(X,Y) :- parent(Z,X), parent(Z,Y), male(X), X \= Y.

haschild(X) :- parent(X,_).
```

---
## Consultas
Agora, com todas essas definições, só nos resta realizar as consultas para verificar se o programa está funcionando corretamente.
```prolog
% Ana é mãe de caio?
?- mother(ana, caio).
true.

% Consulta filhos de João
?- parent(joao, X).
X = caio. ;
X = maria.

% Sofia é irmão de pedro?
?- brother(sofia, pedro).
false.
```
---
## - Parte prática
Para a parte prática, eu trouxe dois exemplos mais complexos que achei sobre árvore genealógica, pois mostravam relações de parentesco um pouco mais distantes.

## Exemplo 1
```prolog
/* Facts */
male(jack).
male(oliver).
male(ali).
male(james).
male(simon).
male(harry).
female(helen).
female(sophie).
female(jess).
female(lily).
parent_of(jack,jess).
parent_of(jack,lily).
parent_of(helen, jess).
parent_of(helen, lily).
parent_of(oliver,james).
parent_of(sophie, james).
parent_of(jess, simon).
parent_of(ali, simon).
parent_of(lily, harry).
parent_of(james, harry).

/* Rules */
father_of(X,Y):- male(X),
    parent_of(X,Y).
mother_of(X,Y):- female(X),
    parent_of(X,Y).

grandfather_of(X,Y):- male(X),
    parent_of(X,Z),
    parent_of(Z,Y).
grandmother_of(X,Y):- female(X),
    parent_of(X,Z),
    parent_of(Z,Y).

sister_of(X,Y):- %(X,Y or Y,X)%
    female(X),
    father_of(F, Y), father_of(F,X), X \= Y.
sister_of(X,Y):- female(X),
    mother_of(M, Y), mother_of(M,X), X \= Y.

brother_of(X,Y):- %(X,Y or Y,X)%
    male(X),
    father_of(F, Y), father_of(F,X),X \= Y.
brother_of(X,Y):- male(X),
    mother_of(M, Y), mother_of(M,X),X \= Y.

aunt_of(X,Y):- female(X),
    parent_of(Z,Y), sister_of(Z,X),!.
uncle_of(X,Y):-
    parent_of(Z,Y), brother_of(Z,X).

ancestor_of(X,Y):- parent_of(X,Y).
ancestor_of(X,Y):- parent_of(X,Z),
    ancestor_of(Z,Y).
```
Nesse exemplo, além das relações parentais apresentadas antes, também inclui tio/tia e todos os ancestrais de uma pessoa (ancestor_of)    
Testando no codespaces:    
  
![exemplo 1](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExdzlvMXAxMjRlYzJkcGtqbm0xNHFkaDV6dTY5dmI1NXduZWU4cWVrZiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/Wi52vQmFc9clQY84B9/giphy.gif)

## Exemplo 2
Eu não consegui achar nenhum exemplo com relações mais longes que funcionasse, então eu mesmo adicionei um cousin_of para consultar primos, e um greatuncle_of para consultar tio-avôs.  
  
Foi um pouco confuso, pois na parte dos primos, eu tive que adicionar "X @< Y" em cousin_of, porque o Prolog devolvia o mesmo par duas vezes, só que invertido. O @< é um comparador que diz quem ‘vem primeiro’ na ordem alfabética dos nomes, então ele garante que só aparece uma versão do par, tipo (harry, simon) e não (simon, harry).
```prolog
/* Facts */
male(jack).
male(oliver).
male(ali).
male(james).
male(simon).
male(harry).

male(caio).

female(helen).
female(sophie).
female(jess).
female(lily).

parent_of(paul, jack).
parent_of(paul, caio).
 
parent_of(jack,jess).
parent_of(jack,lily).
parent_of(helen, jess).
parent_of(helen, lily).
parent_of(oliver,james).
parent_of(sophie, james).
parent_of(jess, simon).
parent_of(ali, simon).
parent_of(lily, harry).
parent_of(james, harry).
 
/* Rules */

sibling_of(X,Y) :-
    parent_of(Z, X),
    parent_of(Z, Y),
    X \= Y,
    !.


father_of(X,Y):- male(X),
    parent_of(X,Y).
 
mother_of(X,Y):- female(X),
    parent_of(X,Y).
 
grandfather_of(X,Y):- male(X),
    parent_of(X,Z),
    parent_of(Z,Y).
 
grandmother_of(X,Y):- female(X),
    parent_of(X,Z),
    parent_of(Z,Y).
 
sister_of(X,Y):- %(X,Y or Y,X)%
    female(X),
    father_of(F, Y), father_of(F,X),X \= Y.
 
sister_of(X,Y):- female(X),
    mother_of(M, Y), mother_of(M,X),X \= Y.
 
aunt_of(X,Y):- female(X),
    parent_of(Z,Y), sister_of(Z,X),!.
 
brother_of(X,Y):- %(X,Y or Y,X)%
    male(X),
    father_of(F, Y), father_of(F,X),X \= Y.
 
brother_of(X,Y):- male(X),
    mother_of(M, Y), mother_of(M,X),X \= Y.
 
uncle_of(X,Y):-
    parent_of(Z,Y), brother_of(Z,X).


ancestor_of(X,Y):- parent_of(X,Y).
ancestor_of(X,Y):- parent_of(X,Z),
    ancestor_of(Z,Y).

sibling_of(X,Y) :-
    parent_of(Z, X),
    parent_of(Z, Y),
    X \= Y.

greatuncle_of(X, Y) :-
    male(X),
    grandfather_of(Z, Y),
    sibling_of(X, Z).

cousin_of(X, Y):-
    parent_of(Z, Y),
    parent_of(W, X),
    sibling_of(W, Z),
    X \= Y,
    X @< Y.
```
  
Aqui eu consulto os tio-avôs e primos existentes na árvore genealógica:  
  
![exemplo 2.1](https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExaWsxeXB3ODFqNnozN3hxaTFsMW0ydDU4YnJuODRueXRwaDk3aGZ4OSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/kaaUWGSYNfJ35ZrQqp/giphy.gif)  

Sem "X @< Y":  
  
![exemplo 2.2](https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExbnh3dnpzb2lneGQzeHo1Y3BiZWZvbjhueTFua3ppc3M1OGNwNHQxMiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/FxQbiziSRKcO1h4t0t/giphy.gif)

# Referências bibliográficas
https://www.educba.com/prolog-family-tree  
https://www.101computing.net/prolog-family-tree  
https://swi-prolog.discourse.group/t/recursion-for-going-through-a-family-tree-and-selecting-cousins/4568
