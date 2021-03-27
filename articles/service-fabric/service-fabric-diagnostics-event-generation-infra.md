---
title: Monitorování úrovně platformy Azure Service Fabric
description: Přečtěte si o událostech a protokolech úrovně platformy, které slouží k monitorování a diagnostice clusterů Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626516"
---
# <a name="monitoring-the-cluster"></a>Monitorování clusteru

Je důležité monitorovat na úrovni clusteru a zjistit, jestli se váš hardware a cluster chová podle očekávání. I když Service Fabric může zachovat aplikace spuštěné během selhání hardwaru, ale stále potřebujete diagnostikovat, jestli k chybě dochází v aplikaci nebo v základní infrastruktuře. Měli byste také monitorovat clustery, abyste lépe naplánovali kapacitu, což pomáhá při rozhodování o přidání nebo odebrání hardwaru.

Service Fabric zpřístupňuje několik strukturovaných událostí platforem, jako jsou [Service Fabric události](service-fabric-diagnostics-events.md), prostřednictvím eventstoru a různých kanálů protokolu. 

V systému Windows jsou Service Fabric události k dispozici od jednoho poskytovatele ETW se sadou relevantních `logLevelKeywordFilters` , která se používá k výběru mezi provozními a datovými kanály datových &. to je způsob, jakým oddělíme odchozí Service Fabric události, které se podle potřeby mají filtrovat.

* **Provozní** Operace vysoké úrovně prováděné Service Fabric a clusterem, včetně událostí pro uzel, který se připravuje, nově nasazené aplikace nebo odvolání upgradu atd. Úplný seznam událostí najdete [tady](service-fabric-diagnostics-event-generation-operational.md).  

* **Funkční – podrobné**  
Sestavy o stavu a rozhodnutí o vyrovnávání zatížení.

K kanálu operace lze získat přístup několika různými způsoby, včetně protokolů událostí ETW/Windows, [eventstoru](service-fabric-diagnostics-eventstore.md) (k dispozici ve Windows v verzích 6,2 a novějších pro clustery Windows). Eventstoru poskytuje přístup k událostem clusteru na základě jednotlivých entit (entity včetně clusteru, uzlů, aplikací, služeb, oddílů, replik a kontejnerů) a zpřístupňují je prostřednictvím rozhraní REST API a Service Fabric klientské knihovny. Pomocí Eventstoru můžete monitorovat své clustery pro vývoj a testování a získat informace o stavu vašich produkčních clusterů v časovém bodě.

* **Data & zasílání zpráv**  
Kritické protokoly a události vygenerované v rámci zasílání zpráv (aktuálně jenom ReverseProxy) a cesta k datům (modely Reliable Services).

* **Data & zasílání zpráv – podrobná**  
Podrobný kanál, který obsahuje všechny nekritické protokoly z dat a zpráv v clusteru (Tento kanál má velmi velký objem událostí).

Kromě toho jsou k dispozici dva strukturované kanály EventSource a také protokoly, které shromažďujeme pro účely podpory.

