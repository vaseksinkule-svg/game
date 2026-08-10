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

U objednávek se proti potvrzení od dodavatele porovnávají tři údaje:
**číslo objednávky**, **datum doručení** a **cena**. Přepíšeš je podle
toho, co dodavatel poslal; co se liší od objednávky, se uloží jako
odchylka.

Položka pak nese odznak *Souhlasí*, nebo *N odchylek* s výpisem po najetí
myší. Změněný termín a cena jsou vidět přímo v řádku pod původní hodnotou.
Rozdíl v termínu se počítá ve dnech, u ceny i v procentech.

V hlavičce se dá zúžit výběr na vlastní dispo kódy; volba se pamatuje.

## Co je dobré vědět

- **Odbavené položky přežijí nový import.** Páruje se přes doklad + položku,
  takže zítřejší export téhož reportu nepřepíše, co jsi dnes vyřídil.
- **Report 500 108 se v každém běhu opakuje.** Řádky se slučují podle
  dokladu, položky a chybové zprávy — drží se poslední běh a počet výskytů.
- **Kódování.** Exporty ze SAPu chodí občas ve windows-1252; pozná se to
  a přečte správně.
- **Sloupce reportu ME2A.** Čtou se pozičně v pořadí: objednávka, položka,
  materiál, krátký text, dodavatel, název dodavatele, množství, MJ, datum
  dodání, cena, měna, dispo, potvrzeno. Poslední dva jsou nepovinné.
  Pokud tvůj export vypadá jinak, stačí upravit `parseObjednavky`.
- **Zapsané odchylky jsou snímek k okamžiku potvrzení.** Když se objednávka
  v SAPu později změní, řádek porovnává potvrzenou hodnotu s tou novou,
  ale uložený text odchylky zůstává původní.
- **Data jsou jen lokálně.** Tlačítko *Vymazat data* smaže import i stav
  odbavení. Ze SAPu se nemaže nic.

Ostrá verze by místo importu četla a zapisovala do SAP přes OData / RFC.
