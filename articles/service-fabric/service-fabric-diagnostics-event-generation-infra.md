---
title: Monitorování úrovně platformy Azure Service Fabric
description: Další informace o událostech a protokolech na úrovni platformy, které slouží ke sledování a diagnostice clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 720cc157111293146b796f8567f94a4f1f4830c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376932"
---
# <a name="monitoring-the-cluster"></a>Sledování clusteru

Je důležité sledovat na úrovni clusteru k určení, zda hardware a cluster se chovají podle očekávání. Ačkoli Service Fabric můžete zachovat aplikace spuštěné během selhání hardwaru, ale stále je třeba diagnostikovat, zda dojde k chybě v aplikaci nebo v základní infrastruktuře. Měli byste také sledovat clustery, abyste lépe naplánovali kapacitu a pomohli při rozhodování o přidání nebo odebrání hardwaru.

Service Fabric zveřejňuje několik událostí strukturované platformy, jako [service fabric události](service-fabric-diagnostics-events.md), prostřednictvím EventStore a různé kanály protokolu out-of-the-box. 

V systému Windows service fabric události jsou k dispozici od `logLevelKeywordFilters` jednoho poskytovatele ETW se sadou relevantní slouží k výběru mezi provozní a datové & zasílání zpráv kanály – to je způsob, jakým jsme oddělit odchozí Service Fabric události, které mají být filtrovány na podle potřeby.

* **Provozní** Operace na vysoké úrovni prováděné service fabric a clusteru, včetně událostí pro uzel blíží, nové aplikace se nasazuje nebo upgrade vrácení zpět, atd. Úplný seznam akcí naleznete [zde](service-fabric-diagnostics-event-generation-operational.md).  

* **Provozní - detailní**  
Sestavy stavu a rozhodnutí o vyrovnávání zatížení.

Provozní kanál lze přistupovat různými způsoby, včetně ETW / Windows protokoly událostí, [EventStore](service-fabric-diagnostics-eventstore.md) (k dispozici ve Windows ve verzích 6.2 a novější pro clustery Windows). EventStore umožňuje přístup k událostem clusteru na základě entity (entity včetně clusteru, uzlů, aplikací, služeb, oddílů, replik a kontejnerů) a zpřístupňuje je prostřednictvím rest API a klientské knihovny Service Fabric. Pomocí úložiště událostí můžete sledovat vaše clustery pro vývoj a testování a získat znalosti stavu produkčních clusterů v okamžiku.

* **Zasílání zpráv & dat**  
Kritické protokoly a události generované v zasílání zpráv (aktuálně pouze ReverseProxy) a datové cestě (modely spolehlivých služeb).

* **Zasílání zpráv & datových dat - podrobné**  
Podrobný kanál, který obsahuje všechny nekritické protokoly z dat a zpráv v clusteru (tento kanál má velmi vysoký objem událostí).

Kromě těchto, existují dva strukturované EventSource kanály k dispozici, stejně jako protokoly, které shromažďujeme pro účely podpory.

