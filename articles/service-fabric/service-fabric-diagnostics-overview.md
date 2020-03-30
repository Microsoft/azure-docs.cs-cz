---
title: Přehled monitorování a diagnostiky infrastruktury azure service fabric
description: Další informace o monitorování a diagnostice clusterů, aplikací a služeb Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282481"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorování a diagnostika pro Azure Service Fabric

Tento článek obsahuje přehled monitorování a diagnostiky pro Azure Service Fabric. Monitorování a diagnostika jsou důležité pro vývoj, testování a nasazování úloh v libovolném cloudovém prostředí. Můžete například sledovat, jak jsou vaše aplikace používány, akce prováděné platformou Service Fabric, využití prostředků s čítači výkonu a celkový stav clusteru. Tyto informace můžete použít k diagnostice a opravě problémů a zabránit jejich výskytu v budoucnu. V několika následujících částech stručně vysvětlí každou oblast monitorování Service Fabric zvážit pro produkční úlohy. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorování aplikace
Monitorování aplikací sleduje, jak jsou používány funkce a součásti aplikace. Chcete sledovat vaše aplikace a ujistěte se, že problémy, které mají vliv na uživatele, jsou zachyceny. Odpovědnost za monitorování aplikací je na uživatelích, kteří vyvíjejí aplikaci a její služby, protože je jedinečná pro obchodní logiku vaší aplikace. Sledování aplikací může být užitečné v následujících scénářích:
* Kolik provozu je moje aplikace zažívá? - Potřebujete škálovat své služby tak, aby splňovaly požadavky uživatelů nebo řešily potenciální problémové místo ve vaší aplikaci?
* Jsou moje služba servisních hovorů úspěšná a sledována?
* Jaké akce provádějí uživatelé mé aplikace? - Sběr telemetrie může vést vývoj budoucích funkcí a lepší diagnostiku chyb aplikací
* Je moje aplikace vyvolání neošetřené výjimky? 
* Co se děje v rámci služeb spuštěných v mých kontejnerech?

Skvělá věc, o monitorování aplikací je, že vývojáři mohou používat jakékoli nástroje a rámec, které by chtěli, protože žije v kontextu vaší aplikace! Další informace o řešení Azure pro monitorování aplikací najdete pomocí Azure Monitor – Application Insights in [Event analysis s Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Máme také návod, jak [nastavit to pro .NET Aplikace](service-fabric-tutorial-monitoring-aspnet.md). Tento kurz se podrobně popisuje, jak nainstalovat správné nástroje, příklad zápisu vlastní telemetrie ve vaší aplikaci a zobrazení diagnostiky aplikací a telemetrie na webu Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Monitorování platformy (clusteru)
Uživatel má kontrolu nad tím, co telemetrie pochází z jejich aplikace, protože uživatel zapíše samotný kód, ale co diagnostika z platformy Service Fabric? Jedním z cílů service fabric je udržet aplikace odolné vůči selhání hardwaru. Tohoto cíle je dosaženo prostřednictvím schopnosti systémových služeb platformy odhalovat problémy s infrastrukturou a rychle překračovat úlohy převzetí služeb při selhání do jiných uzlů v clusteru. Ale v tomto konkrétním případě, co když systémové služby samy o sobě mají problémy? Nebo pokud při pokusu o nasazení nebo přesunutí pracovního vytížení jsou porušena pravidla pro umístění služeb? Service Fabric poskytuje diagnostiku pro tyto a další a ujistěte se, že jste informováni o činnosti probíhá ve vašem clusteru. Některé ukázkové scénáře pro monitorování clusteru zahrnují:

Service Fabric poskytuje komplexní sadu událostí po vybalení z krabice. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) lze přistupovat prostřednictvím EventStore nebo provozní kanál (kanál událostí vystavené platformou). 

* Service Fabric kanály událostí – v systému Windows, Service Fabric události `logLevelKeywordFilters` jsou k dispozici od jednoho poskytovatele ETW se sadou relevantní slouží k výběru mezi provozní a datové & zasílání zpráv kanály – to je způsob, jakým jsme oddělit odchozí Service Fabric události, které mají být filtrovány na podle potřeby. Na Linuxu service fabric události projít LTTng a jsou umístěny do jedné tabulky úložiště, odkud mohou být filtrovány podle potřeby. Tyto kanály obsahují kurátorské, strukturované události, které lze použít k lepšímu pochopení stavu clusteru. Diagnostika je ve výchozím nastavení povolena v době vytvoření clusteru, které vytvářejí tabulku azure storage, kde se odesílají události z těchto kanálů, abyste se v budoucnu dotazovali. 

