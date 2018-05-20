---
title: Úrovni aplikace Azure Service Fabric monitorování | Microsoft Docs
description: Další informace o aplikaci a události na úrovni služby a protokoly použít k monitorování a diagnostice Azure Service Fabric clustery.
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
ms.openlocfilehash: b8118d83e2be452c6aa5bbc8b7a3c220d26903a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="application-and-service-level-logging"></a>Úroveň protokolování služby a aplikace

Instrumentace kód je základem pro většině ostatních aspektů monitorování vašim službám. Instrumentace je jediným způsobem, jak můžete víte, že je něco špatně a ke zjištění toho, co je nutné opravit. Přestože je technicky možné se připojit ladicí program k produkčním služby, není běžnou praxi. Nutnosti podrobně data instrumentace je tedy důležité.

Některé produkty, automaticky instrumentace vašeho kódu. I když tato řešení můžete dobře fungovat, je vyžadováno téměř vždy ruční instrumentace. V části end musí mít dostatek informací k forensically ladění aplikace. Tento dokument popisuje různé přístupy k instrumentaci kódu a kdy zvolit jeden ze způsobů oproti jinému.

Příklady týkající se používání těchto doporučení najdete v tématu [přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Když vytvoříte řešení Service Fabric ze šablony v sadě Visual Studio **EventSource**-odvozené třídy (**ServiceEventSource** nebo **ActorEventSource**) se vygeneruje. Je vytvořit šablonu, ve kterém můžete přidat události pro vaše aplikace nebo služba. **EventSource** název **musí** být jedinečné a musí jej přejmenovat z výchozí šablony řetězec Moje_firma -&lt;řešení&gt;-&lt;projektu&gt;. S více **EventSource** definice, které používají stejný název způsobuje problém v době běhu. Každé definované události, musí mít jedinečný identifikátor. Pokud není jedinečný identifikátor, dojde k selhání běhového prostředí. Některé organizace preassign rozsahy hodnoty pro identifikátory, aby nedocházelo ke konfliktům mezi samostatnou vývojové týmy. Další informace najdete v tématu [hovou na blogu](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) nebo [dokumentace MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET Core protokolování

Je důležité pečlivě naplánovat, jak bude instrumentace vašeho kódu. Plán správné instrumentace můžete vyhnout potenciálně destabilizing vaše základu kódu a pak se museli reinstrument kód. Aby se snížilo riziko, můžete knihovna nástrojů, jako je [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), který je součástí Microsoft ASP.NET Core. ASP.NET Core má [objektu ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) rozhraní, které můžete použít s poskytovatelem podle vaší volby, a současně minimalizujete její vliv na existující kód. Můžete použít kód v ASP.NET Core v systému Windows a Linux, a v úplné rozhraní .NET Framework, takže je váš kód instrumentace standardizované.

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights obsahuje bohatou integrace s Service Fabric mimo pole. Uživatele můžete přidat balíčky nuget AI Service Fabric a přijímat data a protokoly vytvořené a shromažďují lze zobrazit v portálu Azure. Uživatelé navíc doporučujeme přidat vlastní telemetrii diagnostikovat a ladit jejich aplikace a sledování, které jsou služby a částí jejich aplikace používají nejvíc. [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) třídy v sadě SDK poskytuje mnoho způsobů, jak sledovat telemetrie ve svých aplikacích. Podívejte se na příklad instrumentace a přidat službu application insights do vaší aplikace v našem kurzu pro [monitorování a diagnostice aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="next-steps"></a>Další postup

Jakmile jste vybrali poskytovatele protokolování a instrumentace aplikací a služeb, protokolů a událostí muset agregovat před odesláním pro žádnou platformu analysis. Přečtěte si informace o [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md), a [WAD](service-fabric-diagnostics-event-aggregation-wad.md) lépe pochopit některé doporučené možnosti.
