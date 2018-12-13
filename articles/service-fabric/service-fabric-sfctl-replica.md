---
title: Azure Service Fabric CLI sfctl serverem repliky | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl repliky.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0000e5d8bfa7da6ebe1b6702649e56262c9d9cab
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53277365"
---
# <a name="sfctl-replica"></a>sfctl replica
Spravujte repliky, které patří do oddílů služby.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| nasazení | Načte podrobnosti repliky, které jsou nasazené na uzlu Service Fabricu. |
| nasazení seznamu | Získá seznam replikami nasazenými na uzlu Service Fabric. |
| zdravotnictví | Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby. |
| informace | Získá informace o repliky oddílu Service Fabric. |
| list | Získá informace o replikách oddílu služby Service Fabric. |
| odebrat | Odstraní repliku služby běží na uzlu. |
| Stav sestavy | Odešle zprávu o stavu v replice Service Fabric. |
| restart | Restartuje službu repliky trvalé služby spuštěné na uzlu. |

## <a name="sfctl-replica-deployed"></a>nasazení repliky sfctl
Načte podrobnosti repliky, které jsou nasazené na uzlu Service Fabricu.

Načte podrobnosti repliky nasazené na uzlu Service Fabricu. Informace zahrnují druh služby, název služby, aktuální operace služby, aktuální operace služby spuštění datum a čas, ID oddílu, ID repliky a instance, ohlášené zatížení a dalších informací.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identifikátor repliky. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-deployed-list"></a>nasazení repliky sfctl-list
Získá seznam replikami nasazenými na uzlu Service Fabric.

Získá seznam obsahující informace o replikami nasazenými na uzlu Service Fabric. Tyto informace zahrnují ID oddílu, ID repliky, stav repliky, název služby, název typu služby a další informace. ID oddílu nebo ServiceManifestName parametry dotazu použijte k vrácení informací o nasazených repliky odpovídající zadané hodnoty pro tyto parametry.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id aplikace [povinné] | Identitu aplikace. Toto je obvykle celé jméno aplikace bez "fabric\:" schéma identifikátoru URI. Od verze 6.0, hierarchické názvy jsou oddělené znakem "\~" znak. Například, pokud je název aplikace "fabric\:/myapp/app1", bude identita aplikace "myapp\~app1" v 6.0 + a "myapp/app1" v předchozích verzích. |
| --[povinný] název uzlu | Název uzlu. |
| – id oddílu | Identita oddílu. |
| – název manifestu služby | Název manifestu služby zapsaní v rámci typu aplikace v clusteru Service Fabric. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-health"></a>sfctl stavu repliky
Získá stav repliky stavové služby Service Fabric nebo instance bezstavové služby.

Získá stav repliky Service Fabric. EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášené pro repliky založené na stav v pořádku.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identifikátor repliky. |
| --události stavu stavu filtru | Umožňuje filtrování vrácených objektů HealthEvent kolekce na základě stavu. Možné hodnoty pro tento parametr obsahovat celočíselnou hodnotu některého z následujících stavů. Vrátí se jenom události, které odpovídají filtru. Všechny události se používají k vyhodnocení agregovaný stav v pořádku. Pokud není zadán, budou vráceny všechny položky. Hodnoty stavu jsou výčet založený na příznak, takže hodnota může být kombinací těchto hodnot, pomocí bitový operátor "OR". Například pokud zadaná hodnota je 6 všechny události s hodnotou elementu HealthState OK (2) a upozorněním (4), jsou vráceny.  <br> – Výchozí – výchozí hodnota. Odpovídá jakékoli stav HealthState. Hodnota je nula.  <br> -Žádný - filtr, který se pravděpodobně neshoduje s žádnou hodnotu stavu HealthState. Použít, aby nevracela žádné výsledky na dané kolekce stavů. Hodnota je 1.  <br> -Ok – umožňuje filtrovat, že odpovídá vstup s hodnotou elementu HealthState Ok. Hodnota je 2.  <br> -Warning - filtr, že hodnota vstupu odpovídá k elementu HealthState upozornění. Hodnota je 4.  <br> -Chyba – filtr, který se shoduje s hodnotou elementu HealthState Chyba vstupu. Hodnota je 8.  <br> -All - filtr, který odpovídá s libovolnou hodnotou elementu HealthState. Hodnota je 65535. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-info"></a>informace o sfctl repliky
Získá informace o repliky oddílu Service Fabric.

Odpověď obsahuje ID, role, stav, stav, název uzlu, dobu provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identifikátor repliky. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-list"></a>seznam replik sfctl
Získá informace o replikách oddílu služby Service Fabric.

Koncový bod GetReplicas vrátí informace o replik zadaný oddíl. Odpověď obsahuje ID, role, stav, stav, název uzlu, dobu provozu a další podrobnosti o repliky.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – id oddílu [povinné] | Identita oddílu. |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-remove"></a>odebrat repliky sfctl
Odstraní repliku služby běží na uzlu.