* EventStore - EventStore je funkce nabízená platformou, která poskytuje události platformy Service Fabric, které jsou k dispozici v aplikaci Service Fabric Explorer a prostřednictvím rozhraní REST API. Můžete zobrazit snímek zobrazení toho, co se děje ve vašem clusteru pro každou entitu, například uzel, služba, aplikace a dotaz na základě času události. Další informace o EventStore si můžete přečíst v [přehledu eventstore](service-fabric-diagnostics-eventstore.md).    

![Úložiště událostí](media/service-fabric-diagnostics-overview/eventstore.png)

Poskytnutá diagnostika je ve formě komplexní sady událostí po vybalení z krabice. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) ilustrují akce provedené platformou na různých entitách, jako jsou uzly, aplikace, služby, oddíly atd. V posledním scénáři výše, pokud uzel měl jít dolů, `NodeDown` platforma by vyzařovat událost a můžete být okamžitě upozorněni pomocí nástroje sledování volby. Mezi další běžné `ApplicationUpgradeRollbackStarted` `PartitionReconfigured` příklady patří převzetí služeb při selhání nebo během ní. **Stejné události jsou k dispozici na clusterech Windows i Linux.**

Události jsou odesílány prostřednictvím standardních kanálů na Windows i Linux a mohou být čteny libovolným monitorovacím nástrojem, který je podporuje. Řešení Azure Monitor uvězne protokoly Azure Monitoru. Neváhejte a přečtěte si další informace o integraci [protokolů Azure Monitor,](service-fabric-diagnostics-event-analysis-oms.md) která zahrnuje vlastní operační řídicí panel pro váš cluster a některé ukázkové dotazy, ze kterých můžete vytvářet výstrahy. Další koncepty monitorování clusteru jsou k dispozici na [úrovni platformy události a generování protokolu](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric obsahuje model stavu, který poskytuje vykazování rozšiřitelného stavu pro stav entit v clusteru. Každý uzel, aplikace, služba, oddíl, replika nebo instance mají nepřetržitě aktualizovatelný stav. Stav může být buď "OK", "Upozornění" nebo "Chyba". Události Service Fabric si můžete zamyslet jako slovesa provedená clusterem různým entitám a stavu jako přídavné jméno pro každou entitu. Pokaždé, když se změní stav určité entity, bude také vyzařována událost. Tímto způsobem můžete nastavit dotazy a výstrahy pro události stavu v nástroji pro sledování volby, stejně jako každá jiná událost. 

Kromě toho jsme dokonce umožňují uživatelům přepsat stav pro entity. Pokud vaše aplikace prochází upgradea máte ověřovací testy se lhem, můžete napsat service fabric stavu pomocí rozhraní API stavu k označení aplikace již není v pořádku a Service Fabric automaticky vrátí zpět upgrade! Další informace o modelu stavu najdete v [úvodu k monitorování stavu service fabric](service-fabric-health-introduction.md)

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Hlídací psi
Obecně watchdog je samostatná služba, která může sledovat stav a zatížení napříč službami, ping koncové body a sestavy stavu pro cokoli v clusteru. To může pomoci zabránit chybám, které by nebyly zjištěny na základě zobrazení jedné služby. Watchdogs jsou také vhodné místo pro hostování kódu, který provádí nápravné akce bez interakce s uživatelem (například čištění souborů protokolu v úložišti v určitých časových intervalech). Ukázkové implementace služby watchdog [naleznete zde](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorování infrastruktury (výkonu)
Nyní, když jsme se zabývali diagnostikou ve vaší aplikaci a platformě, jak víme, že hardware funguje podle očekávání? Monitorování základní infrastruktury je klíčovou součástí pochopení stavu clusteru a využití prostředků. Měření výkonu systému závisí na mnoha faktorech, které mohou být subjektivní v závislosti na vašich úlohách. Tyto faktory se obvykle měří prostřednictvím čítačů výkonu. Tyto čítače výkonu mohou pocházet z různých zdrojů, včetně operačního systému, rozhraní .NET framework nebo samotné platformy Service Fabric. Některé scénáře, ve kterých by byly užitečné, jsou

* Využívám svůj hardware efektivně? Chcete použít hardware na 90% CPU nebo 10% CPU. To se hodí při škálování clusteru nebo optimalizaci procesů vaší aplikace.
* Mohu proaktivně předpovědět problémy s infrastrukturou? - mnoha problémům předchází náhlé změny (poklesy) výkonu, takže můžete použít čítače výkonu, jako je vstupně-v.a. a využití procesoru, abyste proaktivně předpovídali a diagnostikovali problémy.

Seznam čítačů výkonu, které by měly být shromažďovány na úrovni infrastruktury lze nalézt na [metriky výkon](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric také poskytuje sadu čítačů výkonu pro spolehlivé služby a actors programovací modely. Pokud používáte některý z těchto modelů, tyto čítače výkonu můžete informace k zajištění, že vaše objekty actor jsou spinning nahoru a dolů správně, nebo že vaše spolehlivé požadavky na služby jsou zpracovávány dostatečně rychle. Další informace naleznete [v tématu Monitoring for Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) and [Performance monitoring for Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Řešení Azure Monitor pro jejich shromažďování je protokoly Azure Monitor stejně jako monitorování na úrovni platformy. Agent a [analýza protokolů](service-fabric-diagnostics-oms-agent.md) byste měli použít ke shromažďování příslušných čítačů výkonu a jejich zobrazení v protokolech Azure Monitor.

## <a name="recommended-setup"></a>Doporučená instalace
Teď, když jsme prošli každou oblast monitorování a příklady scénářů, tady je souhrn nástrojů pro monitorování Azure a nastavit potřebné ke sledování všech výše uvedených oblastí. 

* Monitorování aplikací pomocí [application insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorování clusteru pomocí [protokolů agenta diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md) a [azure monitoru](service-fabric-diagnostics-oms-setup.md)
* Monitorování infrastruktury pomocí [protokolů Azure Monitor](service-fabric-diagnostics-oms-agent.md)

Můžete také použít a upravit ukázkovou šablonu ARM, která je [zde](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) umístěna, a automatizovat nasazení všech potřebných prostředků a agentů. 

## <a name="other-logging-solutions"></a>Další logovací řešení

Přestože dvě řešení, která jsme doporučili, [protokoly Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) vytvořily integraci s Service Fabric, mnoho událostí je zapsáno prostřednictvím poskytovatelů ETW a je rozšiřitelné s dalšími řešeními protokolování. Měli byste se také podívat do [elastického zásobníku](https://www.elastic.co/products) (zejména pokud uvažujete o spuštění clusteru v offline prostředí), [Dynatrace](https://www.dynatrace.com/)nebo jakékoli jiné platformy podle vašich preferencí. Seznam integrovaných partnerů máme k dispozici [zde](service-fabric-diagnostics-partners.md).

Klíčové body pro libovolnou platformu, kterou zvolíte, by měly zahrnovat, jak pohodlné jste s uživatelským rozhraním, možnosti dotazování, vlastní vizualizace a řídicí panely, které jsou k dispozici, a další nástroje, které poskytují pro zlepšení vašeho monitorování. 

## <a name="next-steps"></a>Další kroky

* Začínáme s instrumentací aplikací, viz [Událost na úrovni aplikace a generování protokolu](service-fabric-diagnostics-event-generation-app.md).
* Projděte si kroky k nastavení Application Insights pro vaši aplikaci s [Monitor a diagnostikovat ASP.NET základní aplikace na Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Další informace o sledování platformy a události Service Fabric poskytuje pro vás na [úrovni platformy události a generování protokolu](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace integrace protokolů Azure Monitor s Service Fabric na [nastavení protokolů Azure Monitor pro cluster](service-fabric-diagnostics-oms-setup.md)
* Zjistěte, jak nastavit protokoly Azure Monitoru pro monitorování kontejnerů – [monitorování a diagnostika pro kontejnery Windows v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Podívejte se na příklad diagnostických problémů a řešení pomocí service fabric [v diagnostice běžných scénářů](service-fabric-diagnostics-common-scenarios.md)
* Podívejte se na další diagnostické produkty, které se integrují s Service Fabric v [diagnostických partnerech Service Fabric](service-fabric-diagnostics-partners.md)
* Další informace o obecných doporučeních monitorování prostředků Azure – [doporučené postupy – monitorování a diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 