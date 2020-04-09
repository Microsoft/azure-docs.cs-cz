---
title: Distribuované trasování v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Poskytuje informace o podpoře společnosti Microsoft pro distribuované trasování prostřednictvím našeho partnerství v projektu OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 83575aa7f9b63615f453e00bd06b00a5540b9a9e
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892253"
---
# <a name="what-is-distributed-tracing"></a>Co je distribuované trasování?

Příchod moderních cloudových architektur a architektur [mikroslužeb](https://azure.com/microservices) dal vzniknout jednoduchým, nezávisle nasaditelným službám, které můžou snížit náklady a zároveň zvýšit dostupnost a propustnost. Ale zatímco tyto pohyby učinily jednotlivé služby srozumitelnější jako celek, ztížily celkové systémy a ztížily o tom a ladění.

V monolitických architekturách jsme si zvykli na ladění pomocí zásobníků volání. Zásobníky volání jsou brilantní nástroje pro zobrazení toku provádění (metoda A s názvem Metoda B, která se nazývá metoda C), spolu s podrobnostmi a parametry o každém z těchto volání. To je skvělé pro monolity nebo služby spuštěné v jednom procesu, ale jak ladíme, když je volání přes hranice procesu, nikoli pouze odkaz na místní zásobník? 

To je místo, kde distribuované trasování přijde.  

Distribuované trasování je ekvivalentní zásobníky volání pro moderní architektury cloudu a mikroslužeb s přidáním zjednodušující profilování výkonu vyvolána palců Ve službě Azure Monitor poskytujeme dvě prostředí pro využití distribuovaných dat trasování. Prvním z nich je naše zobrazení [diagnostiky transakcí,](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) které je jako zásobník volání s přidanou časovou dimenzí. Zobrazení diagnostiky transakcí poskytuje přehled o jedné transakci/požadavku a je užitečné při hledání hlavní příčiny problémů se spolehlivostí a kritických míst výkonu na základě požadavku.

Azure Monitor také nabízí zobrazení [mapy aplikace,](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) které agreguje mnoho transakcí, aby se zobrazilo topologické zobrazení interakce systémů a jaká je průměrná míra výkonu a chyb. 

## <a name="how-to-enable-distributed-tracing"></a>Jak povolit distribuované trasování

Povolení distribuovaného trasování napříč službami v aplikaci je stejně jednoduché jako přidání správného agenta, sady SDK nebo knihovny do každé služby na základě jazyka, ve které byla služba implementována.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Povolení prostřednictvím application insights prostřednictvím automatické instrumentace nebo sad SDK

Agenti Application Insights a/nebo Sady SDK pro .NET, .NET Core, Java, Node.js a JavaScript podporují nativně distribuované trasování. Pokyny pro instalaci a konfiguraci jednotlivých sad Application Insights SDK jsou k dispozici níže:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Při instalaci a konfiguraci správné sady Application Insights SDK jsou informace o trasování automaticky shromažďovány pro oblíbené architektury, knihovny a technologie pomocí automatických kolektorů závislostí sady SDK. Úplný seznam podporovaných technologií je k dispozici v [dokumentaci k automatickému shromažďování závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Kromě toho všechny technologie lze sledovat ručně s voláním [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Povolit prostřednictvím opencensus

Kromě sad SDK Application Insights podporuje Application Insights také distribuované trasování prostřednictvím [OpenCensus](https://opencensus.io/). OpenCensus je open source, dodavatel-agnostik, jediné distribuce knihoven poskytovat shromažďování metrik a distribuované trasování pro služby. Umožňuje také komunitě s otevřeným zdrojovým kódem povolit distribuované trasování s populárními technologiemi, jako je Redis, Memcached nebo MongoDB. [Microsoft spolupracuje na OpenCensus s několika dalšími partnery pro monitorování a cloud](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

OpenCensus webové stránky udržuje API referenční dokumentaci pro [Python](https://opencensus.io/api/python/trace/usage.html) a [Go](https://godoc.org/go.opencensus.io), stejně jako různé návody pro použití OpenCensus. 

## <a name="next-steps"></a>Další kroky

* [Průvodce použitím Pythonu OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Komplexní sledování výkonu](./../../azure-monitor/learn/tutorial-performance.md)
