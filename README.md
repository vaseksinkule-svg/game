# Disponent 101 — tréninkové prostředí MRP

Simulace denních úkonů disponenta (MRP controllera) podle logiky SAPu.
Jeden soubor `index.html`, bez závislostí — stačí otevřít v prohlížeči.

## Co se tu trénuje

Deset pracovních dní ve výrobním závodě. Disponentská skupina 101, sedm
nakupovaných materiálů. Každý den je potřeba udržet krytí výrobních potřeb
a přitom nevázat zbytečně kapitál v zásobách.

| Transakce | Obrazovka |
|---|---|
| `MD06` | Seznam MRP — materiály se zprávami výjimky |
| `MD04` | Stav zásob a potřeb — časová osa prvků plánování |
| `ME57` | Přiřazení a zpracování nákupních požadavků |
| `ME21N` | Založení objednávky (standardní nebo expresní dodání) |
| `ME22N` | Změna termínu dodání, storno |
| `ME2M` | Otevřené objednávky, urgence |
| `MB52` | Zásoby na skladě |
| `SBWP` | Schránka příjmu zpráv |
| `ZDEN` | Uzávěrka dne a vyhodnocení období |

Kódy se zadávají do pole *Transakce* stejně jako v systému, včetně předpony
`/n`. `F3` je zpět, `/` skočí do příkazového pole.

## Jak funguje plánování

Plánovací běh po každém zásahu:

1. **Kontrola přeplánování** — vznikne-li podkrytí a existuje pozdější pevná
   dodávka, systém nezakládá novou zakázku, ale navrhne přesunout stávající
   vpřed (zpráva **10**). V MD04 proto zůstane vidět propad disponibilního
   množství — přesun musí provést disponent.
2. **Nové plánované zakázky** — periodická velikost dávky, čistá potřeba se
   seskupuje po týdnech. Termín zahájení = termín dodání minus plánovaná
   dodací lhůta; když už uplynul, hlásí se zpráva **06**, do tří dnů **30**.
3. **Nadbytek** — dodávka, kterou v horizontu není potřeba, dostane zprávu
   **20**; dodávka o víc než 12 dní předběhnutá zprávu **15**.

Hodnocený horizont je 40 dní, plánuje se do 46 dní, aby na okraji nevznikalo
umělé podkrytí.

## Vyhodnocení

Skóre se skládá ze tří částí: servisní úroveň (55 bodů, hodnota krytých
potřeb), hospodárnost (25 bodů, expresní příplatky, storna a náklady prostojů)
a vázaný kapitál v průměrné zásobě (20 bodů). Expresní dodání zkracuje lhůtu
zhruba na polovinu za příplatek 22 %, posun objednávky vpřed stojí 9 % a
dodavatel ho nemusí potvrdit, urgence stojí 350 Kč a zvyšuje spolehlivost
dodávky asi o 12 procentních bodů.
