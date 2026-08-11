# Kokpit rutin — ZATE

Denní odbavení disponenta na jedné stránce. Čte textové exporty ze ZATE,
drží si, co už je vyřízené, a nic neposílá ven — data zůstávají v prohlížeči.

Jeden soubor `index.html`, bez závislostí a bez serveru. Stačí otevřít.

## Reporty

Aplikace má dva samostatné listy, přepínají se v hlavičce.

### Denní rutiny

| Report | Název | Dlaždice |
|---|---|---|
| 500 116 | Unconfirmed Customer Orders | Nepotvrzené zakázky |
| 212 020 | Current purchase requisitions | Aktuální BANFy |
| 500 108 | Fehlerprotokolle Lieferanlage | Nevyklopené dodávky |

## Vyzkoušení bez dat

Na prázdném přehledu je vedle importu tlačítko **Načíst ukázková data**
(později i v dialogu importu). Naplní všechny čtyři reporty vymyšlenými
doklady, materiály a dodavateli — nic z toho neodpovídá skutečné firmě,
jen tvarem sedí na formát exportu.

Ukázka prochází stejnými parsery jako ostrá data, takže zároveň ověřuje,
že formát souhlasí. U objednávek je zastoupena každá z pěti fází včetně
jedné s cenovou odchylkou. Tlačítko *Vymazat data* ji odstraní.

## Jak se to používá

1. V SAPu spusť ZATE a report ulož jako lokální soubor (text s tabulátory).
2. Nahoře **Import ze ZATE** — všechny tři soubory můžeš vybrat naráz, nebo
   obsah exportu jen vložit do textového pole.
3. Typ reportu se pozná sám podle hlavičky, nic se nevybírá.

Dlaždice ukazuje počet otevřených položek a upozorní na ty po termínu.
Po rozkliknutí lze každou položku odbavit — u zakázek se zadá potvrzené
množství a datum, u BANFů a chyb stačí potvrdit. Omylem odbavenou položku
vrátí tlačítko **Vrátit**.

### Potvrzení objednávek — samostatný list

Potvrzování objednávek u dodavatele není denní rutina, takže nesedí mezi
dlaždicemi. Má vlastní list dostupný z hlavičky (report `ME2A`); počet
otevřených položek nese odznak u jeho názvu, červený, když je něco po
termínu.

Objednávka prochází pěti fázemi, které jsou vidět na první pohled —
odznakem u řádku a filtrem s počty nad tabulkou:

| Fáze | Kdy nastane |
|---|---|
| **Objednáno** | odesláno dodavateli, ještě nepotvrdil |
| **Potvrzeno** | dodavatel potvrdil, do dodání víc než tři dny |
| **Na cestě** | potvrzeno a do dodání zbývají nejvýš tři dny |
| **Po termínu** | termín uplynul a zboží není zapsané jako doručené |
| **Doručeno** | zapsáno doručení, položka je vyřízená |

*Na cestě* je odvozené z termínu, ne ze skutečné přepravy — aplikace žádná
data o zásilkách nemá. Znamená „mělo by být na cestě".

Dokud dodavatel nepotvrdil, nabízí řádek **Potvrdit** — i u položky po
termínu, protože tam je hlavní úkol zjistit nový termín. Po potvrzení se
čeká na dodání tlačítkem **Doručeno**; kdyby dodavatel termín ještě
posunul, přepíše se potvrzení odkazem **Upravit**.

### Kontrola potvrzení

Při potvrzování se proti potvrzení od dodavatele porovnávají tři údaje:
**číslo objednávky**, **datum doručení** a **cena**. Přepíšeš je podle
toho, co dodavatel poslal; co se liší od objednávky, se uloží jako
odchylka.

Položka pak nese odznak *Souhlasí*, nebo *N odchylek* s výpisem po najetí
myší. Změněný termín a cena jsou vidět přímo v řádku pod původní hodnotou.
Rozdíl v termínu se počítá ve dnech, u ceny i v procentech.

V hlavičce se dá zúžit výběr na vlastní dispo kódy; volba se pamatuje.

## Co je dobré vědět

- **Zapsané údaje přežijí nový import.** Páruje se přes doklad + položku,
  takže zítřejší export nepřepíše, co jsi zadal — a to i u položek, které
  zůstávají otevřené, jako je potvrzená objednávka čekající na dodání.
- **Report 500 108 se v každém běhu opakuje.** Řádky se slučují podle
  dokladu, položky a chybové zprávy — drží se poslední běh a počet výskytů.
- **Kódování.** Exporty ze SAPu chodí občas ve windows-1252; pozná se to
  a přečte správně.
- **Sloupce reportu ME2A.** Čtou se pozičně v pořadí: objednávka, položka,
  materiál, krátký text, dodavatel, název dodavatele, množství, MJ, datum
  dodání, cena, měna, dispo, potvrzeno. Poslední dva jsou nepovinné.
  Pokud tvůj export vypadá jinak, stačí upravit `parseObjednavky`.
- **Čísla dokladů.** Objednávka vždy začíná `70` — podle toho se v exportu
  poznají datové řádky od hlaviček a mezisoučtů. Číslo materiálu je
  sedmimístné a začíná dvojkou nebo čtyřkou; zobrazuje se s tečkami
  (`2.090.290`), i když je export pošle bez nich. Hledat lze v obou
  tvarech, `2090290` najde totéž co `2.090.290`.
- **Zapsané odchylky jsou snímek k okamžiku potvrzení.** Když se objednávka
  v SAPu později změní, řádek porovnává potvrzenou hodnotu s tou novou,
  ale uložený text odchylky zůstává původní.
- **Data jsou jen lokálně.** Tlačítko *Vymazat data* smaže import i stav
  odbavení. Ze SAPu se nemaže nic.

Ostrá verze by místo importu četla a zapisovala do SAP přes OData / RFC.