* [Události Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Události specifické pro programovací model.

* [Události Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Události specifické pro programovací model a čítače výkonu.

* Protokoly podpory  
Systémové protokoly generované Service Fabric pouze pro použití námi při poskytování podpory.

Tyto různé kanály pokrývají většinu protokolování na úrovni platformy, které je doporučeno. Chcete-li zlepšit protokolování na úrovni platformy, zvažte investice do lepšího pochopení modelu stavu a přidání vlastních sestav stavu a přidání **vlastních čítačů výkonu,** které vytvoří pochopení dopadu vašich služeb a aplikací na cluster v reálném čase.

Chcete-li využít těchto protokolů, důrazně doporučujeme ponechat "Diagnostika" povolena během vytváření clusteru na webu Azure Portal. Zapnutím diagnostiky při nasazení clusteru, Windows Azure Diagnostika je schopna potvrdit provozní, spolehlivé služby a spolehlivé aktéry kanály a ukládat data, jak je vysvětleno dále v [agregační události s Azure Diagnostika](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric stavu a vyrovnávání zatížení

Service Fabric má svůj vlastní model stavu, který je podrobně popsán v těchto článcích:

- [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md)
- [Hlášení a kontrola stavu služeb](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Přidání vlastních sestav stavu service fabric](service-fabric-report-health.md)
- [Zobrazit sestavy stavu service fabric](service-fabric-view-entities-aggregated-health.md)

Monitorování stavu je důležité pro více aspektů provozu služby, zejména během upgradu aplikace. Po upgradu každé upgradovací domény služby musí upgradovací doména před přesunem nasazení do další domény upgradu předat kontroly stavu. Pokud nelze dosáhnout stavu OK, nasazení je vrácena zpět, tak, aby aplikace zůstane ve známém stavu OK. Ačkoli někteří zákazníci mohou být ovlivněni před vrácením služeb zpět, většina zákazníků nebude mít problém. Také řešení dochází poměrně rychle, aniž by museli čekat na akci od lidského operátora. Čím více kontrol stavu, které jsou začleněny do vašeho kódu, tím odolnější je vaše služba vůči problémům s nasazením.

Dalším aspektem stavu služby je vykazování metrikze služby. Metriky jsou důležité v Service Fabric, protože se používají k vyrovnání využití prostředků. Metriky mohou být také ukazatelem stavu systému. Můžete mít například aplikaci, která má mnoho služeb a každá instance hlásí metriku požadavků za sekundu (RPS). Pokud jedna služba používá více prostředků než jiná služba, Service Fabric přesune instance služby kolem clusteru, aby se pokusili zachovat i využití prostředků. Podrobnější vysvětlení, jak funguje využití prostředků, naleznete v [tématu Správa spotřeby prostředků a zatížení v service fabric s metrikami](service-fabric-cluster-resource-manager-metrics.md).

Metriky vám také mohou pomoci získat přehled o tom, jak si vaše služba vede. V průběhu času můžete použít metriky ke kontrole, že služba pracuje v rámci očekávaných parametrů. Například pokud trendy ukazují, že v 9 hodin ráno v pondělí ráno průměrný RPS je 1 000, pak můžete nastavit zdravotní zprávu, která vás upozorní, pokud RPS je nižší než 500 nebo vyšší než 1 500. Všechno může být naprosto v pořádku, ale to by mohlo být stojí za to podívat se ujistit, že vaši zákazníci mají velký zážitek. Vaše služba může definovat sadu metrik, které mohou být hlášeny pro účely kontroly stavu, ale které nemají vliv na vyrovnávání prostředků clusteru. Chcete-li to provést, nastavte hodnotu metriky na nulu. Doporučujeme spustit všechny metriky s nulovou váhou a nezvyšovat váhu, dokud si nejste jisti, že pochopíte, jak vážení metrik ovlivňuje vyrovnávání prostředků pro váš cluster.

> [!TIP]
> Nepoužívejte příliš mnoho vážených metrik. Může být obtížné pochopit, proč instance služby jsou přesouvány pro vyrovnávání. Několik metrik může jít dlouhou cestu!

Všechny informace, které mohou označovat stav a výkon vaší aplikace, jsou kandidátem pro metriky a sestavy stavu. **Čítač výkonu procesoru vám může říct, jak je váš uzel využíván, ale neříká, zda je určitá služba v pořádku, protože na jednom uzlu může být spuštěno více služeb.** Metriky, jako je RPS, zpracované položky a latence požadavků, však mohou označovat stav konkrétní služby.

## <a name="service-fabric-support-logs"></a>Protokoly podpory service fabric

Pokud potřebujete kontaktovat podporu Microsoftu o pomoc s vaším clusterem Azure Service Fabric, protokoly podpory jsou téměř vždy vyžadovány. Pokud je váš cluster hostovaný v Azure, protokoly podpory se automaticky nakonfigurují a shromažďují jako součást vytváření clusteru. Protokoly jsou uloženy ve vyhrazeném účtu úložiště ve skupině prostředků vašeho clusteru. Účet úložiště nemá pevný název, ale v účtu se zobrazí kontejnery objektů blob a tabulky s názvy, které začínají *fabric*. Informace o nastavení kolekcí protokolů pro samostatný cluster naleznete v [tématu Vytvoření a správa samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) a [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md). Pro samostatné instance Service Fabric by měly být protokoly odeslány do místní sdílené složky. Tyto protokoly jsou **nutné** mít pro podporu, ale nejsou určeny k použití nikým mimo tým zákaznické podpory společnosti Microsoft.

## <a name="measuring-performance"></a>Měření výkonu

Měření výkonu vašeho clusteru vám pomůže pochopit, jak je schopen zpracovat načtení a řídit rozhodnutí kolem škálování clusteru (další informace o škálování clusteru [v Azure](service-fabric-cluster-scale-up-down.md)nebo [místně).](service-fabric-cluster-windows-server-add-remove-nodes.md) Údaje o výkonu jsou také užitečné ve srovnání s akcemi, které jste vy nebo vaše aplikace a služby mohli provést při analýze protokolů v budoucnu. 

Seznam čítačů výkonu, které se mají shromažďovat při použití service fabric, najdete [v tématu Čítače výkonu v service fabric](service-fabric-diagnostics-event-generation-perf.md)

Zde jsou dva běžné způsoby, jak můžete nastavit shromažďování dat o výkonu pro váš cluster:

* **Použití agenta**  
Toto je upřednostňovaný způsob shromažďování výkonu ze stroje, protože agenti mají obvykle seznam možných metrik výkonu, které lze shromažďovat, a je poměrně snadný proces výběru metrik, které chcete shromažďovat nebo měnit. Přečtěte si o Azure Monitor nabízí Azure Monitor protokoly v Service Fabric [Azure Monitor protokoly integrace](service-fabric-diagnostics-event-analysis-oms.md) a nastavení [agenta Log Analytics](../log-analytics/log-analytics-windows-agent.md) se dozvědět více o agenta Log Analytics, což je jeden takový agent monitorování, který je schopen vyzvednout data o výkonu pro virtuální počítače clusteru a nasazené kontejnery.

* **Čítače výkonu pro Azure Table Storage**  
Metriky výkonu můžete také odeslat do stejného úložiště tabulky jako události. To vyžaduje změnu konfigurace Diagnostika Azure vyzvednout příslušné čítače výkonu z virtuálních počítačů ve vašem clusteru a povolení k vyzvednutí docker statistiky, pokud budete nasazovat všechny kontejnery. Přečtěte si o konfiguraci [čítačů výkonu ve službě WAD](service-fabric-diagnostics-event-aggregation-wad.md) v service fabric pro nastavení kolekce čítačů výkonu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [integraci protokolů Azure Monitor služby Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) pro shromažďování diagnostiky clusteru a vytváření vlastních dotazů a výstrah
* Další informace o service fabric je v estavě diagnostické prostředí, [EventStore](service-fabric-diagnostics-eventstore.md)
* Projděte si některé [běžné diagnostické scénáře](service-fabric-diagnostics-common-scenarios.md) v service fabric
