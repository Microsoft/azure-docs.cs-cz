---
title: Azure Service Fabric aplikace úroveň monitorování | Dokumentace Microsoftu
description: Další informace o aplikaci a událostí na úrovni služby a protokoly pro monitorování a Diagnostika clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 532ad8dfeff9a14d3d1533669149e1e374ed4460
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283225"
---
# <a name="application-and-service-level-logging"></a>Protokolování úrovně služby a aplikace

Kód instrumentace je základem pro většina jiných aspektů monitorování vašich služeb. Instrumentace je jediný způsob, jak můžete vědět, že se něco stalo a diagnostikovat, co je potřeba opravit. I když je technicky možné se připojit ladicí program k služby v produkčním prostředí, není běžnou praxí. S podrobná data instrumentace je tedy důležité.

Některé produkty automaticky vyladit kód. I když tato řešení může dobře fungovat, je téměř vždy vyžaduje ruční instrumentace. Nakonec musíte mít dostatek informací, které forensically ladění aplikace. Tento dokument popisuje různé přístupy k instrumentaci kódu a kdy zvolit jeden ze způsobů před jiným.

Příklady, jak používat tyto návrhy najdete v tématu [přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Při vytváření řešení Service Fabric pomocí šablony v sadě Visual Studio **EventSource**-odvozené třídy (**ServiceEventSource** nebo **ActorEventSource**) se vygeneruje . Šablonu se vytvoří, ve kterém můžete přidat události pro vaše aplikace nebo služba. **EventSource** název **musí** být jedinečné a musí se přejmenovat z výchozí šablony řetězec společnost –&lt;řešení&gt;-&lt;projektu &gt;. S více **EventSource** definice, které používají stejný název způsobí, že problém v době běhu. Každý definovaný událost musí mít jedinečný identifikátor. Pokud není jedinečný identifikátor, dojde k selhání běhového prostředí. Některé organizace preassign rozsahy hodnot pro identifikátory, jak zamezit konfliktům mezi samostatné vývojové týmy. Další informace najdete v tématu [daňové na blogu](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) nebo [dokumentaci MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core protokolování

Je důležité, abyste pečlivě naplánovat, jak se váš kód instrumentace. Instrumentace správný plán vám může pomoct vyhnout potenciálně destabilizing vašeho základu kódu a pak museli reinstrument kód. Chcete-li snížit riziko, můžete použít knihovnu instrumentace jako [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), které je součástí sady Microsoft ASP.NET Core. ASP.NET Core má [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) rozhraní, které můžete použít u poskytovatele podle vašeho výběru, při současné minimalizaci vlivu na existující kód. Kód můžete použít v ASP.NET Core ve Windows a Linuxu, a v úplné rozhraní .NET Framework, takže je váš kód instrumentace standardizované.

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights má bohaté integrace s platformou Service Fabric úprav. Uživatelé mohou přidat balíčky nuget AI Service Fabric a příjem dat a protokolů a shromažďují zobrazit na webu Azure Portal. Navíc uživatelé můžou přidávat své vlastní telemetrická data, pokud chcete diagnostikovat a ladit své aplikace a sledování, které jsou služby a ve svých aplikacích používají nejčastěji. [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) třídy v sadě SDK poskytuje mnoho způsobů, jak sledovat telemetrii ve svých aplikacích. Podívejte se na příklad toho, jak instrumentovat a přidejte application insights do vaší aplikace v našem kurzu pro [monitorování a Diagnostika aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>Další postup

Po výběru poskytovatele protokolování k instrumentaci aplikací a služeb, protokolů a událostí muset před odesláním na jakoukoli platformu analýzy se dají agregovat. Přečtěte si informace o [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), [využitím EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), a [WAD](service-fabric-diagnostics-event-aggregation-wad.md) lépe pochopit některé doporučené možnosti.
