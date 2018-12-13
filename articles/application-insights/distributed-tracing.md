---
title: Distribuované trasování ve službě Azure Application Insights | Dokumentace Microsoftu
description: Poskytuje informace o podpoře společnosti Microsoft pro distribuované trasování prostřednictvím našich místní server pro předávání a partnerství se společností OpenCensus projektu
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aa163d215f4b9c396585dbba63883a3d1beff457
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164258"
---
# <a name="what-is-distributed-tracing"></a>Co je distribuované trasování?

Nástupu moderních cloudových a [mikroslužeb](http://azure.com/microservices) architektury vyvolalo jednoduchého, samostatně nasaditelných služeb, které pomáhají snižovat náklady a současně zvýšit dostupnost a propustnost. Ale Přestože tyto pohybů plb typu jsme usnadnili individuálních služeb, a pochopit jako celek, tuším celkové systémy obtížnější odůvodnitelný a ladění.

V monolitické architektury jste získali jsme použili až po ladění zásobníky volání. Zásobníky volání jsou vynikající nástroje pro zobrazení toku provádění (metoda volaná metoda B, který volá metodu C), spolu s detaily a parametry, které o každé z těchto volání. To je skvělé pro monolitické nebo služby spuštěné v jednom procesu, ale jak kdy budeme ladit je hranice procesu, nikoli pouze odkaz na místní zásobník volání? 

Který přichází distribuované trasování.  

Distribuované trasování je ekvivalentem zásobníky volání pro moderní architektury mikroslužeb a cloudu, a uveďte vyvolány v profileru zjednodušenou výkonu. Ve službě Azure Monitor zajišťuje dvě prostředí určená pro data distribuované trasování. První je naše [Diagnostika transakcí](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) zobrazení, které se podobá včteně zásobník volání s časovou dimenzi. Zobrazení diagnostiky transakce poskytuje vhled do jedné transakce/žádosti a je užitečné při hledání původní příčinu problémy se spolehlivosti a kritické body výkonu na základě žádosti.

Azure Monitor nabízí také [Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) zobrazení, která agreguje velký počet transakcí zobrazíte topologické zobrazení interakci v systémech a jaké jsou průměrný výkon a chybovost. 

## <a name="how-to-enable-distributed-tracing"></a>Jak povolit distribuované trasování

Povolení trasování distribuované napříč službami v aplikaci je stejně jednoduché jako přidání správné sady SDK nebo knihovny pro všechny služby založené na jazyce, který byl implementován v.

## <a name="enabling-via-application-insights-sdks"></a>Povolení prostřednictvím sady SDK služby Application Insights

Sady Application Insights SDK pro .NET, .NET Core, Javy, Node.js a JavaScript podporují distribuované trasování nativně. Pokyny pro instalaci a konfiguraci jednotlivých Application Insights SDK jsou k dispozici níže:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

S správné Application Insights SDK nainstalovanou a nakonfigurovanou informace trasování se automaticky shromažďují, aby oblíbených architektur, knihoven a technologií podle závislostí sady SDK automaticky – kolekce. Úplný seznam podporovaných technologií je k dispozici v [dokumentaci automatického sběru závislost](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Kromě toho můžete všechny technologie sledovat ručně pomocí volání [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Povolit prostřednictvím OpenCensus

Kromě sady Application Insights SDK Application Insights podporuje také distribuované trasování prostřednictvím [OpenCensus](https://opencensus.io/). OpenCensus je opensourcový, nezávislá na dodavateli jednotné distribuce knihoven kvůli shromažďování metrik a distribuované trasování pro služby. Umožňuje také opensourcová komunita, která umožňuje distribuované trasování pomocí oblíbených technologií, jako jsou Redis Memcached nebo MongoDB. [Microsoft spolupracuje na OpenCensus s několika dalšími partnery monitorování a cloudu](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Pro přidání možností distribuované trasování do aplikace s OpenCensus, nejprve [nainstalujte a nakonfigurujte předávání místní Application Insights](./opencensus-local-forwarder.md). Tady nakonfigurujte OpenCensus směrovat data distribuované trasování prostřednictvím místního serveru pro předávání. Obě [Python](./opencensus-python.md) a [Přejít](./opencensus-go.md) jsou podporovány.

Web OpenCensus udržuje referenční dokumentace rozhraní API pro [Python](https://opencensus.io/api/python/trace/usage.html) a [Přejít](https://godoc.org/go.opencensus.io), stejně jako různé jiné pokyny pro používání OpenCensus. 

## <a name="next-steps"></a>Další postup

* [Použití Průvodce OpenCensus Pythonu](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikace](./app-insights-app-map.md)
* [Sledování výkonu začátku do konce](./app-insights-tutorial-performance.md)
