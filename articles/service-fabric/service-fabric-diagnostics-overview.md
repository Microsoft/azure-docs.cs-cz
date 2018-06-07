---
title: Přehled diagnostiky a Azure Service Fabric monitorování | Microsoft Docs
description: Další informace o monitorování a Diagnostika pro clustery, aplikace a služby Azure Service Fabric.
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
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: f7fe07500f877cf34626e53361c9c68dd459a5e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643171"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorovací a diagnostické pro Azure Service Fabric

Tento článek obsahuje přehled monitorování a Diagnostika pro Azure Service Fabric. Monitorovací a diagnostické jsou důležité k vývoji, testování a nasazení úloh v jakémkoli prostředí cloudu. Monitorování umožňuje sledovat, jak se aplikace používají, vaše využití prostředků a celkový stav clusteru. Můžete tyto informace slouží k diagnostikování a opravte všechny problémy a zabránit problémům v budoucnu. 

## <a name="application-monitoring"></a>Monitorování aplikací
Monitorování aplikací sleduje, jak jsou používány funkce a součásti vaší aplikace. Chcete monitorovat aplikací k problémům se, že dopad, který uživatelé jsou zachyceny. Monitorování aplikace může být užitečné v následujících scénářích:
* určení správné zatížení aplikacemi a uživatel přenosy dat – potřebujete škálovat vaše služby, aby splnily požadavky na uživatele nebo IP adresa potenciální problémová místa ve vaší aplikaci?
* Identifikace problémy s komunikace služby a vzdálenou komunikaci mezi clusteru
* Nad tím, co uživatelé dělají s vaší aplikací – shromažďování telemetrických dat ve svých aplikacích může pomoci průvodce budoucí funkce vývoj a lepší diagnostiku pro chyby aplikace
* Monitorování, co se děje uvnitř spuštěných kontejnerů

Service Fabric podporuje mnoho možností pro instrumentace kódu aplikace s správné trasování a telemetrií. Doporučujeme vám, že používáte Application Insights (AI). AI na integraci s Service Fabric zahrnuje tooling prostředí pro portál Visual Studio a Azure, stejně jako určité metriky Service Fabric, poskytnutí prostředí komplexní protokolování se na pole. Když mnoho protokoly jsou automaticky vytvořen a shromážděné pro vás pomocí AI, doporučujeme vám, že přidáte další vlastní protokolování pro vaše aplikace k vytvoření bohatší možnosti diagnostiky. Viz informace o seznámení se službou Application Insights s Service Fabric v [analýza události s Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Monitorování platformy (Cluster)
Monitorování cluster Service Fabric je důležité zajistit, že platformy a všechny úlohy běží tak, jak má. Jedním z cílů Service Fabric je udržovat aplikace odolné vůči selhání hardwaru. Tento cíl je dosaženo pomocí služeb systému platformy možnost ke zjišťování problémů s infrastrukturou a rychle převzetí služeb při selhání úlohy na jiné uzly v clusteru. Ale v tomto případě, co v případě systému služeb sami mají problémy? Nebo pokud při pokusu o přesunutí zatížení, jsou pravidla pro umístění služby došlo k porušení? Monitorování clusteru umožňuje udržení informovanosti o aktivity probíhající v clusteru, což pomáhá s diagnostikou problémy a jejich oprava efektivně. Některé klíče, který má být hledání věci:
* Způsob, jakým očekáváte, z hlediska umístění vašich aplikací a vyrovnávání práce mimo cluster chová Service Fabric? 
* Jsou provedeny uživatele akce v clusteru potvrzeny a provést u podle očekávání? To je obzvláště důležité, když škálování clusteru.
* Service Fabric zpracovává vaše data a vaše služba služba komunikace uvnitř clusteru správně?

Service Fabric nabízí komplexní sadu událostí mimo pole. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) je přístupný prostřednictvím rozhraní API EventStore nebo provozní kanál (kanál událostí vystavené platformu). 
* EventStore - EventStore (k dispozici v systému Windows verze 6.2 nebo novější, Linux stále probíhá od datum poslední aktualizace v tomto článku), zpřístupní tyto události prostřednictvím sada rozhraní API (dostupné prostřednictvím koncové body REST nebo prostřednictvím klientské knihovny). Další informace o EventStore na [EventStore přehled](service-fabric-diagnostics-eventstore.md).
* Nejsou k dispozici z jednoho zprostředkovatele trasování událostí pro Windows se sadou relevantní Service Fabric události kanálů - na Windows Service Fabric události `logLevelKeywordFilters` umožňuje výběr mezi dat & zasílání zpráv a Operational kanály – to je způsob, ve kterém jsme oddělit odchozí Služba Fabric události filtrovanou na podle potřeby. V systému Linux Service Fabric události pocházet prostřednictvím LTTng a jsou vloženy do jedné tabulky úložiště, ze kterých mohou být filtrovány podle potřeby. Tyto kanály obsahovat kurátorované, strukturovaných události, které lze použít pro lepší pochopení stav clusteru. Diagnostika jsou povolené ve výchozím nastavení v okamžiku vytvoření clusteru, které vytvoření tabulky Azure Storage níž jsou odesílány události z těchto kanálů pro vás k dotazování v budoucnu. 

