---
title: Azure Service Fabric CLI sfctl uzly | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl uzlu.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: e68a258c8e323b62f85219648c011ce1e661ee0d
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494534"
---
# <a name="sfctl-node"></a>sfctl node
Spravujte uzly, které tvoří cluster.

## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| zakázat | Deaktivujte uzel clusteru Service Fabric se zadaným deaktivaci záměr. |
| povolit | Aktivujte uzel clusteru Service Fabric, která je právě deaktivována. |
| stav | Získá stav uzlu Service Fabricu. |
| informace | Získá informace o konkrétním uzlu v clusteru Service Fabric. |
| Seznam | Získá seznam uzlů v clusteru Service Fabric. |
| načítání | Získá informace o načtení uzlu Service Fabric. |
| stavu Remove | Service Fabric oznámí, že trvalého stavu na uzlu trvale odstraněn nebo ztráty. |
| Stav sestavy | Odešle zprávu o stavu na uzlu Service Fabric. |
| restart | Restartování uzlu clusteru Service Fabric. |
| Přechod | Spuštění nebo zastavení uzlu clusteru. |
| Přechod stavu | Získá průběh operace začít používat StartNodeTransition. |

## <a name="sfctl-node-disable"></a>zakázat uzel sfctl
Deaktivujte uzel clusteru Service Fabric se zadaným deaktivaci záměr.

