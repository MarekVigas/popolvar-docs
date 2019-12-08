# Zadanie 3 – Popolvár
## Marek Vigaš

## Vytvorenie grafu
Kazdy vrchol grafu je reprezentovany nasledujucim structom. Nasledne je alokovane pole o velkosti m*n (podla vstupu). Nasledne su vsetky vrcholy grafy ulozene za sebou postupne do tohto pola. Vrchol vie k svojim susedom pristupovat pomocou pointrov. Pokial vrchol niektoreho suseda nema ukladame tam hodnotu ```NULL```. Zaroven su vsetky teleporty udrziavane v linked listoch. Pre kazdy typ teleportu existuje link list teleportov rovnakeho typu.
```c
typedef struct node {
    char type;
    unsigned short x;
    unsigned short y;
    struct node *top, *down, *right, *left, *cameFrom;
    unsigned short g;
    unsigned short f;
    unsigned short h;
    bool closedSet;
    bool openSet;
} Node;
```

## Metoda hladania
Na vyhladavanie v grafoch som vyuzil [A*](https://en.wikipedia.org/wiki/A*_search_algorithm) algoritmus. Rovnako ako dijkstrov alogritmus si tento algoritmus pre kazdy vrchol pocita *g* a *h*. *g* reprezentuje aka zlozita bola cesta od zaciatku k danemu vrcholu. *h* reprezentuje heristicku vzdialenost vrcholu k cielovemu vrcholu. *h* pocitame pomocou [Manhattan distance](https://en.wikipedia.org/wiki/Taxicab_geometry). 
```
abs(start->x - end->x) + abs(start->y - end->y)
```
Nasledne mozme pre kazdy vrchol urcit hodnotu ```f = g + h```. Podla hodnoty *f* nasledne vyberame dalsie vrcholy na preskumanie v grafe. Ked *h* urcime rovne 0 tak sa A* srpava rovnako ako obycajny dijkstrov algoritmus. Toto nam vyrazne pomaha pri zrychleni behu alrgoritmu, pretoze nemusime preskumat vsetky vrcholy grafu. Tento pristup ma vsak nevyhodu v tom ze ak mame v grafe vrchol ktory sa nachadza v opacnom smere od ciela ale mozes nas k cielu dostat skor nebude objaveny. Tento pripad je v popolvarovi teleport.

Na vyhladavanie dalsieho vhodneho suseda na preskumanie sa pouziva linked list a nie priority queue. Tato datova struktura je rychla na vkladanie, kedze vzdy pridavame na zaciatok a prvky v tejto datovej strukture nie su nijak utriedene. Na vyhladavanie vsak tato datova struktura nie je vhodna.    

![alt text](/images/wrongPath.png "Wrong Path")
V tomto priklade mozme vydiet ze popolvar nevyuzil teleport aj ked bol generator aktivny. V pripade teleportov mozeme tomuto efektu mozme zabranit tak ze budeme pokracovat v hladani aj ked sme sa nejakou cestou dostali do ciela a urcime *h* pre vsetky vrcholy 0. 

V pripade ze sa na mape nachadza generator. Popolvar pojde ku generatoru a nasledne k drakovi. Nasledne vyskusa vsetky mozne permutacie pre poradie princezien a vyberie permutaciu, ktora mala najrychlejsiu cestu. Ked princezne ocislujeme mozme ich navstivit v tychto poradiach. Pokial cast cesty niektorej permutacie je dlhsia ako aktulna najlepsia cesta vieme ze mozme pokracovat na dalsiu permutaciu bez toho aby sme hladal zvysne cestu pre vybranu permutaciu.

```
0,1,2
1,0,2
2,0,1
0,2,1
1,2,0
2,1,0
```

![alt text](/images/21_1.png "Besst Path")
Na tomto trivialnom pripade mozme vydiet ze bola zvolena najlepsia permutacia.  

## Testovanie
Na testovanie som znova zvolil netradicny sposob. Vytvoril som webovu aplikaciu, ktora sluzi ako vizualizacie a postupne animuje vytvorenu cestu. [https://popolvar.surge.sh/](https://popolvar.surge.sh/). Tato vizualizacie mapy a cesty bola nasledne zverejnena na [githube](https://github.com/MarekVigas/popolvar-map) pre buduce generacie. Vyziva vizualne testovanie vysledkov takzvanu metodou "pozriem a vidim ze to je zle" :)

Pre vstupnu mapu a cestu vytvori nasledovnu animaciu cesty s ocislovanymi bodmi.
```
3 15 47
CCCNNNNNNNN0CCN
HCHCCCCCCNNHHPN
HGCNNNNND0NNNNN

0 0
1 0
1 1
1 2
1 1
2 1
3 1
4 1
5 1
6 1
7 1
8 1
8 2
9 2
11 0
12 0
13 0
13 1
```
![alt text](/images/20_0.png "Besst Path")
![alt text](/images/20_1.png "Besst Path")

Vyskusal som viacero vlsatnych mensich scenarov, ale aj scenare z turingu. 

### Vlastne testy 
![alt text](/images/20_0.png "Besst Path")
![alt text](/images/20_1.png "Besst Path")

![alt text](/images/21_0.png "Besst Path")
![alt text](/images/21_1.png "Besst Path")

![alt text](/images/22_0.png "Besst Path")
![alt text](/images/22_1.png "Besst Path")

### Testy a vysledky z turingu

![alt text](/images/0_0.png "Besst Path")
![alt text](/images/0_1.png "Besst Path")

```
Testujem scenar #1:
5 5 30
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/1_0.png "Besst Path")
![alt text](/images/1_1.png "Besst Path")

```
Testujem scenar #2:
7 7 47
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/2_0.png "Besst Path")
![alt text](/images/2_1.png "Besst Path")

```
Testujem scenar #3:
5 30 108
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/3_0.png "Besst Path")
![alt text](/images/3_1.png "Besst Path")

```
Testujem scenar #4:
11 11 66
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/4_0.png "Besst Path")
![alt text](/images/4_1.png "Besst Path")


