# 🚂 Rušeň a zlaté mince

Jednoduchá počítačová hra v štýle známej hry „hadík“. Namiesto hada však po tráve jazdí **rušeň**, ktorý ťahá **vagóny**. Rušeň zbiera **zlaté mince** a za každú mincu dostane ďalší vagón.

Hra je napísaná v jazyku **JavaScript** a beží v obyčajnom internetovom prehliadači. Nič netreba inštalovať. Je určená ako **príklad na učenie základov programovania**.

---

## 📁 Súbory

| Súbor | Čo v ňom je |
|---|---|
| `rusen.html` | **Verzia 1:** najjednoduchšia hra. Rušeň aj vagóny sú farebné štvorčeky, minca je žltý krúžok. |
| `rusen_v2.html` | **Verzia 2:** rovnaká hra, ale s krajšou grafikou. Parná lokomotíva, osobné vagóny, točiace sa kolesá a minca so znakom €. |
| `README.md` | Tento návod. |

Odporúčame začať **verziou 1**. Keď jej rozumieš, pozri sa, čo sa zmenilo vo verzii 2.

---

# 🎮 ČASŤ 1: Ako sa hrá

## Spustenie hry

1. Nájdi súbor `rusen.html` alebo `rusen_v2.html`.
2. Dvakrát naň klikni.
3. Hra sa otvorí v prehliadači (Chrome, Edge, Firefox…) a hneď sa začne.

## Ovládanie

| Kláves | Čo urobí |
|---|---|
| ⬆️ šípka hore | rušeň zabočí hore |
| ⬇️ šípka dole | rušeň zabočí dole |
| ⬅️ šípka doľava | rušeň zabočí doľava |
| ➡️ šípka doprava | rušeň zabočí doprava |
| medzerník | nová hra (keď hra skončila) |

## Pravidlá

- Rušeň ide **stále dopredu**. Ty mu iba šípkami meníš smer.
- Vagóny idú **presne po stopách** rušňa.
- Keď rušeň prejde cez **zlatú mincu**, dostaneš **1 bod** a vlak sa **predĺži o jeden vagón**.
- Rušeň sa nemôže otočiť priamo dozadu, lebo by narazil do vlastného vagóna.
- Hra **skončí**, keď rušeň narazí do **okraja** hracej plochy alebo do **vlastného vagóna**.

## Tip

Čím je vlak dlhší, tým je hra ťažšia. Premýšľaj dopredu, kam pôjdeš, aby si sa nezamotal do vlastných vagónov!

---

# 👩‍💻 ČASŤ 2: Ako sa takáto hra programuje

Otvor súbor `rusen.html` v textovom editore (napríklad Poznámkový blok, Notepad++ alebo Visual Studio Code). Uvidíš, že kód je rozdelený na očíslované časti a má veľa komentárov.

> 💡 **Komentár** je text pre ľudí, počítač ho ignoruje. V JavaScripte začína znakmi `//`.

## Z čoho sa skladá súbor

Súbor `.html` má tri časti:

1. **HTML:** kostra stránky. Nadpis, text a **plátno** (`<canvas>`), na ktoré kreslíme hru.
2. **CSS** (v značke `<style>`): ako stránka vyzerá, teda farby a okraje.
3. **JavaScript** (v značke `<script>`): samotný program, ktorý hru riadi.

## Hlavná myšlienka: hracia plocha zo štvorčekov

Hracia plocha je ako **šachovnica**. Každé políčko má dve súradnice:

- **x:** koľké políčko zľava (0, 1, 2, …)
- **y:** koľké políčko zhora (0, 1, 2, …)

```
      x=0  x=1  x=2  x=3
y=0   [ ]  [ ]  [ ]  [ ]
y=1   [ ]  [V]  [V]  [R]     ← R = rušeň na x=3, y=1
y=2   [ ]  [ ]  [ ]  [ ]
```

Pozor, **y rastie smerom dole** (nie hore ako v matematike).

## Krok 1: Premenné, čiže „škatuľky“ na údaje

**Premenná** je ako škatuľka s menom, do ktorej si program niečo uloží:

```js
let body = 0;          // koľko má hráč bodov
let koniecHry = false; // skončila už hra? (false = nie, true = áno)
```

## Krok 2: Vlak je zoznam políčok

Celý vlak je jeden **zoznam** (po anglicky *array*, po slovensky aj „pole“). Prvá položka je rušeň, ostatné sú vagóny:

```js
vlak = [
  { x: 5, y: 10 },   // [0] rušeň
  { x: 4, y: 10 },   // [1] 1. vagón
  { x: 3, y: 10 }    // [2] 2. vagón
];
```

## Krok 3: Smer pohybu

Smer si pamätáme v dvoch číslach, `dx` a `dy`. Tie hovoria, o koľko políčok sa rušeň posunie:

| Smer | dx | dy |
|---|---|---|
| doprava | 1 | 0 |
| doľava | -1 | 0 |
| dole | 0 | 1 |
| hore | 0 | -1 |

## Krok 4: Stláčanie klávesov

Program „počúva“ klávesnicu. Keď stlačíš kláves, spustí sa kúsok kódu:

```js
document.addEventListener("keydown", function (udalost) {
  if (udalost.key === "ArrowUp" && dy !== 1) {
    dx = 0; dy = -1;   // ideme hore
  }
  // ... a podobne pre ostatné šípky
});
```

Podmienka `dy !== 1` znamená „ak práve nejdeme dole“. Vďaka nej sa vlak neotočí priamo do seba.

## Krok 5: Pohyb vlaku (najdôležitejší trik!)

Ako posunúť celý vlak o jedno políčko? Netreba posúvať každý vagón zvlášť. Stačí:

1. **Pridať nový rušeň na začiatok** zoznamu (o jedno políčko ďalej v smere jazdy): `vlak.unshift(novyRusen)`
2. **Odstrániť posledný vagón z konca**: `vlak.pop()`

```
Predtým:   [V][V][R]
Pridáme:   [V][V][R][R]    ← nový rušeň vpredu
Odoberieme:   [V][R][R]    ← zmizol posledný vagón
```

Vyzerá to, akoby sa celý vlak posunul! Starý rušeň sa takto zmenil na prvý vagón.

**A čo keď zjeme mincu?** Vtedy posledný vagón **neodstránime**. Vlak tak bude o jeden vagón dlhší. Jednoduché, však?

## Krok 6: Kontrola nárazu

Pred každým krokom sa program pýta:

- **Je nové políčko mimo plochy?** (x alebo y je menšie ako 0, alebo väčšie ako plocha) → koniec hry
- **Je nové políčko tam, kde už stojí vagón?** Na toto prejdeme cyklom `for` celý zoznam a porovnáme súradnice → koniec hry

## Krok 7: Minca na náhodnom mieste

```js
minca = {
  x: Math.floor(Math.random() * POCET_POLICOK),
  y: Math.floor(Math.random() * POCET_POLICOK)
};
```

- `Math.random()` dá náhodné číslo od 0 do 1 (napríklad 0,73).
- Vynásobíme ho počtom políčok (0,73 × 20 = 14,6).
- `Math.floor` odreže desatinné miesta (14).

## Krok 8: Kreslenie

Na plátno kreslíme „štetcom“ (`stetec`):

```js
stetec.fillStyle = "red";           // vyber farbu
stetec.fillRect(x, y, sirka, vyska); // nakresli obdĺžnik
```

Pri každom kroku najprv **všetko zmažeme** a potom nakreslíme znova: mincu, vagóny aj rušeň. Takto funguje každá animácia, aj kreslený film!

## Krok 9: Herná slučka

Hra je v skutočnosti stále dookola opakovaný kúsok kódu:

```js
setInterval(krokHry, 150);  // každých 150 milisekúnd zavolaj krokHry
```

A `krokHry` urobí toto:

```
┌─→ pohni vlakom
│   skontroluj náraz a mincu
│   nakresli všetko
└── počkaj chvíľku a opakuj
```

Takto funguje takmer každá počítačová hra na svete.

---

## 🎨 Čo je nové vo verzii 2

Pravidlá hry sú **úplne rovnaké**. Zmenilo sa iba **kreslenie**:

- **Pomocné funkcie** `obdlznik()`, `kruh()` a `koleso()`. Rušeň sa z nich skladá ako zo stavebnice.
- **Rušeň z boku:** kabína, kotol, komín, dym, svetlo a kolesá.
- **Zrkadlenie:** keď ide rušeň doľava, obrázok otočíme príkazom `stetec.scale(-1, 1)`.
- **Točiace sa kolesá:** používajú počítadlo krokov a trochu matematiky (`Math.cos`, `Math.sin`).
- **Farebné vagóny:** farby sa striedajú vďaka zvyšku po delení (`%`).
- **Tráva ako šachovnica:** dva cykly `for` v sebe a pravidlo „ak je x + y párne, použi svetlejšiu zelenú“.

---

## 🧪 Úlohy na vyskúšanie

Skús v kóde niečo zmeniť, ulož súbor a v prehliadači stlač **F5** (obnoviť stránku).

**Ľahké**
1. Zmeň rýchlosť hry (`RYCHLOST`). Čo sa stane pri 50? A pri 500?
2. Zmeň farbu rušňa alebo vagónov.
3. Vo verzii 2 zmeň znak na minci z `"€"` na `"$"`.
4. Nech je na začiatku vlak dlhší, teda pridaj ďalší vagón do zoznamu `vlak`.

**Stredné**

5. Za každú mincu daj 10 bodov namiesto 1.
6. Nech sa hra po každej minci trošku zrýchli.
7. Pridaj druhú mincu naraz.

**Ťažšie**

8. Pridaj „jedovatú“ červenú mincu, po ktorej sa vlak **skráti** o jeden vagón.
9. Nech hra po náraze do okraja **neskončí**, ale rušeň sa objaví na opačnej strane plochy.
10. **Nájdi chybu!** Keď vlak ide doprava a ty veľmi rýchlo stlačíš ⬆️ a hneď ⬅️, vlak narazí sám do seba. Prečo sa to deje a ako by si to opravil?
    *Nápoveda: smer sa zmení dvakrát skôr, ako sa vlak stihne pohnúť.*

---

Príjemnú zábavu pri hraní aj programovaní! 🚂💰