* [Události Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Programování událostí konkrétního modelu.

* [Události Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Programové modely události a čítače výkonu.

* Protokoly podpory  
Systémové protokoly vygenerované Service Fabric, které se dají použít jenom při poskytování podpory.

Tyto různé kanály zahrnují většinu doporučeného protokolování na úrovni platformy. Pokud chcete zlepšit protokolování na úrovni platformy, zvažte možnost lépe pochopit model stavu a přidat vlastní sestavy o stavu a přidat vlastní **čítače výkonu** , abyste mohli sestavovat informace o dopadu služeb a aplikací na cluster v reálném čase.

Aby bylo možné využít tyto protokoly, důrazně doporučujeme ponechat při vytváření clusteru na webu Azure Portal povolenou možnost Diagnostika. Když zapnete diagnostiku a nasadíte cluster, Windows Azure Diagnostics je schopný potvrdit provozní kanály, Reliable Services a spolehlivé aktéry a ukládat data, jak je popsáno dále v tématu [agregace událostí s Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Vytváření sestav o stavu a načítání Azure Service Fabric

Service Fabric má svůj vlastní model stavu, který je podrobně popsán v těchto článcích:

- [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md)
- [Hlášení a kontrola stavu služeb](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Přidat vlastní sestavy o stavu Service Fabric](service-fabric-report-health.md)
- [Zobrazit Service Fabric sestavy o stavu](service-fabric-view-entities-aggregated-health.md)

Monitorování stavu je pro více aspektů provozu služby velmi důležité, zejména během upgradu aplikace. Po upgradu každé upgradované domény služby musí doména upgradu před přechodem na další upgradovací doménu projít kontroly stavu. Pokud nelze dosáhnout stavu OK, nasazení je vráceno zpět, aby aplikace zůstala ve známém stavu OK. I když někteří zákazníci mohou být ovlivněni, než se služby vrátí zpět, většina zákazníků nebude mít potíže. Řešení je také relativně rychlé bez nutnosti čekat na akci od lidského operátora. Další kontroly stavu, které jsou začleněny do kódu, jsou pružnější, že vaše služba představuje problémy s nasazením.

Dalším aspektem stavu služby je hlášení metrik ze služby. Metriky jsou v Service Fabric důležité, protože se používají k vyrovnávání využití prostředků. Metriky mohou být také indikátorem stavu systému. Můžete mít například aplikaci, která má mnoho služeb a každá instance hlásí metriku požadavků za sekundu (RPS). Pokud jedna služba používá více prostředků než jiná služba, Service Fabric přesune instance služby kolem clusteru a pokusí se zachovat ještě využití prostředků. Podrobnější vysvětlení toho, jak využití prostředků funguje, najdete v tématu [Správa spotřeby prostředků a načítání v Service Fabric s využitím metrik](service-fabric-cluster-resource-manager-metrics.md).

Metriky vám také pomůžou poskytnout přehled o tom, jak vaše služba provádí. V průběhu času můžete metriky použít ke kontrole, jestli služba funguje v rámci očekávaných parametrů. Pokud se například v trendech ukáže, že v pondělí ráno dopoledne průměrné RPS je 1 000, můžete nastavit sestavu stavu, která vás upozorní, pokud je RPS pod 500 nebo vyšší než 1 500. Vše může být dokonale jemné, ale může se stát, že budete mít jistotu, že vaši zákazníci mají Skvělé prostředí. Vaše služba může definovat sadu metrik, které mohou být hlášeny pro účely kontroly stavu, ale které neovlivňují vyrovnávání prostředků clusteru. To provedete tak, že nastavíte váhu metriky na hodnotu nula. Doporučujeme, abyste spustili všechny metriky s váhou nula a nezvětšili váhu, dokud si nejste jistí, jak tyto metriky ovlivňují vyrovnávání zatížení pro váš cluster.

> [!TIP]
> Nepoužívejte příliš mnoho vážených metrik. Může být obtížné pochopit, proč se instance služby pohybují pro vyrovnávání. Několik metrik může jít dlouhou dobu.

Všechny informace, které mohou indikovat stav a výkon vaší aplikace, jsou kandidátem na metriky a sestavy o stavu. **Čítač výkonu procesoru vám může sdělit, jak se váš uzel využívá, ale nedává vám vědět, jestli je konkrétní služba v pořádku, protože na jednom uzlu může běžet víc služeb.** Ale metriky, jako jsou RPS, zpracovávané položky a latence žádosti, můžou indikovat stav konkrétní služby.

## <a name="service-fabric-support-logs"></a>Protokoly podpory Service Fabric

Pokud potřebujete požádat o pomoc s clusterem Azure Service Fabric, obraťte se na podporu. protokoly podpory se téměř vždy vyžadují. Pokud je váš cluster hostovaný v Azure, protokoly podpory se automaticky konfigurují a shromažďují v rámci vytváření clusteru. Protokoly se ukládají do vyhrazeného účtu úložiště ve skupině prostředků vašeho clusteru. Účet úložiště nemá pevný název, ale v účtu se zobrazí kontejnery a tabulky objektů BLOB s názvy, které začínají na *prostředky infrastruktury*. Informace o nastavení kolekcí protokolů pro samostatný cluster najdete v tématu [Vytvoření a Správa samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) a [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md). Pro samostatné instance Service Fabric se protokoly mají odeslat do místní sdílené složky. Je **nutné** mít tyto protokoly k dispozici pro podporu, ale nejsou určeny pro všechny uživatele mimo tým zákaznické podpory společnosti Microsoft.

## <a name="measuring-performance"></a>Měření výkonu

Měření výkonu clusteru vám pomůže pochopit, jak je možné zvládnout zatížení a měřit rozhodnutí v souvislosti s škálováním clusteru (Další informace o škálování clusteru [v Azure](service-fabric-cluster-scale-in-out.md)nebo [v](service-fabric-cluster-windows-server-add-remove-nodes.md)místním prostředí). Údaje o výkonu jsou také užitečné při porovnání s akcemi, které jste si vyučinili, nebo při analýze protokolů v budoucnu. 

Seznam čítačů výkonu, které se mají shromáždit při použití Service Fabric, najdete [v tématu čítače výkonu v Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Tady jsou dva běžné způsoby, jak můžete nastavit shromažďování dat o výkonu pro svůj cluster:

* **Použití agenta**  
Jedná se o preferovaný způsob shromažďování výkonu z počítače, protože agenti mají obvykle seznam možných metrik výkonu, které se dají shromáždit, a je poměrně jednoduchý proces výběru metrik, které chcete shromažďovat nebo měnit. Přečtěte si o Azure Monitor, které nabízí Azure Monitor protokoluje v Service Fabric [Azure monitor protokoluje integraci](service-fabric-diagnostics-event-analysis-oms.md) a [nastavuje agenta pro Log Analytics](../azure-monitor/agents/agent-windows.md) , kde se dozvíte víc o agentovi Log Analytics, který je jedním z takových agentů monitorování, který dokáže vystavovat data výkonu pro virtuální počítače clusteru a nasazené kontejnery.

* **Čítače výkonu do Azure Table Storage**  
Metriky výkonu můžete také odesílat do stejného úložiště tabulek jako události. To vyžaduje změnu konfigurace Azure Diagnostics, aby se braly odpovídající čítače výkonu z virtuálních počítačů ve vašem clusteru, a pokud budete nasazovat nějaké kontejnery, umožníte jim vybrat si statistiku Docker. Přečtěte si o konfiguraci [čítačů výkonu v WAD](service-fabric-diagnostics-event-aggregation-wad.md) v tématu Service Fabric k nastavení kolekce čítačů výkonu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [integraci protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) Service Fabric ke shromažďování diagnostiky clusteru a vytváření vlastních dotazů a upozornění.
* Service Fabric Seznamte se s [eventstoru](service-fabric-diagnostics-eventstore.md) v integrovaném diagnostickém prostředí.
* Projděte si některé [běžné diagnostické scénáře](service-fabric-diagnostics-common-scenarios.md) v Service Fabric
