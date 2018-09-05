---
title: Azure Service Fabric CLI - sfctl chaos | Dokumentace Microsoftu
description: Popisuje příkazy Service Fabric CLI sfctl chaos.
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
ms.openlocfilehash: 1e77dfdec902b64f2524c790bda6742eaa1105fc
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669170"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Spuštění, zastavení a vytváření sestav o tento Chaos se dá testovat službu.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Popis|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Získání a nastavení plánu chaos. |
## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| stránka events | Získá další segment Chaos události na základě token pro pokračování nebo časový rozsah. |
| získat | Získáte stav Chaos. |
| start | Chaos se spustí v clusteru. |
| Stop | Chaos zastaví, pokud běží v clusteru a vložit Chaos plán v zastaveném stavu. |

## <a name="sfctl-chaos-events"></a>sfctl chaos události
Získá další segment Chaos události na základě token pro pokračování nebo časový rozsah.

Pokud chcete získat další segment události chaosu, můžete zadat token ContinuationToken. Získat počáteční nový segment Chaos událostí, můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc. Token ContinuationToken a časový rozsah nelze zadat ve stejném volání. Po více než 100 události Chaos se tento Chaos se dá události jsou vráceny ve více segmentech kde segment, který obsahuje více než 100 události Chaos a zobrazíte další segment provedete volání tohoto rozhraní API se token pro pokračování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --token pro pokračování | Parametr tokenu pokračování slouží k získání další sadu výsledků. Token pro pokračování se neprázdná hodnota je zahrnutý v odpovědi rozhraní API, když výsledky ze systému se nevejdou do odpověď o jedné. Když je tato hodnota předána na další volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak pokračovací token neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódování URL. |
| --koncový. čas utc | Windows soubor času představující čas ukončení časový rozsah, pro kterou má vygenerovat sestava Chaos. Poraďte [DateTime.ToFileTimeUtc metoda](https://docs.microsoft.com/dotnet/api/system.datetime.tofiletimeutc?redirectedfrom=MSDN&view=netframework-4.7.2#System_DateTime_ToFileTimeUtc) podrobnosti. |
| – maximální počet výsledků | Maximální počet výsledků, které má být vrácena jako součást stránkové dotazy. Tento parametr definuje horní mez počtu výsledky. Výsledky se vrátí, může být nižší než zadané maximální počet výsledků, pokud se nevejdou do zprávy podle omezení velikosti maximální počet zpráv definované v konfiguraci. Pokud tento parametr je nula, nebo není zadán, obsahuje stránkovaného dotazu tolik výsledky nejdříve, který se vejde v návratové zprávě. |
| --spuštění. čas utc | Windows soubor času představující čas zahájení časový rozsah, pro kterou má vygenerovat sestava Chaos. Poraďte [DateTime.ToFileTimeUtc metoda](https://docs.microsoft.com/dotnet/api/system.datetime.tofiletimeutc?redirectedfrom=MSDN&view=netframework-4.7.2#System_DateTime_ToFileTimeUtc) podrobnosti. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Získáte stav Chaos.

Získáte Stav označující, zda Chaos běží, Chaos parametrů použitých pro spouštění Chaos a stav plánu Chaos Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Chaos se spustí v clusteru.

Pokud chaosu už neběží v clusteru, začíná Chaos předaná v parametrech Chaos. Pokud Chaos je již spuštěn, když se toto volání je, volání selže s kódem chyby FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-type-health-policy-map | JSON kódovaný seznam s maximální procento aplikací není v pořádku pro specifických typů aplikací. Každá položka určuje jako klíč název typu aplikace a jako hodnotu celého čísla, která představuje procento MaxPercentUnhealthyApplications používá k vyhodnocení aplikací určený typ aplikace. <br><br> Definuje mapu s maximální procento aplikací není v pořádku pro specifických typů aplikací. Každá položka určuje jako klíč název typu aplikace a jako hodnotu celého čísla, která představuje procento MaxPercentUnhealthyApplications používá k vyhodnocení aplikací určený typ aplikace. Mapa aplikace typ stavu zásady je možné během vyhodnocení stavu clusteru popisující typy speciální aplikací. Typy aplikací, které jsou zahrnuté v mapě vyhodnocují procento zadané v objektu map a ne s globální MaxPercentUnhealthyApplications definované v zásadě stavu clusteru. Aplikace zadán v mapování typů aplikací nejsou porovnávány s globální fond aplikací. Například pokud některé aplikace typ jsou důležité, Správce clusteru můžete přidat položku do mapy pro tento typ aplikace a přiřaďte ho hodnotu 0 % (tedy ne tolerovat možnost, všechny chyby). Všechny ostatní aplikace mohou být vyhodnoceny s MaxPercentUnhealthyApplications nastavte na 20 % tolerovat několika selháními tisíců instancí aplikace. Mapa aplikace typ stavu zásad se používá jenom v případě, že manifest clusteru umožňuje pomocí položky konfigurace pro HealthManager/EnableApplicationTypeHealthEvaluation vyhodnocování stavu typu aplikace. |
| --chaos filtr cíle | JSON slovník zakódovány dva klíče typu řetězec. Dva klíče jsou seznamy NodeTypeInclusionList a ApplicationInclusionList. Hodnoty pro oba tyto klíče jsou seznamy řetězců. chaos_target_filter definuje všechny filtry pro cílové Chaos chyby, například chybující pouze určité typy uzlů nebo přerušením jen určité aplikace. <br><br> Pokud se nepoužívá chaos_target_filter, Chaos chyb všechny entity v clusteru. Pokud použijete chaos_target_filter Chaos chyb pouze entity, které splňují chaos_target_filter specifikace. Seznamy NodeTypeInclusionList a ApplicationInclusionList povolit pouze sjednocení sémantiku. Není možné určit průsečíkem seznamy NodeTypeInclusionList a ApplicationInclusionList. Například není možné určit "selhání tuto aplikaci, pouze pokud je na daný typ uzlu." Jakmile entity je součástí seznamy NodeTypeInclusionList a ApplicationInclusionList, nelze entity vyloučit pomocí ChaosTargetFilter. I v případě, že se nezobrazují applicationX v ApplicationInclusionList v iteraci některé Chaos applicationX může být došlo k chybě vzhledem k tomu, že se stane se v uzlu nodeTypeY, který je součástí seznamy NodeTypeInclusionList. Pokud seznamy NodeTypeInclusionList a ApplicationInclusionList jsou prázdné, je vyvolána ArgumentException. Všechny typy chyb (restartovat uzel, restartujte balíček kódu, repliku odstranit, restartovat repliky, přesunutí primární a sekundární) jsou povolené pro uzly z těchto typů uzlů. Pokud typ uzlu (Řekněme, že NodeTypeX) se nezobrazují v seznamy NodeTypeInclusionList, pak pro uzly NodeTypeX se nikdy povolí úrovně chyby uzlu (například NodeRestart), ale balíček a repliky chyb kódu je možné povolit pro NodeTypeX pořád, pokud aplikace v Jsou umístěny v uzlu NodeTypeX ApplicationInclusionList se stane. Maximálně 100 názvy typů uzlu mohou být součástí tohoto seznamu, tento počet zvýšit, je nutné konfiguraci MaxNumberOfNodeTypesInChaosEntityFilter upgradovat konfiguraci. Všechny repliky patří do těchto aplikací služeb jsou vydávání kompaktních repliky chyb (restartování repliky, odebrat repliky, přesunutí primárního a sekundárního přesunutí) Chaos. Pouze v případě, že balíček kódu je hostitelem repliky jenom tyto aplikace se může restartovat Chaos balíček kódu. Pokud aplikace v tomto seznamu nezobrazí, se můžete stále být došlo k chybě v iteraci některé Chaos Pokud ukončení aplikace na uzlu typu uzlu, který je součástí seznamy NodeTypeInclusionList. Nicméně pokud applicationX se váže na nodeTypeY prostřednictvím omezení umístění a applicationX chybí z ApplicationInclusionList a nodeTypeY chybí z seznamy NodeTypeInclusionList, pak applicationX nikdy selže. Maximálně 1000 názvy aplikací mohou být součástí tohoto seznamu, tento počet zvýšit, je nutné konfiguraci MaxNumberOfApplicationsInChaosEntityFilter upgradovat konfiguraci. |
| – místní | JSON kódovaný mapy (string, string) zadejte páry klíč hodnota. Na mapě slouží k zaznamenání informací o spuštění Chaos. Nemůže existovat více než 100 takových dvojic a každý řetězec (klíče nebo hodnoty) může obsahovat nejvýše 4095 znaků. Toto mapování se nastavil starter tento Chaos se dá spustit volitelně ukládat kontextu o konkrétního spuštění. |
| --zakázání přesunutí repliky chyb | Zakáže přesunutí primární a sekundární chyby přesunout. |
| -max clusteru stabilizaci | Maximální dobu čekání pro všechny entity, které jsou stabilní a v dobrém stavu clusteru.  Výchozí\: 60. <br><br> Chaos spustí v iterací a na začátku každé iterace ověřuje stav clusteru entit. Během ověřování Pokud entita clusteru není stabilní a v pořádku v rámci MaxClusterStabilizationTimeoutInSeconds, Chaos vygeneruje událost ověření se nezdařilo. |
| – maximální počet souběžných – chyby | Maximální počet souběžných chyby vyvolané na iteraci. Chaos spustí v iteracích a dvě po sobě následujících iterací jsou odděleny fázi ověřování. Čím vyšší souběžnosti, mnohem vyššími vkládání chyb--nevyvolá složitější řadu stavy k odhalení chyb. Doporučujeme začínat hodnotu 2 nebo 3 a při přesouvání postupujte opatrně.  Výchozí\: 1. |
| --max-percent-unhealthy-apps | Při vyhodnocování stavu clusteru během chaosu, maximální povolené procento žádostí, není v pořádku, před ohlášení chyby. <br><br> Maximální povolené procento žádostí, není v pořádku, před ohlášení chyby. Například pokud chcete povolit 10 % aplikací v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento aplikací, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje alespoň jedna aplikace není v pořádku, stav se vyhodnotí jako upozornění. To se vypočítá jako podíl počtu poškozené aplikace přes celkový počet instancí aplikace v clusteru, s výjimkou aplikací typy aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet aplikací. Procentuální výchozí hodnota je nula. |
| --max-percent-unhealthy-nodes | Při vyhodnocování stavu clusteru během chaosu, maximální povolené procento poškozené uzly před ohlášení chyby. <br><br> Maximální povolené procento poškozené uzly před ohlášení chyby. Například pokud chcete povolit 10 % uzly v dobrém stavu, tato hodnota by 10. Procento představuje přípustný maximální procento uzlů, které může být není v pořádku, než clusteru se považuje za chybu. Pokud procento respektován, ale existuje nejméně jeden uzel není v pořádku, stav se vyhodnotí jako upozornění. Procentuální se vypočítá jako podíl počtu uzlů v pořádku za celkový počet uzlů v clusteru. Výpočet zaokrouhlí tento údaj tolerovat selhání jednoho na malý počet uzlů. Procentuální výchozí hodnota je nula. Ve velkých clusterech některé uzly bude vždy dolů nebo navýšení kapacity pro opravy, tak tolerovat, který by měl být nakonfigurovaný toto procento. |
| – čas spuštění | Celkový čas (v sekundách) pro který Chaos spustí před zastavením automaticky. Maximální povolená hodnota je 4 294 967 295 (System.UInt32.MaxValue).  Výchozí\: 4294967295. |
| --timeout -t | Server časový limit v sekundách.  Výchozí\: 60. |
| --Čekací doba mezi chyb | Čekací doba (v sekundách) mezi po sobě jdoucích chyb v rámci jedné iterace.  Výchozí\: 20. <br><br> Tím je hodnota větší, čím nižší překrývání mezi chybami a jednodušší posloupnost stavu přechody, která procházejí clusteru. Doporučujeme začít s hodnotu mezi 1 a 5 a cvičení opatrní při přesouvání. |
| --Čekací doba mezi iteracemi | Čas – oddělení (v sekundách) mezi dvě po sobě následujících iterací Chaos. Vyšší hodnota, tím nižší sazby vkládání selhání.  Výchozí\: 30. |
| --upozornění jako chyby | Nastaví zásadu health zpracovávat upozornění jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --debug | Zvýšit úroveň podrobností protokolování lze zobrazit, že všechny protokoly ladění. |
| – Nápověda -h | Zobrazte tuto zprávu nápovědy a ukončení. |
| --vstupně - výstupní | Formát výstupu.  Povolené hodnoty\: json, jsonc, tabulky, tsv.  Výchozí\: json. |
| – dotaz | Řetězec dotazu JMESPath. Zobrazit http\://jmespath.org/ pro další informace a příklady. |
| -verbose | Zvýšit úroveň podrobností protokolování. Pomocí parametru--ladění pro protokoly ladění úplné. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Chaos zastaví, pokud běží v clusteru a vložit Chaos plán v zastaveném stavu.

Chaos zastaví spuštění nové chyby. Ke spuštění, dokud nedojde k jejich dokončení bude pokračovat vydávaných za pochodu chyb. Aktuální plán Chaos přejde do stavu ukončeno. Po ukončení plánu bude zůstat v zastaveném stavu a nejsou používány k nové spuštění plánu Chaos Chaos. Aby bylo možné pokračovat, plánování, musí být nastaven nový plán Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
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