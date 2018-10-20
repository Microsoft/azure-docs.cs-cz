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
ms.date: 10/18/2018
ms.author: srrengar
ms.openlocfilehash: 5fc2674a145be99fb8867c5cf1b1f65ba860db80
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457829"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorování a diagnostiky pro Azure Service Fabric

Tento článek obsahuje přehled monitorování a diagnostiky pro Azure Service Fabric. Monitorování a Diagnostika jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli prostředí cloud. Monitorování umožňuje sledovat využití aplikací, využití prostředků a celkový stav clusteru. Můžete tyto informace slouží k Diagnostikujte a opravte všechny problémy a zabránit problémům v budoucnu. 

## <a name="application-monitoring"></a>Monitorování aplikací
Monitorování aplikací, sleduje používání funkcí a komponent vaší aplikace. Budete chtít monitorovat Ujistěte se, že problémy tohoto dopadu, které jsou zachyceny uživatelé vaší aplikace. Monitorování aplikací může být užitečné v následujících scénářích:
* Určení načítání aplikace a uživatele provozu – potřebujete škálovat služby na splnili nároky uživatelů nebo IP adresa potenciální problémová místa ve vaší aplikaci?
* Identifikovat případné potíže s komunikace služby a vzdálené komunikace v clusteru
* Tím, co uživatelé dělají s vaší aplikací – shromažďování telemetrických dat do vašich aplikací může pomoci řídit vývoj funkcí budoucí průvodce a lepší diagnostiku pro aplikaci chyby
* Sledování, co se děje ve spuštěné kontejnery

Service Fabric podporuje mnoho možností k instrumentaci kódu aplikace pomocí správné trasování a telemetrie. Doporučujeme používat Application Insights (AI). Integrace pro AI s využitím Service Fabric zahrnuje nástroje prostředí pro Visual Studio a Azure portal, stejně jako konkrétní metriky Service Fabric, zajistit prostředí s komplexní out-of-the-box protokolování. Přestože mnoho protokoly se automaticky vytváří a shromážděných za vás s využitím AI, doporučujeme přidat další vlastní protokolování do vašich aplikací pro vytvoření pohodlnější a pestřejší prostředí diagnostiky. Další informace o zahájení práce s Application Insights s využitím Service Fabric v [analýza událostí s využitím Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitorování platformy (Cluster)
Monitorování clusteru Service Fabric je důležité zajistit, že všechny úlohy a platformy běží tak, jak má. Jedním z cílů Service Fabric je udržovat aplikace odolné vůči selhání hardwaru. Tento cíl se dosahuje prostřednictvím možnosti platformy systémových služeb ke zjištění problémů s infrastrukturou a rychlé převzetí služeb při selhání úlohy jiným uzlům v clusteru. Ale v tomto konkrétním případě co když vlastních služeb systému mají problémy? Nebo pokud při pokusu o přesunutí úloh, jsou pravidla pro umístění služby došlo k porušení? Monitorování clusteru umožňuje Udržujte si přehled o probíhající ve vašem clusteru, která pomáhá při diagnostikování problémů a jejich implementace vyžadovala efektivně aktivity. Několik důležitých věcí, které chcete hledat jsou:
* Service Fabric chová tak, jak jste očekávali, co se týče uvedení vaší aplikace a vyrovnávání alternativní clusteru? 
* Pocházejí akce uživatelů ve vašem clusteru potvrzení a spouštět v podle očekávání? To je obzvláště důležité při škálování clusteru.
* Service Fabric zpracovává vaše data a vaše služba služba komunikace uvnitř clusteru správně?

Service Fabric nabízí komplexní sadu událostí úprav. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) přístupné prostřednictvím rozhraní API Eventstoru nebo provozní kanál (událost kanálu vystavené platformu). 
* Eventstoru - Eventstoru (k dispozici na Windows verze 6.2 a novější, Linux i v průběhu od datum poslední aktualizace v tomto článku), poskytuje tyto události prostřednictvím sady rozhraní API (přístupná prostřednictvím koncové body REST nebo Klientská knihovna). Další informace o Eventstoru na [přehled Eventstoru](service-fabric-diagnostics-eventstore.md).
* Service Fabric události kanálů – Windows na Service Fabric jsou k dispozici z jednoho zprostředkovatele trasování událostí pro Windows se sadou relevantní `logLevelKeywordFilters` použijí k výběru mezi provozní Data a zasílání zpráv v kanálech – Toto je způsob, ve kterém jsme oddělit odchozí Service Fabric události filtrovanou na podle potřeby. Události Service Fabric v Linuxu, procházejí LTTng a jsou vloženy do jedné tabulky úložiště, ze které se dají se filtrovat podle potřeby. Tyto kanály obsahovat kurátorované, strukturované události, které lze použít pro lepší pochopení stavu vašeho clusteru. Je povolená Diagnostika ve výchozím nastavení při vytváření clusteru, které vytvářejí tabulce Azure Storage se odešle události z těchto kanálů pro vás k dotazování v budoucnu. 

