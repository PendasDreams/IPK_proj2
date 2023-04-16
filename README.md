# IPK_proj2 (Packet sniffer)

xnovos14 / Denis Novosád


Cílem projektu je vytvořit program, který umožňuje zachytávat a filtrovat pakety v počítačové síti. Tento program umožňuje zachytávání paketů na různých síťových zařízeních, ale podporuje pouze zařízení typu Ethernet. Je také schopen filtrovat pakety pomocí dostupných filtrů na základě určitého protokolu nebo kombinace protokolů, a to také podle portu, který se používá pro komunikaci. Výsledky programu jsou zobrazeny na standardním výstupu výpočetního zařízení.



# Teorie

Sniffing paketů je technika, která umožňuje získat data, která cestují v síti. Tyto techniky jsou nejčastěji používány pro správu sítě a kontrolu sítového provozu. Pakety obsahují data, která jsou rozdělena do různých vrstev, kde každá vrstva má svou vlastní hlavičku a tělo. Tělo paketu také obsahuje informace z nižší vrstvy, včetně další hlavičky a těla. Postupným zpracováním paketu po jednotlivých vrstvách lze nakonec získat požadovaná data.

# Návrh

Program je napsán v programovacím jazyce C++ a používá se v něm objektově orientované programování. Celý program je rozdělen na tři moduly: main, sniffer a packet.

V modulu main se zpracovávají argumenty příkazové řádky a vytváří se instance třídy sniffer, na kterých jsou poté volány metody této třídy.

Modul sniffer obsahuje implementaci třídy se stejným názvem. Tato třída obsahuje data o konfiguraci zařízení, které bude zachytávat pakety, jako je typ zařízení, druh filtru, počet paketů, které se mají zachytit, a další. Instance této třídy pak provádí své metody, jako je například připojení k vybranému síťovému zařízení, nakonfigurování nastavení pro zachytávání paketů, spuštění zachytávání paketů a výpis všech dostupných síťových zařízení.

Třída packet v modulu packet slouží k zpracování a výpisu zachycených paketů. Instance této třídy obsahuje data o zachyceném paketu, jako je samotný paket, délka dat a čas zachycení paketu. V metodě parse() se získávají metadata paketu, například adresy příjemce, odesílatele, porty atd. Poté se podle určeného druhu paketu volají funkce, které "rozbalí" paket po jednotlivých vrstvách.

# Implemetnace

## Zpracovávání argumentů


