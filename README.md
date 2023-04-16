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

### Zpracovávání argumentů

Pro zpracování argumentů se používá knihovna getopt. Program přijímá krátké i dlouhé možnosti přepínačů:

`./ipk-sniffer [-i interface | --interface interface] {-p port [--tcp|-t] [--udp|-u]} [--arp] [--icmp4] [--icmp6] [--igmp] [--mld] {-n num}s`


Pokud není při zadání přepínače -i uveden žádný argument, program vypíše na standardní výstup síťový výstup, který je k dispozici. Protože knihovna getopt neumožňuje zpracování tohoto typu, v projektu se tento problém řeší jinak


# Funkce třídy sniffer
Funkce **init()** je určena k přípravě spuštění zachytávání paketů v síti. Pro spuštění této funkce jsou potřebné konfigurační údaje, jako je zařízení, na kterém se bude zachytávat, filtr pro zpracování paketů, čas ukončení programu při chybějící odpovědi od síťového zařízení, režim promiskuitního použití a počet paketů, které se mají zachytit a vypsat na obrazovku. Následně se pomocí funkce **pcap_lookupnet()** zjistí IPv4 adresa a maska zařízení a pomocí funkce **pcap_open_live()** se získá popisovač zachytávání paketů. Textový filtr se přeloží na filtrující program pomocí funkce **pcap_compile()** a nastaví se pomocí **pcap_setfilter()**. Pomocí funkce **pcap_datalink()** se zjistí typ datalinku zařízení. Při jakékoliv chybě se vypíše chybové hlášení a program skončí s chybovou návratovou hodnotou 1.

Funkce **print_interfaces()** slouží k výpisu všech dostupných síťových zařízení. Při chybě se vypíše chybové hlášení a program skončí s návratovou hodnotou 1.

Funkce **capture_packets()** je určena pro zachytávání paketů pomocí funkce **pcap_loop()** a jejich posílání do funkce **handle_packet()**, která pakety zpracovává.

Funkce **handle_packet()** vytvoří a inicializuje nový objekt s daty přijatými funkcí **pcap_loop()**. Následně se vytvořený paket zpracuje funkcí **parse()**. Pokud je zpracování úspěšné, paket se vypíše pomocí funkce **print_packet()**.

Funkce **exit_sniffer()** slouží k ukončení běhu snifferu, pokud stále běží funkce **pcap_loop()**. Tuto funkci lze zastavit pomocí funkce **pcap_breakloop()** a uvolní se paměť popisovače.


# Funkce třídy pakcet

### Konstruktor
Konstruktor alokuje paměť pro paket a ukládá do něj časovou stopu a délku přijatého paketu. Též ukládá datovou část paketu, která bude později použita k získání metadat o přijatém paketu.


Funkce **print_packet()** má za úkol vytisknout celý paket a používá specifické funkce pro tisk jednotlivých částí paketu. Tisk paketu je závislý na jeho správném zpracování.

Funkce **print_data()** má za úkol vypsat formátovaná data obsažená v paketu. Data jsou zapsána po bytech a jsou vypsána jako hexadecimální hodnoty. Pokud je to možné, jsou také vypsána v ASCII formátu. Pokud není znak v ASCII formátu dostupný, je nahrazen tečkou.

Funkce **print_header()** má za úkol vypsat hlavičku paketu, která obsahuje informace o čase přijetí paketu, jeho zdroji a cílové adrese, kam byl paket odeslán, a délce paketu v bytech.

Funkce **parse()** slouží k obsluze zpracování paketu. V této funkci se identifikuje typ paketu z ethernetové hlavičky (IPv4, IPv6 nebo ARP). Podle tohoto typu se rozhodne, jaká funkce bude volána na zpracování další vrstvy paketu.

Funkce **handle_ip_packet()** slouží k zpracování paketů s typem IPv4. Z hlavičky tohoto typu se získává informace o použitém protokolu (TCP, UDP, ICMP, IGMP). Poté, co jsou metadata zpracována, jsou zapsána do hlavičky.

Funkce handle **ip6_packet()** má za úkol zpracovávat pakety s typem IPv6. Pomocí IPv6 hlavičky se identifikuje, o jaký protokol se jedná - zda TCP, UDP nebo ICMP. Po úspěšném zpracování metadat se zaznamenávají do hlavičky.

Funkce **handle_arp_packet()** má za úkol zpracovávat pakety s typem ARP. Z této hlavičky se získává informace o zdrojové a cílové adrese a ty jsou poté uloženy do hlavičky.

Funkce **get_packet_time()** má za úkol upravit formát časového razítka přijatí paketu podle standardu RFC3339.

### Destruktor
Tato funkce má za úkol uvolnit alokované zdroje při vytváření paketu.

# Překlad a spuštění 

V adresáři se nachází makefile, pomocí příkazu make se vytvoří spustitelný soubor v kořenovém adresáři. Příkaz make clean vymaže dočasné soubory a vytvořené spustitelné soubory.
 
#### Přiklad a spuštění
`$ ./ipk-sniffer -i eth0 --tcp` 


# Výstup programu
Program zpracovává zachycené pakety a vypisuje je na standardní výstup. Výpis obsahuje hlavičku, ve které jsou uložena metadata paketu. Zbytek výpisu tvoří celá datová část paketu v formátovaném tvaru.

Chybové stavy jsou identifikovány a řešeny pomocí příslušných chybových hlášek. Pokud se vyskytne jakákoli chyba, bude zobrazeno chybové hlášení na standardní chybový výstup "stderr" a program se ukončí s návratovou hodnotou 1. Navíc je v programu zachycen signál "Keyboard interrupt" a po jeho přijetí bude program korektně ukončen voláním funkce "exit sniffer()".








