---
title: Azure Service Fabric CLI – sfctl chaos
description: Přečtěte si o sfctl rozhraní příkazového řádku Azure Service Fabric. Obsahuje seznam příkazů pro správu chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260834"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Spuštění, zastavení a hlášení služby chaos test Service.

## <a name="subgroups"></a>Podskupiny
|Podskupina|Description|
| --- | --- |
| [CXL](service-fabric-sfctl-chaos-schedule.md) | Získejte a nastavte plán chaos. |
## <a name="commands"></a>Příkazy

|Příkaz|Popis|
| --- | --- |
| stránka events | Získá další segment událostí chaos na základě tokenu pro pokračování nebo časového rozsahu. |
| get | Získejte stav chaos. |
| start | Spustí chaos v clusteru. |
| Zastavit | Zastaví chaos, pokud je spuštěný v clusteru a umístí chaos plán do zastaveného stavu. |

## <a name="sfctl-chaos-events"></a>sfctl události chaos
Získá další segment událostí chaos na základě tokenu pro pokračování nebo časového rozsahu.

Chcete-li získat další segment událostí chaos, můžete zadat token continuationtoken. Chcete-li získat začátek nového segmentu událostí chaos, můžete zadat časový rozsah prostřednictvím StartTimeUtc a EndTimeUtc. Nemůžete zadat jak token continuationtoken, tak i časový rozsah ve stejném volání. Pokud je k dispozici více než 100 událostí chaos, vrátí se události chaos ve více segmentech, kde segment neobsahuje více než 100 událostí chaos a získá další segment, který zavoláte do tohoto rozhraní API s tokenem pro pokračování.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --pokračování-token | Parametr tokenu pokračování slouží k získání další sady výsledků. Token pokračování s neprázdnou hodnotou je zahrnut v odpovědi rozhraní API v případě, že se výsledky ze systému nevejdou do jediné odpovědi. Když se tato hodnota předává do dalšího volání rozhraní API, vrátí rozhraní API další sadu výsledků. Pokud nejsou k dispozici žádné další výsledky, token pokračování neobsahuje hodnotu. Hodnota tohoto parametru nesmí být kódovaná v adrese URL. |
| --koncový čas – UTC | Čas souboru systému Windows představující čas ukončení časového rozsahu, pro který má být vygenerována sestava chaos. Podrobnosti naleznete v [metodě DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --Max – výsledky | Maximální počet výsledků, které mají být vráceny v rámci stránkovaných dotazů. Tento parametr definuje horní mez počtu vrácených výsledků. Vrácené výsledky mohou být menší než zadané maximální výsledky, pokud se nevejdou do zprávy podle maximální velikosti omezení velikosti zprávy definované v konfiguraci. Pokud je tento parametr nula nebo není zadán, dotaz stránkovaného obsahu obsahuje tolik výsledků, kolik jich bylo možné do návratové zprávy. |
| --Start-Time-UTC | Čas souboru systému Windows představující čas zahájení časového rozsahu, pro který má být vygenerována sestava chaos. Podrobnosti naleznete v [metodě DateTime. ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-get"></a>sfctl chaos Get
Získejte stav chaos.

Získá stav chaos, který označuje, jestli je chaos spuštěný, parametry chaos používané pro spuštění chaos a stav plánu chaos.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-start"></a>sfctl chaos Start
Spustí chaos v clusteru.

Pokud chaos v clusteru ještě neběží, spustí se chaos s předanými parametry chaos. Pokud je po provedení tohoto volání již spuštěno chaos, volání se nezdařilo s kódem chyby FABRIC_E_CHAOS_ALREADY_RUNNING. Další podrobnosti najdete [Service Fabric v článku o chaos řízených v clusterech](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) .

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Typ aplikace-Health-Policy-– mapování | Pole kódovaného slovníku (klíč/hodnota) JSON s maximálním počtem nezdravých aplikací pro konkrétní typy aplikací. Každá položka slovníku určuje jako klíč název typu aplikace a celé číslo pro hodnotu, která představuje MaxPercentUnhealthyApplications procento, které slouží k vyhodnocení aplikací zadaného typu aplikace. <br><br> Definuje mapu s maximálním procentem nezdravých aplikací pro konkrétní typy aplikací. Mapování zásad stavu aplikace lze použít při vyhodnocení stavu clusteru k popisu jednotlivých typů aplikací. Typy aplikací, které jsou součástí mapy, jsou vyhodnocovány proti procentům uvedeným v mapě a nikoli s globálním MaxPercentUnhealthyApplications definovaným v zásadách stavu clusteru. Aplikace typů aplikací zadaných na mapě se nezapočítávají na Globální fond aplikací. Pokud jsou například některé aplikace typu kritické, může správce clusteru přidat položku na mapu pro daný typ aplikace a přiřadit jí hodnotu 0% (Netolerovat žádné chyby). Všechny ostatní aplikace je možné vyhodnotit s MaxPercentUnhealthyApplications nastavenou na 20% a tolerovat některé chyby z tisíců instancí aplikací. Mapa zásad stavu aplikace se používá jenom v případě, že clusterový manifest umožňuje vyhodnocování stavu aplikace pomocí položky konfigurace pro HealthManager/EnableApplicationTypeHealthEvaluation. <br><br> Příklad řetězce kódovaného JSON: [{ \" Key \" : \" Fabric:/hlasovacího \" , \" Value \" : \" 0 \" }] |
| --chaos-Target-Filter | Slovník kódovaný ve formátu JSON se dvěma klíči řetězcového typu. Tyto dva klíče jsou NodeTypeInclusionList a ApplicationInclusionList. Hodnoty pro oba tyto klíče jsou seznamem řetězců. chaos_target_filter definuje všechny filtry pro cílené chyby chaos, například při selhání pouze určitých typů uzlů nebo při selhání pouze určitých aplikací. <br><br> Pokud se chaos_target_filter nepoužívá, chaos chyby všechny entity clusteru. Pokud se používá chaos_target_filter, chaos chyby pouze entity, které splňují specifikaci chaos_target_filter. NodeTypeInclusionList a ApplicationInclusionList umožňují pouze sémantiku sjednocení. Není možné zadat průnik NodeTypeInclusionList a ApplicationInclusionList. Například není možné zadat "selhání této aplikace pouze v případě, že je na tomto typu uzlu". Jakmile je entita zahrnutá v NodeTypeInclusionList nebo ApplicationInclusionList, tuto entitu nejde vyloučit pomocí ChaosTargetFilter. I v případě, že se applicationX v ApplicationInclusionList nezobrazí, může v některých chaos iterace applicationX dojít k chybě, protože se stane na uzlu nodeType, který je součástí NodeTypeInclusionList. Pokud jsou NodeTypeInclusionList i ApplicationInclusionList prázdné, je vyvolána výjimka ArgumentException. Pro uzly těchto typů uzlů jsou povoleny všechny typy chyb (restartovat uzel, restartovat balíček kódu, odebrat repliku, restartovat repliku, přesunout primární a přesunout sekundární). Pokud se typ uzlu (říká NodeTypeX) se nezobrazí v NodeTypeInclusionList, pak chyby na úrovni uzlu (jako NodeRestart) nikdy nebudou pro uzly NodeTypeX povolené, ale pokud se aplikace v NodeTypeX stane umístěnou v uzlu ApplicationInclusionList, můžou se pořád povolit balíčky kódu a chyby repliky. V tomto seznamu může být zahrnutých maximálně 100 názvů typů uzlů. aby se tento počet zvýšil, je pro konfiguraci MaxNumberOfNodeTypesInChaosEntityFilter nutný upgrade konfigurace. Všechny repliky patřící ke službám těchto aplikací jsou snadněji k chybám repliky (restartování repliky, odebrání repliky, přesunutí primárního a přesunutí sekundárního) pomocí chaos. Chaos může restartovat balíček kódu pouze v případě, že balíček kódu hostuje pouze repliky těchto aplikací. Pokud se aplikace v tomto seznamu nezobrazí, může být stále chyba v některém chaos iteraci, pokud aplikace skončí na uzlu typu uzlu, který je součástí NodeTypeInclusionList. Pokud je však applicationX svázán s nodeType prostřednictvím omezení umístění a applicationX chybí od ApplicationInclusionList a uzel nodeType není v NodeTypeInclusionList, pak applicationX nebude nikdy chybět. V tomto seznamu může být zahrnutých maximálně 1000 názvů aplikací. aby se tento počet zvýšil, je pro konfiguraci MaxNumberOfApplicationsInChaosEntityFilter nutný upgrade konfigurace. |
| --kontext | Mapa kódovaná pomocí formátu JSON (řetězec, řetězec) typu páry klíč-hodnota. Mapu lze použít k zaznamenání informací o spuštění chaos. Takové páry nesmí být větší než 100 a každý řetězec (klíč nebo hodnota) může mít maximálně 4095 znaků. Tato mapa je nastavená počáteční verzí chaos spuštění na volitelně uložit kontext týkající se konkrétního běhu. |
| --Disable-Move-replike-Faults | Zakáže sekundární chyby přesunout primární a přesunout. |
| --Max-cluster-stabilizace | Maximální doba, po kterou se má čekat, až budou všechny entity clusteru stabilní a v dobrém stavu.  Výchozí hodnota je \: 60. <br><br> Chaos se spustí v iteracích a na začátku každé iterace ověří stav entit clusteru. Při ověřování, pokud entita clusteru není stabilní a v pořádku v rámci MaxClusterStabilizationTimeoutInSeconds, chaos vygeneruje událost neúspěšného ověření. |
| --Max-souběžné – chyby | Maximální počet souběžných chyb vyvolaných v rámci iterace. Chaos se provádí v iteracích a dvě po sobě jdoucí iterace jsou oddělené fází ověření. Čím vyšší je souběžnost, tím efektivnější je vkládání chyb – vystavování složitějších sérií stavů za účelem zjištění chyb. Doporučení je začínat hodnotou 2 nebo 3 a při přechodu do režimu opatrnosti.  Výchozí hodnota \: 1. |
| --Max-Percent-není v pořádku – aplikace | Při vyhodnocování stavu clusteru během chaos je před Nahlášením chyby maximální povolené procento nefunkčních aplikací. <br><br> Maximální povolené procento nezdravých aplikací před Nahlášením chyby. Například pokud chcete, aby 10% aplikací bylo ve špatném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl aplikací, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje aspoň jedna poškozená aplikace, stav se vyhodnotí jako varování. To se počítá vydělením počtu nezdravých aplikací nad celkovým počtem instancí aplikace v clusteru, kromě aplikací typů aplikací, které jsou součástí ApplicationTypeHealthPolicyMap. Výpočet se zaokrouhlí na jednu neúspěch u malého počtu aplikací. Výchozí procento je nula. |
| --Max-Percent-není v pořádku – uzly | Při vyhodnocování stavu clusteru během chaos je maximální povolené procento uzlů, které nejsou v pořádku, před odesláním chyby. <br><br> Maximální povolené procento uzlů, které nejsou v pořádku, před odesláním chyby. Pokud například chcete, aby 10% uzlů bylo v nesprávném stavu, bude tato hodnota 10. Procento představuje maximální povolený procentuální podíl uzlů, které mohou být chybné, než se cluster bude považovat za chybu. Pokud je dodrženo procento, ale existuje alespoň jeden uzel, který není v pořádku, je stav vyhodnocen jako upozornění. Procentuální hodnota se vypočítá vydělením počtu uzlů, které nejsou v pořádku, na celkový počet uzlů v clusteru. Výpočet se zaokrouhlí na jednu neúspěch na malých číslech uzlů. Výchozí procento je nula. Ve velkých clusterech se u některých uzlů vždycky odeberou nebo odeberou opravy, takže toto procento by mělo být nakonfigurované na tolerování. |
| --čas spuštění | Celková doba (v sekundách), po kterou se chaos spustí před automatickým zastavením Maximální povolená hodnota je 4 294 967 295 (System. UInt32. MaxValue).  Výchozí hodnota je \: 4294967295. |
| --Timeout-t | Výchozí hodnota je \: 60. |
| --čekací doba-mezi chybami | Doba čekání (v sekundách) mezi po sobě jdoucími chybami v rámci jedné iterace.  Výchozí hodnota je \: 20. <br><br> Čím větší je hodnota, tím nižší je překrývání mezi chybami a jednodušší sekvence přechodů stavu, které cluster prochází. Doporučení je začínat hodnotou v rozmezí od 1 do 5 a při přesunu se můžete věnovat opatrnosti. |
| --Wait-Time-Between-iterace | Časové oddělení (v sekundách) mezi dvěma po sobě jdoucími iteracemi chaos. Čím větší je hodnota, tím nižší je míra vkládání chyb.  Výchozí hodnota je \: 30. |
| --Warning-as-Error | Označuje, zda jsou upozornění zpracována se stejnou závažností jako chyby. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos zastavení
Zastaví chaos, pokud je spuštěný v clusteru a umístí chaos plán do zastaveného stavu.

Zastaví chaos spouštění nových chyb. Probíhající chyby se budou i nadále spouštět, dokud nebudou dokončeny. Aktuální plán chaos je přepnut do stavu Zastaveno. Po zastavení plánu zůstane stav zastaveno a nebude se používat k chaos plánování nových spuštění chaos. Aby bylo možné pokračovat v plánování, je třeba nastavit nový plán chaos.

### <a name="arguments"></a>Argumenty

|Argument|Description|
| --- | --- |
| --Timeout-t | Časový limit serveru pro provedení operace během několika sekund. Tento časový limit určuje dobu, po kterou bude klient ochotn počkat na dokončení požadované operace. Výchozí hodnota pro tento parametr je 60 sekund.  Výchozí hodnota je \: 60. |

### <a name="global-arguments"></a>Globální argumenty

|Argument|Description|
| --- | --- |
| --ladění | Zvyšte úroveň podrobností protokolování, aby se zobrazily všechny protokoly ladění. |
| --Help-h | Zobrazí tuto zprávu s upozorněním a ukončí. |
| --výstup-o | Výstupní formát.  Povolené hodnoty \: : JSON, jsonc, Table, TSV.  Výchozí \: JSON. |
| --dotaz | Řetězec dotazu JMESPath \:Další informace a příklady najdete v tématu http//jmespath.org/. |
| --verbose | Zvyšte úroveň podrobností protokolování. Použijte--Debug pro úplné protokoly ladění. |


## <a name="next-steps"></a>Další kroky
- [Nastavte](service-fabric-cli.md) Service Fabric CLI.
- Naučte se používat rozhraní příkazového řádku Service Fabric s použitím [ukázkových skriptů](./scripts/sfctl-upgrade-application.md).
