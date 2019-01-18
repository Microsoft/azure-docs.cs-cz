---
title: Azure Service Fabric, monitorování a Diagnostika – přehled | Dokumentace Microsoftu
description: Další informace o monitorování a diagnostiky pro Azure Service Fabric clusterů, aplikací a služeb.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: f558c6fcfa864b142209712a536adf1be97122cf
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389248"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorování a diagnostiky pro Azure Service Fabric

Tento článek obsahuje přehled monitorování a diagnostiky pro Azure Service Fabric. Monitorování a Diagnostika jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli prostředí cloud. Můžete například sledovat, jak vaše aplikace se používají, akce prováděné pomocí platformy Service Fabric, využití prostředků pomocí čítačů výkonu a celkový stav clusteru. Tyto informace můžete diagnostikovat a opravit problémy a zabránili jejich výskytu v budoucnu. Další několik části stručně popisují každou oblast Service Fabric monitorování vzít v úvahu pro produkční úlohy. 

## <a name="application-monitoring"></a>Monitorování aplikace
Monitorování aplikací, sleduje používání funkcí a komponent vaší aplikace. Budete chtít monitorovat Ujistěte se, že problémy tohoto dopadu, které jsou zachyceny uživatelé vaší aplikace. Je na starost monitorování aplikací na uživatele, kteří vyvíjejí aplikace a jeho služeb, protože je jedinečné pro obchodní logice aplikace. Monitorování aplikací může být užitečné v následujících scénářích:
* Kolik provozu mojí aplikaci dochází? – Je potřeba škálovat vaše služby, které splňují požadavky na uživatele nebo adresu potenciální problémová místa ve vaší aplikaci?
* Jsou moje volání mezi službami úspěšné a sledované?
* Jaké akce pocházejí uživatelé mojí aplikace? – Shromažďování telemetrických dat můžete Průvodce vývoj budoucích funkcí a lepší diagnostiku pro chyby aplikace
* Moje aplikace vyvolává neošetřené výjimky? 
* Co se děje ve službách spuštěných ve své kontejnery?

Nejlepší o monitorování aplikací je, že vývojáři můžou využívat jakékoli nástroje a rozhraní, co by rádi, protože se nachází v rámci kontextu vaší aplikace! Další informace o řešení Azure pro monitorování aplikací prostřednictvím služby Azure Monitor – Application Insights v [analýza událostí s využitím Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Máme také kurz s postupy [nastavíte pro aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md). Tento kurz prochází přes způsobu jejich instalace ty správné nástroje, příklad zapisovat vlastní telemetrická data do vaší aplikace a zobrazení rozhraní application diagnostics a telemetrických dat na webu Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Monitorování platformy (Cluster)
Uživatel má kontrolu nad jaké telemetrická data pochází z jejich aplikace protože uživatel zapíše kód samotné, ale co o diagnostické z platformy Service Fabric? Jedním z cílů Service Fabric je udržovat aplikace odolné vůči selhání hardwaru. Tento cíl se dosahuje prostřednictvím možnosti platformy systémových služeb ke zjištění problémů s infrastrukturou a rychlé převzetí služeb při selhání úlohy jiným uzlům v clusteru. Ale v tomto konkrétním případě co když vlastních služeb systému mají problémy? Nebo pokud při pokusu o nasazení nebo přesunutí úlohy, jsou pravidla pro umístění služby došlo k porušení? Service Fabric nabízí diagnostiku pro tyto a další zajistit, aby že byli informováni o probíhající ve vašem clusteru aktivity. Několik ukázkových scénářů pro monitorování clusterů patří:

Service Fabric nabízí komplexní sadu událostí úprav. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) přístupné prostřednictvím Eventstoru nebo provozní kanál (událost kanálu vystavené platformu). 

* Service Fabric události kanálů – Windows na Service Fabric jsou k dispozici z jednoho zprostředkovatele trasování událostí pro Windows se sadou relevantní `logLevelKeywordFilters` použijí k výběru mezi provozní Data a zasílání zpráv v kanálech – Toto je způsob, ve kterém jsme oddělit odchozí Service Fabric události filtrovanou na podle potřeby. Události Service Fabric v Linuxu, procházejí LTTng a jsou vloženy do jedné tabulky úložiště, ze které se dají se filtrovat podle potřeby. Tyto kanály obsahovat kurátorované, strukturované události, které lze použít pro lepší pochopení stavu vašeho clusteru. Je povolená Diagnostika ve výchozím nastavení při vytváření clusteru, které vytvářejí tabulce Azure Storage se odešle události z těchto kanálů pro vás k dotazování v budoucnu. 