Doporučujeme pomocí Eventstoru pro rychlou analýzu a získat snímek představu, jak provozování clusteru a pokud se dějí věci jako očekává. Pro shromažďování protokolů a událostí generovaných ve vašem clusteru, obecně doporučujeme používat [rozšíření Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). To také integrace s analýza služby Service Fabric, Log Analytics Service Fabric konkrétní řešení, která poskytuje vlastní řídicí panel pro sledování clusterů Service Fabric a umožňuje událostí vašeho clusteru a nastavte si upozornění. Další informace najdete v [analýza událostí s využitím Log Analytics](service-fabric-diagnostics-event-analysis-oms.md). 

 Další informace o monitorování clusteru na [generování úrovně událostí a protokolu platformy](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Sledování výkonu
Monitorování základní infrastruktury je klíčovou součástí pochopení stavu vašeho clusteru a využití prostředků. Měření výkonu systému, závisí na několika faktorech, z nichž každý se většinou měří prostřednictvím klíčových ukazatelů výkonu (KPI). Service Fabric relevantní klíčové ukazatele výkonu lze mapovat na metriky, které mohou být shromažďovány z uzlů v clusteru, jako jsou čítače výkonu.
Těchto klíčových ukazatelů výkonu můžou pomoct s:
* Vysvětlení využití prostředků a načtení – pro účely škálování clusteru nebo optimalizace procesů vaší služby.
* Předpověď problémů s infrastrukturou – mnoho problémů předchází (drops) i s náhlými změny ve výkonu, tak klíčové ukazatele výkonu můžete použít například vstupně-výstupních operací a procesoru využití sítě předpovídat a diagnostikovat problémy s infrastrukturou.

Seznam čítačů výkonu, které by měly být shromažďovány na úrovni infrastruktury najdete v [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric poskytuje sady čítačů výkonu pro programovací modely Reliable Services a objekty actor. Pokud používáte některý z těchto modelů, můžete tyto čítače výkonu poskytují klíčové ukazatele výkonu, které pomáhají zajistit, že jsou vaše actors zprovozňování navýšení nebo snížení kapacity správně, nebo že žádostí o spolehlivé služby jsou zpracovávanou dostatečně rychle. Další informace najdete v tématu [monitorování pro vzdálenou komunikaci Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) a [sledování výkonu služby Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Kromě toho Application Insights také obsahuje sadu metriky výkonu, který bude shromažďovat, pokud je nakonfigurovaný s vaší aplikace.

Použití [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) ke shromažďování čítačů výkonu odpovídající a zobrazení těchto klíčových ukazatelů výkonu ve službě Azure Log Analytics.

![Graf Přehled diagnostiky](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric zahrnuje stav modelu, který poskytuje rozšiřitelné zdraví, vytváření sestav pro stav entity v clusteru. Každý uzel, aplikace, služby, oddílu, replik nebo instancí, se průběžně aktualizovat stav. Stav může být buď "OK", "Varování" nebo "Chyba". Stav se změní prostřednictvím sestav o stavu, které jsou emitovány pro každou entitu, založené na problémy v clusteru. Stav entity můžete kdykoli v Service Fabric Exploreru (SFX) kontrolovat, jak je znázorněno níže, nebo může být dotázán pomocí rozhraní API stavu na platformách. Můžete také přizpůsobit sestav o stavu a měnit stav entity, a to přidáním vlastních sestav o stavu nebo pomocí rozhraní API stavu. Další informace o modelu health najdete v [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md).

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Kromě toho nejnovější sestavy stavu v SFX, každá sestava dostupný také jako událost. Stav události se můžou shromažďovat prostřednictvím provozní kanál (naleznete v tématu [agregace událostí s využitím Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) a uložené ve službě Log Analytics pro výstrahy a dotazování v budoucnu. To pomůže zjistit potíže, které mohou ovlivnit dostupnost vaší aplikace, proto doporučujeme, abyste nastavili upozornění na selhání vhodné scénáře (vlastní výstrahy prostřednictvím Log Analytics).

## <a name="other-logging-solutions"></a>Jiná řešení protokolování

I když obě řešení je vhodné, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) vytvořili mnoho událostí jsou zapsané pomocí zprostředkovatelů trasování událostí pro Windows a jsou v integraci s platformou Service Fabric Rozšiřitelné s jinými řešeními pro protokolování. Také byste se podívat do [řešení Elastic Stack](https://www.elastic.co/products) (zejména pokud uvažujete o spuštění clusteru v prostředí offline), [Dynatrace](https://www.dynatrace.com/), nebo jakékoli jiné platformě dáváte přednost. Máme seznam integrovaných partnerů, které jsou k dispozici [tady](service-fabric-diagnostics-partners.md).

Klíčové body pro jakoukoli platformu, že kterou si zvolíte by měl obsahovat jak pohodlné se pomocí uživatelského rozhraní a dotazování na ně možnosti, umožňuje vizualizovat data a vytvářet snadno čitelný řídicí panely a další nástroje poskytují k vylepšení monitorování, například automatické upozorňování.

## <a name="next-steps"></a>Další postup

* Začínáme s instrumentace aplikací, najdete v části [aplikace úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-app.md).
* Další informace o monitorování na platformu a události Service Fabric nabízí za vás na [generování úrovně událostí a protokolu platformy](service-fabric-diagnostics-event-generation-infra.md).
* Projděte si postup pro nastavení AI pro vaši aplikaci s [monitorování a Diagnostika aplikace ASP.NET Core v Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Zjistěte, jak nastavit službu OMS Log Analytics pro monitorování kontejnerů- [monitorování a Diagnostika pro Windows kontejnery v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Viz příklad diagnostiky problémů a řešení s využitím Service Fabric v [Diagnostika běžné scénáře](service-fabric-diagnostics-common-scenarios.md)
* Podívejte se na další diagnostiky produkty, které se integrují s využitím Service Fabric v [diagnostických partneři Service Fabric](service-fabric-diagnostics-partners.md)
* Další informace o obecných doporučení pro monitorování pro prostředky Azure – [osvědčené postupy – monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