Deaktivujte uzel clusteru Service Fabric se zadaným deaktivaci záměr. Po deaktivaci právě probíhá, záměrem deaktivovat lze zvýšit, ale ne snížit (například uzel, který je deaktivováno se záměrem pozastavit může být dále deaktivované Restart, ale ne naopak. Uzly mohou znovu aktivovat pomocí aktivovat uzel operace kdykoli po se deaktivuje. Pokud deaktivaci není kompletní, tato akce zruší deaktivaci. Uzel, který ocitne mimo provoz a uvede do provozu při deaktivaci se stále potřeba znovu aktivovat před služby budou umístěny na tomto uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --deaktivaci záměr | Popisuje záměr nebo důvod deaktivace uzlu. Možné hodnoty jsou následující. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-enable"></a>sfctl uzlu povolit
Aktivujte uzel clusteru Service Fabric, která je právě deaktivována.

Aktivuje uzel clusteru Service Fabric, která je právě deaktivována. Po aktivaci uzlu se znovu stane přijatelné cíl pro uvedení nové repliky a případné deaktivované repliky zbývající na uzlu se znovu aktivovat.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-health"></a>stav uzlu sfctl
Získá stav uzlu Service Fabricu.

Získá stav uzlu Service Fabricu. EventsHealthStateFilter použijte k filtrování kolekce událostí stavu hlášené pro uzel podle stavu. Pokud uzel, který určíte podle názvu v health store neexistuje, vrátí chybu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
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

## <a name="sfctl-node-info"></a>informace o uzlu sfctl
Získá informace o konkrétním uzlu v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu provozu a další podrobnosti o uzlu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-list"></a>seznam uzlů sfctl
Získá seznam uzlů v clusteru Service Fabric.

Odpověď obsahuje název, stav, ID, stav, dobu provozu a další podrobnosti o uzlech.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --Filtr stavu uzlu | Umožňuje filtrování podle NodeStatus uzly. Vrátí se pouze uzly, které se odpovídající hodnota zadaného filtru. Hodnota filtru může být jedna z následujících akcí.  Výchozí\: výchozí. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-load"></a>sfctl uzlu zatížení
Získá informace o načtení uzlu Service Fabric.

Načte informace o načtení uzlu Service Fabric pro všechny metriky, které mají zatížení nebo definované kapacity.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-remove-state"></a>sfctl remove stav uzlu
Service Fabric oznámí, že trvalého stavu na uzlu trvale odstraněn nebo ztráty.

Z toho vyplývá, že není možné obnovit trvalý stav tohoto uzlu. Obvykle k tomu dojde, pokud pevném disku bylo vymazáno čistý, nebo pokud dojde k chybě na pevný disk. Uzel musí být mimo provoz pro tuto operaci k dosažení úspěchu. Tato operace umožňuje vědět, že repliky v daném uzlu už neexistuje a v daných prostředcích služby by se měla zastavit čeká na tyto repliky k téhle akci vrátit Service Fabric. Tato rutina nespustí, pokud není odebraná stavu na uzlu a uzel můžete přijít zpět beze změny stavu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-report-health"></a>Stav sestavy uzlu sfctl
Odešle zprávu o stavu na uzlu Service Fabric.

Sestavy stav určeného uzlu Service Fabric. Sestava může obsahovat informace o zdroji sestava stavu a na kterém je uvedena vlastnost. Sestava je odeslána do Service Fabric uzel brány, která je směruje k úložišti stavů. Sestava může být přijal brány, ale odmítnuté úložiště stavu po další ověření. Úložiště stavu může třeba odmítnout sestavy z důvodu neplatného parametru, jako jsou zastaralé pořadové číslo. Pokud chcete zobrazit, zda byla použita sestava v health store, zkontrolujte, že sestava se zobrazí v části HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| – Stav – vlastnost [povinné] | Vlastnost s informacemi o stavu. <br><br> Entita může mít sestav o stavu pro různé vlastnosti. Vlastnost je řetězec a oprava výčet umožňují flexibilní reportérka ke kategorizaci podmínku stavu, který spouští sestavu. Například reportérka s ID zdroje "LocalWatchdog" můžete monitorovat stav disku v uzlu, tak ho může hlásit vlastnost "AvailableDisk" v tomto uzlu. Stejné reportérka můžete monitorovat připojení uzlu, takže ho může hlásit vlastnost "Připojení" ve stejném uzlu. V health store tyto sestavy jsou považovány za události samostatné stavu pro zadaný uzel. Spolu s ID zdroje vlastnost jednoznačně identifikuje informací o stavu. |
| – Stav [povinné] | Možné hodnoty zahrnují\: "Neplatný", "Ok", "Upozorňující", "Chyba", "Neznámý". |
| --[povinný] název uzlu | Název uzlu do sestavy. |
| – id zdroje [povinné] | Název zdroje, které identifikuje součásti klienta/sledovacího zařízení/systému, který se vygenerovat informace o stavu. |
| – Popis | Popis informací o stavu. <br><br> Představuje libovolný text pro přidání lidské čitelné informace o sestavě. Maximální délka řetězce popis je 4096 znaků. Pokud zadaný řetězec je delší, bude automaticky zkrácen. Při zkráceno, poslední znaky popis obsahovat značky "[zkrátila]" a celkový počet řetězec velikost je 4 096 znaků. Výskyt značky určuje uživatelům této zkrácení došlo k chybě. Všimněte si, že při zkráceno, popis má menší než 4096 znaků z původního řetězce. |
| --okamžité | Příznak označující, zda mají být okamžitě odesílány sestavy. <br><br> Sestava stavu posílá do Service Fabric gateway aplikace, který se předává k úložišti stavů. Pokud okamžité nastavená na hodnotu true, zpráva se odešle okamžitě ze brána protokolu HTTP k úložišti stavů, bez ohledu na nastavení klienta fabric, které používá brána aplikace HTTP. To je užitečné pro kritické zprávy, které by měly být odeslány co nejdříve. V závislosti na načasování a jiných podmínek odesílat sestavy může stále nezdaří, pokud bránu HTTP je uzavřený nebo zprávy nelze navázat spojení s bránou. Okamžité je nastavený na hodnotu false, sestava se odesílá podle nastavení stavu klienta ze brána protokolu HTTP. Proto bude možné dávce závislosti na konfiguraci HealthReportSendInterval. Toto je doporučené nastavení, protože umožňuje stavu klienta k optimalizaci zdraví, vytváření sestav zprávy k úložišti stavů, stejně jako zpracování sestavy stavu. Ve výchozím nastavení nejsou odesílány sestavy okamžitě. |
| – vypršela platnost odebrat v případě | Hodnota, která označuje, zda sestava se odebere z health store, když jeho platnost vyprší. <br><br> Je-li nastavena hodnota true, sestava se odebere z health store po vypršení její platnosti. Pokud je nastaven na hodnotu false, sestava je považováno za chybu, pokud vypršela platnost. Hodnota této vlastnosti je ve výchozím nastavení hodnotu false. Když klienti pravidelně hlásit, nastavují by měl RemoveWhenExpired false (výchozí). Tímto způsobem je osoby podávající hlášení dochází k problémům (například zablokování) a nemůže oznamovat entity je vyhodnocena v chybě, když vyprší platnost sestava stavu. Označí entitu jako patřící do chybového stavu. |
| --pořadové číslo | Pořadové číslo pro tuto sestavu stavu jako číselný řetězec. <br><br> Pořadové číslo sestav se používá v úložišti stavů ke zjišťování zastaralých sestavy. Pokud není zadán, je číslo sekvence automaticky generované klientem stavu při přidání sestavy. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| – Hodnota ttl | Doba trvání, pro kterou je tato sestava stavu platná. Toto pole je použít formát ISO8601 pro zadání dobu trvání. <br><br> Když klienti pravidelně hlásit, odesílají by zprávy s frekvencí vyšší než hodnota time to live. Pokud klienti nenahlásí na přechod, nastavují time to live nekonečno. Když vyprší čas TTL, událost stavu, který obsahuje informace o stavu je odebrán z health store, pokud je RemoveWhenExpired hodnotu true, nebo vyhodnocovány v chybě, pokud RemoveWhenExpired false. Pokud není zadaný, čas TTL výchozí hodnoty na nekonečnou hodnotu. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-restart"></a>sfctl restartování uzlu
Restartování uzlu clusteru Service Fabric.

Restartování uzlu clusteru Service Fabric, která je již spuštěna.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| --Vytvořit výpis prostředků infrastruktury | Zadejte hodnotu PRAVDA, chcete-li vytvořit výpis procesu uzel fabric. Toto je velká a malá písmena.  Výchozí\: hodnotu False. |
| – id instance uzlu | ID instance cílový uzel. Pokud je zadané instance ID uzel se restartuje jenom v případě, že se shoduje s aktuální instancí uzlu. Výchozí hodnotu "0" odpovídají žádné ID instance. Instance ID lze zjistit pomocí dotazu get uzlu.  Výchozí\: 0. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-transition"></a>sfctl pro přechod uzlů
Spuštění nebo zastavení uzlu clusteru.

Spuštění nebo zastavení uzlu clusteru.  Uzel clusteru je proces, ne samotné instanci operačního systému.  Pro spuštění uzlu, předejte pro parametr NodeTransitionType v "Start". Zastavit uzel, předejte "Stop" pro parametr NodeTransitionType. Toto rozhraní API začíná operace – rozhraní API vrátí že uzlu nemusí dokončení ještě přechod. Volání GetNodeTransitionProgress s stejným OperationId zobrazíte průběh operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| ---instance-id uzlu [povinné] | ID instance uzlu cílový uzel. To se dá určit pomocí GetNodeInfo rozhraní API. |
| --[povinný] název uzlu | Název uzlu. |
| --uzlu přechodu – typ [povinné] | Určuje typ přechodu k provedení.  NodeTransitionType.Start Spustí zastavené uzlu. NodeTransitionType.Stop přestane uzel, který je v provozu. |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
| --stop-doba trvání v sekund [povinné] | Doba trvání v sekundách, aby uzel zastavena.  Minimální hodnota 600, maximální délka je 14400.  Po tuto dobu vypršení platnosti, uzel se automaticky vytvoří zpět. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-node-transition-status"></a>sfctl uzlu přechod stavu
Získá průběh operace začít používat StartNodeTransition.

Získá průběh operace začít StartNodeTransition pomocí zadané ID operace.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --[povinný] název uzlu | Název uzlu. |
| – id operace [povinné] | Identifikátor GUID, který identifikuje volání tohoto rozhraní API.  To je předán do odpovídajícího GetProgress rozhraní API. |
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