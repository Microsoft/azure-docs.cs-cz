---
title: Azure Service Fabric CLI- sfctl chaos
description: Další informace o sfctl, rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu chaosu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906162"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Začněte, zastavte a oznamte službu testování chaosu.

## <a name="subgroups"></a>Podskupin
|Podskupina|Popis|
| --- | --- |
| [Plán](service-fabric-sfctl-chaos-schedule.md) | Najdi a nastavte plán chaosu. |
## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| stránka events | Získá další segment Chaos události založené na tokenu pokračování nebo časový rozsah. |
| get | Získejte stav Chaos. |
| start | Spustí chaos v clusteru. |
| Zastavit | Zastaví Chaos, pokud je spuštěn v clusteru a dát Chaos Plán v zastaveném stavu. |

## <a name="sfctl-chaos-events"></a>sfctl chaos události
Získá další segment Chaos události založené na tokenu pokračování nebo časový rozsah.

Chcete-li získat další segment Chaos události, můžete zadat ContinuationToken. Chcete-li získat začátek nového segmentu událostí Chaos, můžete určit časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc. Nelze zadat continuationtoken a časový rozsah ve stejném volání. Pokud existuje více než 100 událostí Chaos, události Chaos jsou vráceny ve více segmentech, kde segment neobsahuje více než 100 událostí Chaos a získat další segment, který provedete volání tohoto rozhraní API s tokenem pokračování.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --continuation-token | Parametr tokenu pokračování se používá k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API, pokud se výsledky ze systému nevejdou do jediné odpovědi. Když je tato hodnota předána dalšímu volání rozhraní API, rozhraní API vrátí další sadu výsledků. Pokud neexistují žádné další výsledky, pak token pokračování neobsahuje hodnotu. Hodnota tohoto parametru by neměla být kódována adresou URL. |
| --konec-čas-utc | Čas souboru systému Windows představující čas ukončení časového rozsahu, pro který má být generována sestava Chaos. Podrobnosti naleznete [v metodě DateTime.ToFileTimeUtc.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --max-výsledky | Maximální počet výsledků, které mají být vráceny jako součást stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle omezení maximální velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, stránkovaný dotaz obsahuje co nejvíce výsledků, které se vejdou do vrácené zprávy. |
| --start-time-utc | Čas souboru systému Windows představující čas zahájení časového rozsahu, pro který má být generována sestava Chaos. Podrobnosti naleznete [v metodě DateTime.ToFileTimeUtc.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-get"></a>sfctl chaos dostat
Získejte stav Chaos.

Získejte stav Chaosu označující, zda chaos běží, parametry Chaosu používané pro spuštění Chaosu a stav plánu chaosu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Spustí chaos v clusteru.

Pokud Chaos není již spuštěnv clusteru, spustí Chaos s předané v Chaos parametry. Pokud Chaos je již spuštěna při tomto volání, volání se nezdaří s kódem chyby FABRIC_E_CHAOS_ALREADY_RUNNING. Další podrobnosti naleznete v článku [Indukovat řízený chaos v clusterech Service Fabric.](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos)

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --app-type-health-policy-map --app-type-health-policy-map --app-type-health-policy-map --app | JSON kódovaný seznam s maximální procento není v pořádku aplikace pro konkrétní typy aplikací. Každá položka určuje jako klíč název typu aplikace a jako hodnotu celé číslo, které představuje procento MaxPercentUnhealthyApplications použité k vyhodnocení aplikací zadaného typu aplikace. <br><br> Definuje mapu s maximální procento nefunkční aplikace pro konkrétní typy aplikací. Každá položka určuje jako klíč název typu aplikace a jako hodnotu celé číslo, které představuje procento MaxPercentUnhealthyApplications použité k vyhodnocení aplikací zadaného typu aplikace. Mapování zásad stavu typu aplikace lze použít při hodnocení stavu clusteru k popisu speciálních typů aplikací. Typy aplikací zahrnuté v mapě jsou vyhodnoceny podle procenta zadaného v mapě a nikoli podle globálních aplikací MaxPercentUnhealthyApplications definovaných v zásadách stavu clusteru. Aplikace typů aplikací zadaných v mapě se nezapočítávají do globálního fondu aplikací. Pokud jsou například některé aplikace typu kritické, může správce clusteru přidat položku do mapy pro daný typ aplikace a přiřadit jí hodnotu 0 % (to znamená netolerovat žádné chyby). Všechny ostatní aplikace mohou být vyhodnoceny s MaxPercentUnhealthyApplications nastavena na 20 % tolerovat některé chyby z tisíce instancí aplikace. Mapa zásad stavu typu aplikace se používá pouze v případě, že manifest clusteru umožňuje vyhodnocení stavu typu aplikace pomocí položky konfigurace pro HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filtr | JSON kódovaný slovník se dvěma klávesami typu řetězce. Dva klíče jsou NodeTypeInclusionList a ApplicationInclusionList. Hodnoty pro oba tyto klíče jsou seznam řetězec. chaos_target_filter definuje všechny filtry pro cílené chyby Chaos, například chybování pouze určitých typů uzlů nebo chybování pouze určitých aplikací. <br><br> Pokud chaos_target_filter není použit, Chaos chyby všechny entity clusteru. Pokud chaos_target_filter se používá, Chaos chyby pouze entity, které splňují chaos_target_filter specifikace. NodeTypeInclusionList a ApplicationInclusionList povolit pouze sémantiku unie. Není možné zadat průsečík NodeTypeInclusionList a ApplicationInclusionList. Například není možné zadat "chyba této aplikace pouze v případě, že je na tomto typu uzlu." Jakmile je entita zahrnuta do nodeTypeInclusionList nebo ApplicationInclusionList, nelze tuto entitu vyloučit pomocí chaostargetfilter. I v případě, že applicationX nezobrazí v ApplicationInclusionList, v některých chaos iterace applicationX může být vadný, protože se stane být na uzlu nodeTypeY, který je součástí NodeTypeInclusionList. Pokud jsou prázdné nodeTypeInclusionList a ApplicationInclusionList, je vyvolána výjimka ArgumentException. Pro uzly těchto typů uzlů jsou povoleny všechny typy chyb (restartování uzlu, restartování balíčku kódu, odebrání repliky, restartování repliky, přesunutí primárního a sekundárního přesunutí). Pokud typ uzlu (řekněme NodeTypeX) se nezobrazí v NodeTypeInclusionList, pak chyby na úrovni uzlu (jako NodeRestart) nikdy povolena pro uzly NodeTypeX, ale kód balíček a replika chyby mohou být stále povoleny pro NodeTypeX, pokud aplikace v ApplicationInclusionList se zobrazí v uzlu NodeTypeX. Do tohoto seznamu může být zahrnuto maximálně 100 názvů typů uzlů, aby se toto číslo zvýšilo, je pro konfiguraci MaxNumberOfNodeTypesInChaosEntityFilter vyžadován upgrade konfigurace. Všechny repliky, které patří do služeb těchto aplikací jsou přístupné chyby replik (restartovat repliku, odebrat repliku, přesunout primární a přesunout sekundární) podle Chaos. Chaos může restartovat balíček kódu pouze v případě, že balíček kódu hostuje pouze repliky těchto aplikací. Pokud se aplikace nezobrazí v tomto seznamu, může být stále chybována v některé iteraci Chaos, pokud aplikace skončí na uzlu typu uzlu, který je součástí NodeTypeInclusionList. Však pokud applicationX je vázána na nodeTypeY prostřednictvím umístění omezení a applicationX chybí ApplicationInclusionList a nodeTypeY chybí NodeTypeInclusionList, pak applicationX nikdy nebude vadný. Do tohoto seznamu může být zahrnuto maximálně 1000 názvů aplikací, aby se toto číslo zvýšilo, je pro konfiguraci MaxNumberOfApplicationsInChaosEntityFilter vyžadován upgrade konfigurace. |
| --kontext | JSON kódované mapa (řetězec, řetězec) typ klíče-hodnota párů. Mapu lze použít k zaznamenání informací o spuštění Chaosu. Nemůže existovat více než 100 takových párů a každý řetězec (klíč nebo hodnota) může být maximálně 4095 znaků dlouhý. Tato mapa je nastavena startérchaos spustit volitelně ukládat kontext o konkrétní spuštění. |
| --disable-move-replica-faults --disable-move-replica-faults --disable-move-replica-faults --disable | Zakáže přesunutí primární a přesunout sekundární chyby. |
| --max-stabilizace clusteru | Maximální doba čekání na všechny entity clusteru, aby se stala stabilní a v pořádku.  Výchozí\: 60. <br><br> Chaos se spustí v iterací a na začátku každé iterace ověří stav entit clusteru. Během ověřování, pokud entita clusteru není stabilní a v pořádku v rámci MaxClusterStabilizationTimeoutInSeconds, Chaos generuje událost ověření se nezdařilo. |
| --max-souběžné poruchy | Maximální počet souběžných chyb indukovaných na iteraci. Chaos se spustí v iterací a dvě po sobě jdoucí iterací jsou odděleny fází ověřování. Čím vyšší je souběžnost, tím agresivnější je vstřikování chyb – což vede ke složitější sérii států k odhalení chyb. Doporučujeme začít s hodnotou 2 nebo 3 a postupovat opatrně při pohybu nahoru.  Výchozí\: hodnota 1. |
| --max-percent-unhealthy-apps --max-percent-unhealthy-apps --max-percent-unhealthy-apps --max | Při vyhodnocování stavu clusteru během Chaos, maximální povolené procento nefunkční aplikace před hlášením chyby. <br><br> Maximální povolené procento nefunkčních aplikací před oznámením chyby. Chcete-li například povolit 10 % aplikací není v pořádku, tato hodnota by 10. Procento představuje maximální tolerované procento aplikací, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jedna aplikace není v pořádku, stav je vyhodnocen jako upozornění. Vypočítá se vydělením počtu aplikací není v pořádku na celkový počet instancí aplikace v clusteru, s výjimkou aplikací typů aplikací, které jsou zahrnuty v ApplicationTypeHealthPolicyMap. Výpočtem zaokrouhluje nahoru tolerovat jednu chybu na malý počet aplikací. Výchozí procento je nula. |
| --max-percent-unhealthy-uzly | Při vyhodnocování stavu clusteru během Chaos, maximální povolené procento uzly není v pořádku před hlášením chyby. <br><br> Maximální povolené procento uzlů není v pořádku před oznámením chyby. Chcete-li například povolit 10 % uzlů, které mají být nefunkční, tato hodnota by byla 10. Procento představuje maximální tolerované procento uzlů, které mohou být nefunkční, než je cluster považován za chybný. Pokud je procento respektováno, ale existuje alespoň jeden uzel není v pořádku, stav je vyhodnocen jako upozornění. Procento se vypočítá vydělením počtu uzlů není v pořádku nad celkový počet uzlů v clusteru. Výpočtem zaokrouhluje nahoru tolerovat jednu poruchu na malý počet uzlů. Výchozí procento je nula. Ve velkých clusterech budou některé uzly vždy mimo nebo mimo pro opravy, takže toto procento by mělo být nakonfigurováno tak, aby to tolerovalo. |
| --time-to-run | Celkový čas (v sekundách), po který bude Chaos spuštěn před automatickým zastavením. Maximální povolená hodnota je 4,294,967,295 (System.UInt32.MaxValue).  Výchozí\: hodnota 4294967295. |
| --časový čas -t | Výchozí\: 60. |
| --wait-time-between-faults --wait-time-between-faults --wait-time-between-faults --wait | Čekací doba (v sekundách) mezi po sobě jdoucími chybami v rámci jedné iterace.  Výchozí\: 20. <br><br> Čím větší hodnota, tím nižší je překrývání mezi chybami a jednodušší pořadí přechodů stavu, které cluster prochází. Doporučujeme začít s hodnotou mezi 1 a 5 a buďte opatrní při pohybu nahoru. |
| --wait-time-between-iterations --wait-time-between-iterations --wait-time-between-iterations --wait | Časové oddělení (v sekundách) mezi dvěma po sobě jdoucími iterací Chaosu. Čím větší je hodnota, tím nižší je rychlost vstřikování poruchy.  Výchozí\: 30. |
| --upozornění jako chyba | Označuje, zda jsou upozornění považována za chyby se stejnou závažností. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos zastavit
Zastaví Chaos, pokud je spuštěn v clusteru a dát Chaos Plán v zastaveném stavu.

Zabrání chaosu v provádění nových chyb. Poruchy za letu budou pokračovat, dokud nebudou dokončeny. Aktuální chaos plán je uveden do zastaveného stavu. Jakmile je plán zastaven, zůstane v zastaveném stavu a nebude použit k chaosu naplánovat nové běhy Chaosu. Aby bylo možné pokračovat v plánování, musí být nastaven nový plán chaosu.

### <a name="arguments"></a>Argumenty

|Argument|Popis|
| --- | --- |
| --časový čas -t | Časový rozsah serveru pro provedení operace v sekundách. Tento časový limit určuje dobu trvání, po kterou je klient ochoten čekat na dokončení požadované operace. Výchozí hodnota tohoto parametru je 60 sekund.  Výchozí\: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Popis|
| --- | --- |
| --ladění | Zvyšte podrobnost protokolování, chcete-li zobrazit všechny protokoly ladění. |
| --pomoc -h | Zobrazit tuto zprávu nápovědy a ukončete ji. |
| --výstup -o | Výstupní formát.  Povolené\: hodnoty json, jsonc, tabulka, tsv.  Výchozí\: json. |
| --dotaz | Řetězec dotazu JMESPath. Další\:informace a příklady naleznete na adrese http //jmespath.org/. |
| --verbose | Zvyšte podrobnost protokolování. Použijte --debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Setup](service-fabric-cli.md) the Service Fabric CLI.
- Naučte se používat funkce CLI service fabric pomocí [ukázkových skriptů](/azure/service-fabric/scripts/sfctl-upgrade-application).