* Eventstoru - Eventstoru je funkce nabízených platformou, která poskytuje události platformy Service Fabric jsou k dispozici v Service Fabric Exploreru a prostřednictvím rozhraní REST API. Zobrazí se zobrazení snímku, co se děje ve vašem clusteru pro každou entitu například uzel, služby, aplikace a pro dotazy podle času události. Můžete také další informace o Eventstoru na [přehled Eventstoru](service-fabric-diagnostics-eventstore.md).    

![Eventstoru](media/service-fabric-diagnostics-overview/eventstore.png)

Diagnostika k dispozici jsou ve formě komplexní sadu událostí úprav. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) znázorňují akce prováděné platformy na různé entity, jako je například uzly, aplikace, služby, oddíly atd. V posledním scénáři nad uzel, přestanou fungovat, pokud platformu by generování `NodeDown` událostí a může dostat tak oznámení okamžitě tak váš nástroj pro sledování podle výběru. Další běžné příklady `ApplicationUpgradeRollbackStarted` nebo `PartitionReconfigured` při selhání. **Stejné události jsou k dispozici na clustery s Linuxem a Windows.**

Události se odesílají prostřednictvím standardních kanálů ve Windows a Linuxu a mohli číst monitorovacího nástroje, který podporuje tyto. Řešení Azure Monitor je služba Log Analytics. Nebojte se další informace o našich [Log Analytics integrace](service-fabric-diagnostics-event-analysis-oms.md) obsahující vlastní provozní řídicí panel pro váš cluster a několik ukázkových dotazů, ze kterých můžete vytvářet upozornění. Další koncepty monitorování clusteru najdete na adrese [generování úrovně událostí a protokolu platformy](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric zahrnuje stav modelu, který poskytuje rozšiřitelné zdraví, vytváření sestav pro stav entity v clusteru. Každý uzel, aplikace, služby, oddílu, replik nebo instancí, se průběžně aktualizovat stav. Stav může být buď "OK", "Varování" nebo "Chyba". Události Service Fabric můžete představit jako příkazy, které provádí cluster tak, aby různé entity a stavu jako přídavné pro každou entitu. Pokaždé, když změní stav na konkrétní entitu, události se také být vygenerován. Tímto způsobem můžete nastavit výstrahy pro události týkající se stavu a dotazů v nástroji pro monitorování podle vlastní volby, stejně jako jakákoli jiná událost. 

Kromě toho jsme i uživatelům umožňují přepsat stavu pro entity. Pokud vaše aplikace právě probíhá upgrade a máte ověřovací testy, které selhaly, můžete zapisovat do stavu Service Fabric pomocí rozhraní API stavu k označení aplikace již není v pořádku a Service Fabric budou automaticky vrácení zpět inovace! Další informace o stavu modelu, podívejte se [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md)

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Obecně platí sledovacího zařízení je samostatná služba, která může sledovat stav a zatížení mezi koncové body ping, služeb a stavu sestavy pro všechno, co v clusteru. To může pomoci zabránit chybám, které nebudou zjištěny založené na zobrazení jedné služby. Watchdogs je také vhodné místo pro hostování kódu, který provede nápravné akce bez zásahu uživatele (například čištění protokolu souborů v úložišti v určitých časových intervalech). Můžete najít ukázku implementace služby sledovacích [tady](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorování infrastruktury (výkon)
Teď, když jsme si popsali diagnostiku ve vaší aplikace a platformy, jak víme, že hardware na funguje podle očekávání? Monitorování základní infrastruktury je klíčovou součástí pochopení stavu vašeho clusteru a využití prostředků. Měření výkonu systému, závisí na mnoha faktorech, které může být subjektivní v závislosti na vašich úloh. Tyto faktory jsou většinou měří prostřednictvím čítače výkonu. Tyto čítače výkonu můžou pocházet z nejrůznějších zdrojů, včetně operačního systému, rozhraní .NET framework nebo samotné platformě Service Fabric. Některé scénáře, ve kterých by byly užitečné jsou

* Teď můžu využívá hardware efektivně? Opravdu chcete použít svůj hardware na 90 % využití procesoru nebo procesoru 10 %. To je užitečné, když škálování clusteru nebo optimalizace procesů vaší aplikace.
* Mohu nějak předpovědět, problémů s infrastrukturou proaktivně? – Mnoho problémů předchází náhlých změn (drops) výkon, abyste mohli používat čítače výkonu, jako je využití vstupně-výstupních operací a procesoru předpovídat a diagnostikovat problémy aktivně sítě.

Seznam čítačů výkonu, které by měly být shromažďovány na úrovni infrastruktury najdete v [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md). 

Alsp Service Fabric poskytuje sady čítačů výkonu pro programovací modely Reliable Services a objekty actor. Pokud používáte některý z těchto modelů, můžete tyto čítače výkonu informací zajistit, že jsou vaše actors zprovozňování navýšení nebo snížení kapacity správně, nebo že žádostí o spolehlivé služby jsou zpracovávanou dostatečně rychle. Další informace najdete v tématu [monitorování pro vzdálenou komunikaci Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) a [sledování výkonu služby Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Shromažďování těchto řešení Azure Monitor je služba Log Analytics stejně jako monitorování na úrovni platformy. Měli byste použít [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) ke shromažďování čítačů výkonu odpovídající a zobrazení v Log Analytics.

## <a name="recommended-setup"></a>Doporučené nastavení
Teď, když jsme šli za každé oblasti monitorování a ukázkové scénáře, tady je přehled nástroje pro monitorování a nastavit potřebné k monitorování všech výše uvedených oblastech Azure. 

* Monitorování aplikací pomocí [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorování clusterů pomocí [agenta diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md) a [Log Analytics](service-fabric-diagnostics-oms-setup.md)
* Monitorování infrastruktury s využitím [Log Analytics](service-fabric-diagnostics-oms-agent.md)

Můžete také použít a změnit Ukázková šablona ARM nachází [tady](service-fabric-diagnostics-oms-setup.md#deploy-log-analytics-with-azure-resource-manager) k automatizaci nasazení všechny potřebné prostředky a agentů. 

## <a name="other-logging-solutions"></a>Jiná řešení protokolování

I když obě řešení je vhodné, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) vytvořili mnoho událostí jsou zapsané pomocí zprostředkovatelů trasování událostí pro Windows a jsou v integraci s platformou Service Fabric Rozšiřitelné s jinými řešeními pro protokolování. Také byste se podívat do [řešení Elastic Stack](https://www.elastic.co/products) (zejména pokud uvažujete o spuštění clusteru v prostředí offline), [Dynatrace](https://www.dynatrace.com/), nebo jakékoli jiné platformě dáváte přednost. Máme seznam integrovaných partnerů, které jsou k dispozici [tady](service-fabric-diagnostics-partners.md).

Klíčové body pro jakoukoli platformu, že kterou si zvolíte by měl obsahovat jak pohodlné se pomocí uživatelského rozhraní, možnosti dotazování, vlastní vizualizace a řídicí panely, které jsou k dispozici, a další nástroje poskytují k vylepšení prostředí pro monitorování. 

## <a name="next-steps"></a>Další postup

* Začínáme s instrumentace aplikací, najdete v části [aplikace úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-app.md).
* Projděte si kroky k nastavení Application Insights pro vaši aplikaci s [monitorování a Diagnostika aplikace ASP.NET Core v Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Další informace o monitorování na platformu a události Service Fabric nabízí za vás na [generování úrovně událostí a protokolu platformy](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace integrace Log Analytics s využitím Service Fabric v [nastavení Log Analytics pro cluster](service-fabric-diagnostics-oms-setup.md)
* Zjistěte, jak nastavit službu Log Analytics pro monitorování kontejnerů- [monitorování a Diagnostika pro Windows kontejnery v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Viz příklad diagnostiky problémů a řešení s využitím Service Fabric v [Diagnostika běžné scénáře](service-fabric-diagnostics-common-scenarios.md)
* Podívejte se na další diagnostiky produkty, které se integrují s využitím Service Fabric v [diagnostických partneři Service Fabric](service-fabric-diagnostics-partners.md)
* Další informace o obecných doporučení pro monitorování pro prostředky Azure – [osvědčené postupy – monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 