Doporučujeme pomocí EventStore pro rychlé analýzy a získat snímku představu o tom, jak váš cluster pracuje a pokud událostem dochází jako očekává. Pro shromažďování protokolů a událostí generovaných cluster, obvykle doporučujeme používat [rozšíření Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). To se integruje s Service Fabric analýzy, analýzy protokolů OMS Service Fabric specifického řešení, které poskytuje vlastní řídicí panel pro monitorování clusterů Service Fabric a umožňuje dotazování události vašeho clusteru a nastavit výstrahy. Další informace o to v [analýza události s OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 Si můžete přečíst informace o monitorování clusteru na [platformy úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Sledování výkonu
Monitorování infrastruktury základní je klíčovou součástí pochopení stav clusteru a vaše využití prostředků. Měření výkonu systému, závisí na několika faktorech, z nichž každý se většinou měří prostřednictvím klíčových ukazatelů výkonu (KPI). Service Fabric relevantní klíčových ukazatelů výkonu lze mapovat na metriky, které mohou být shromažďovány z uzlů v clusteru, jako čítače výkonu.
Těchto klíčových ukazatelů výkonu můžou pomoct se:
* Pochopení využití prostředků a zatížení - za účelem škálování clusteru nebo optimalizací procesů služby.
* Predikci problémů s infrastrukturou - mnohé problémy s předchází nečekané změny (vyřazuje) ve výkonu, abyste mohli používat klíčové ukazatele výkonu například využití vstupně-výstupních operací a procesoru sítě předpovědi a diagnostikovat problémy infrastruktury.

Seznam čítačů výkonu, které by měly být shromažďovány na úrovni infrastruktury najdete na [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric poskytuje sadu čítačů výkonu pro programovací modely Reliable Services a aktéři. Pokud používáte některou z těchto modelů, tyto čítače výkonu pro klíčové ukazatele výkonu, které pomáhají zajistit, že jsou vaše aktéři otáčí nahoru a dolů správně nebo že žádostí o služby spolehlivé dochází ke zpracování dostatečně rychle. Další informace najdete v tématu [monitorování pro vzdálenou komunikaci spolehlivá služba](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) a [monitorování výkonu Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Kromě toho Application Insights také obsahuje sadu metrik výkonu, které se budou shromažďovat, pokud je nakonfigurovaná s vaší aplikací.

Použití [agenta OMS](service-fabric-diagnostics-oms-agent.md) ke shromažďování čítačů výkonu příslušné a zobrazení těchto klíčových ukazatelů výkonu v OMS Log Analytics.

![Graf Přehled diagnostiky](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric obsahuje stav modelu, který poskytuje rozšiřitelný vykazování stavu pro stav entity v clusteru. Každý uzel, aplikace, služby, oddíl, repliky nebo instance, má průběžně aktualizovat stav. Stav může být buď "OK", "Upozornění" nebo "Chyba". Stav se změní prostřednictvím sestav stavu, které jsou vygenerované pro každou entitu podle problémy v clusteru. Stav entity můžete kdykoli v Service Fabric Explorer (SFX) kontrolovat, jak je uvedeno níže, nebo může být dotazován prostřednictvím rozhraní API stavu na platformách. Můžete také přizpůsobit sestavy o stavu a upravit stav entity přidáním vlastních sestav stavu nebo pomocí rozhraní API služby stavu. Další informace o stavu modelu najdete na [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md).

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Kromě zobrazení nejnovější sestavy stavu v SFX, je také jednotlivých sestav k dispozici jako událost. Stav události se můžou shromažďovat prostřednictvím provozní kanálu (najdete v části [agregace událostí pomocí diagnostiky Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) a jsou uložené v analýzy protokolů pro výstrahy a dotazování v budoucnu. To pomáhá rozpoznat problémy, které můžou mít vliv dostupnosti vaší aplikace, takže doporučujeme, abyste nastavili výstrahy pro příslušné selhání scénáře (vlastní výstrahy prostřednictvím analýzy protokolů).

## <a name="other-logging-solutions"></a>Jiných řešení pro protokolování

I když obě řešení je doporučeno, [OMS](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) jste vytvořili v integraci s Service Fabric, mnoho událostí se zapisují prostřednictvím zprostředkovatelé trasování událostí pro Windows a jsou extensible s jinými protokolování řešení. Taky by měl vypadat do [elastické zásobníku](https://www.elastic.co/products) (obzvláště pokud uvažujete o spuštění clusteru v režimu offline), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), nebo jakékoli jiné Platforma vaši volbu. 

Klíčové body pro libovolnou platformu, že si vyberete by měla obsahovat jak pohodlné jste s uživatelským rozhraním a dotazování umožňuje vizualizovat data a vytvářet snadno čitelné řídicí panely a dalších nástrojů poskytuje k vylepšení monitorování, možnosti například automatizované výstrahy.

## <a name="next-steps"></a>Další postup

* Začínáme s instrumentace aplikací, najdete v části [aplikace úrovni událostí a protokolu generování](service-fabric-diagnostics-event-generation-app.md).
* Další informace o monitorování platformy a události Service Fabric nabízí pro vás na [platformy úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-infra.md).
* Projděte postup pro nastavení AI pro vaši aplikaci s [monitorování a diagnostikovat v Service Fabric aplikace ASP.NET Core](service-fabric-tutorial-monitoring-aspnet.md).
* Zjistěte, jak nastavit OMS analýzy protokolů pro monitorování kontejnery- [monitorování a Diagnostika pro Windows kontejnerů v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Další informace o obecné monitorování doporučení pro prostředky Azure - [osvědčené postupy – monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