```
Testujem scenar #5:
40 50 272
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/5_0.png "Besst Path")
![alt text](/images/5_1.png "Besst Path")

```
Testujem scenar #6:
80 20 371
PRILIS DLHO -- Princezne boli zachranene, ale trvalo to prilis dlho: 97. (optimalny cas 90)
```

![alt text](/images/6_0.png "Besst Path")
![alt text](/images/6_1.png "Besst Path")

```
Testujem scenar #7:
100 10 425
PRILIS DLHO -- Princezne boli zachranene, ale trvalo to prilis dlho: 48. (optimalny cas 47)
```

![alt text](/images/7_0.png "Besst Path")
![alt text](/images/7_1.png "Besst Path")

```
Testujem scenar #8:
950 10 3175
OK -- Princezne boli zachranene v najkratsom moznom case!
```

![alt text](/images/8_0.png "Besst Path")
![alt text](/images/8_1.png "Besst Path")

```
Testujem scenar #9:
150 150 933
OK -- Princezne boli zachranene v najkratsom moznom case!
```

## Zaver
Niektore testy nenajdu optimalnu cestu pretoze som zvolil strategiu ak sa v mape nachadza generator tak sa pokusi vzdy najskor dostat ku generatoru. Preto ak je vyhodnejsie najskor zabit draka a generator aktivovat az potom ako zachranujeme princezne tuto cestu nenajdem. 

Najvacsie zrychlenie by som dosiahol pokial by som doimplementoval priority queue. Co by znamenalo ze scenare 8 a 9 by zbehli vyrazne rychlejsie a vo stanovenom case. Priority queue je vsak zanedbatelna cast riesenia, pretoze riesenie je pripravene na pouzitie s priority queue a vacsina novsich jazykov ako c++ ma priamo v jazyku implementovanu priority queue.

Je mozne dosiahnut ale lepsie pamatove vyzitie. Vsetky pointre ktore maju ulozene pointre na susedov mozme pocas behu programu pocitat. Pretoze lavy sused sa nachadza v poli na indexe ```i-1``` a pravy sused sa nachadza na ```i+1```. Rovnako mozme takto vypocitat aj suseda nad a pod vrcholom. ```i +- numberOfColumns```. Avsak tieto pointre som tam ponechal z dovodu zlepsenia prehladnosti kodu. 