Toto rozhraní API simuluje selhání Service Fabric repliky odstranit repliku z clusteru Service Fabric. Odstranění repliky se zavře, přechody repliky do role, None a poté odebere všechny informace o stavu repliky z clusteru. Toto rozhraní API testuje cesty odebrání stavu repliky a simuluje cesta trvalý selhání sestavy prostřednictvím klienta pro rozhraní API. Upozornění – zde nejsou žádné bezpečnostní kontroly provést při použití tohoto rozhraní API. Nesprávné použití tohoto rozhraní API může způsobit ztrátu dat pro stavové služby. Kromě toho forceRemove příznak má vliv na všechny repliky hostované v rámci stejného procesu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identifikátor repliky. |
| --force odebrat | Odebrání aplikace Service Fabric nebo služby vynuceně bez nutnosti kontaktovat řádné vypnutí pořadí. Tento parametr lze použít k vynuceně odstranění aplikace nebo služby, pro které delete je bezproblémové vypršení časového limitu z důvodu problémů v kódu služby, které brání zavřít replik. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-report-health"></a>Stav sestavy repliky sfctl
Odešle zprávu o stavu v replice Service Fabric.

Sestavy stavu zadaná replika Service Fabric. Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric gateway repliky, který se předává k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zobrazit, zda byla použita sestava v health store, spustit získat stav repliky a zkontrolujte, že sestava se zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identita oddílu. |
| – id zdroje [povinné] | Název zdroje, který identifikuje klienta/sledovacího zařízení/systémové součásti, které vygenerovalo informací o stavu. |
| – Popis | Popis informací o stavu. <br><br> Představuje libovolný text pro přidání lidské čitelné informace o sestavě. Maximální délka řetězce popis je 4096 znaků. Pokud zadaný řetězec je delší, bude automaticky zkrácen. Při zkráceno, poslední znaky popis obsahovat značky "[zkrátila]" a celkový počet řetězec velikost je 4 096 znaků. Výskyt značky určuje uživatelům této zkrácení došlo k chybě. Všimněte si, že při zkráceno, popis má menší než 4096 znaků z původního řetězce. |
| --okamžité | Příznak, který udává, zda sestavy by měl být odesílány okamžitě. <br><br> Sestava stavu posílá do Service Fabric gateway aplikace, který se předává k úložišti stavů. Pokud okamžité nastavená na hodnotu true, zpráva se odešle okamžitě ze brána protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá brána aplikace HTTP. To je užitečné pro kritické zprávy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále nezdaří, pokud bránu HTTP je uzavřený nebo zprávy nelze navázat spojení s bránou. Okamžité je nastavený na hodnotu false, sestava se odesílá podle nastavení stavu klienta ze brána protokolu HTTP. Proto bude možné dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje stavu klienta k optimalizaci zdraví, vytváření sestav zprávy k úložišti stavů, stejně jako zpracování sestavy stavu. Ve výchozím nastavení nejsou odesílány sestavy okamžitě. |
| – vypršela platnost odebrat v případě | Hodnota, která označuje, zda sestava se odebere z health store, když jeho platnost vyprší. <br><br> Je-li nastavena hodnota true, sestava se odebere z health store po vypršení její platnosti. Pokud je nastaven na hodnotu false, sestava je považováno za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnotu false. Když klienti pravidelně hlásit, nastavují by měl RemoveWhenExpired false (výchozí). Tímto způsobem je osoby podávající hlášení dochází k problémům (například zablokování) a nemůže oznamovat entity je vyhodnocena v chybě, když vyprší platnost sestava stavu. Označí entitu jako patřící do chybového stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestav se používá v úložišti stavů ke zjišťování zastaralých sestavy. Pokud není zadán, je číslo sekvence automaticky generované klientem stavu při přidání sestavy. |
| --druh služby | Druh služby repliky (Bezstavová nebo stavová) pro které se hlásí stav. Možné hodnoty jsou následující\: "Bezstavové", "Stavová".  Výchozí\: Stateful. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – Hodnota ttl | Doba trvání, pro kterou je tato sestava stavu platná. Toto pole používá formát ISO8601 pro zadání dobu trvání. <br><br> Když klienti pravidelně hlásit, odesílají by zprávy s frekvencí vyšší než hodnota time to live. Pokud klienti nenahlásí na přechod, nastavují time to live nekonečno. Když vyprší čas TTL, událost stavu, který obsahuje informace o stavu je odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocovány v chybě, pokud RemoveWhenExpired false. Pokud není zadaný, čas TTL výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-replica-restart"></a>sfctl repliky restartování
Restartuje službu repliky trvalé služby spuštěné na uzlu.

Restartuje službu repliky trvalé služby spuštěné na uzlu. Upozornění – zde nejsou žádné bezpečnostní kontroly provést při použití tohoto rozhraní API. Nesprávné použití tohoto rozhraní API může vést ke ztrátě dostupnosti pro stavové služby.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| – id oddílu [povinné] | Identita oddílu. |
| – id repliky [povinné] | Identifikátor repliky. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |


## <a name="next-steps"></a>Další postup
- [Instalační program](service-fabric-cli.md) příkazového řádku Service Fabric.
- Další informace o použití nástroje příkazového řádku Service Fabric pomocí [ukázkové skripty](/azure/service-fabric/scripts/sfctl-upgrade-application).
