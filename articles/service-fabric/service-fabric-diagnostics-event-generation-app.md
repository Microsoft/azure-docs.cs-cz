---
title: Monitorování úrovně aplikační struktury Azure Service Fabric
description: Další informace o událostech a protokolech na úrovni aplikací a služeb, které slouží ke sledování a diagnostice clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464722"
---
# <a name="application-logging"></a>Protokolování aplikací

Instrumentace kódu není jen způsob, jak získat přehledo svých uživatelů, ale také jediný způsob, jak zjistit, zda je něco špatně ve vaší aplikaci a diagnostikovat, co je třeba opravit. I když technicky je možné připojit ladicí program k produkční službě, není to běžná praxe. Takže s podrobnými daty instrumentace je důležité.

Některé produkty automaticky instrumentují váš kód. Ačkoli tato řešení mohou fungovat dobře, ruční instrumentace je téměř vždy nutné být specifické pro vaši obchodní logiku. Nakonec musíte mít dostatek informací k forenznímu ladění aplikace. Service Fabric aplikace lze instrumentovat s libovolným rámcem protokolování. Tento dokument popisuje několik různých přístupů k instrumentaci kódu a kdy zvolit jeden přístup před druhým. 

Příklady použití těchto návrhů naleznete v tématu [Přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Aplikace Přehledy SDK

Application Insights má bohatou integraci s Service Fabric po vybalení z krabice. Uživatelé můžou přidávat balíčky nugetu ai service fabric a přijímat data a protokoly vytvořené a shromážděné zobrazitelné na webu Azure Portal. Kromě toho se uživatelům doporučuje přidat vlastní telemetrii, aby diagnostikovali a ladili své aplikace a sledovali, které služby a části jejich aplikace se používají nejvíce. Třída [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) v sadě SDK poskytuje mnoho způsobů sledování telemetrie ve vašich aplikacích. Podívejte se na příklad, jak instrumentovat a přidat přehledy aplikací do vaší aplikace v našem kurzu pro [monitorování a diagnostiku aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Při vytváření service fabric řešení ze šablony v sadě Visual Studio, **EventSource**odvozené třídy **(ServiceEventSource** nebo **ActorEventSource**) je generována. Je vytvořena šablona, ve které můžete přidat události pro vaši aplikaci nebo službu. Název **EventSource** **musí** být jedinečný a měl by být přejmenován&lt;&gt;-&lt;z&gt;výchozího řetězce šablony MyCompany - solution project . S více **EventSource** definice, které používají stejný název způsobí problém za běhu. Každá definovaná událost musí mít jedinečný identifikátor. Pokud identifikátor není jedinečný, dojde k selhání za běhu. Některé organizace předem přiřazují rozsahy hodnot pro identifikátory, aby se zabránilo konfliktům mezi samostatnými vývojovými týmy. Další informace naleznete [v blogu vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) nebo v [dokumentaci k msdn](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>ASP.NET jádra protokolování

Je důležité pečlivě naplánovat, jak budete nástroj kódu. Správný plán instrumentace vám může pomoci vyhnout se potenciálně destabilizovat základní kód a potom je nutné znovu instrumentovat kód. Chcete-li snížit riziko, můžete zvolit knihovnu instrumentace, jako je [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), která je součástí Microsoft ASP.NET Core. ASP.NET Core má rozhraní [ILogger,](/dotnet/api/microsoft.extensions.logging.ilogger) které můžete použít s poskytovatelem dle vašeho výběru, při minimalizaci vlivu na stávající kód. Kód můžete použít v ASP.NET Core v systému Windows a Linux a v úplném rozhraní .NET Framework, takže váš kód instrumentace je standardizovaný.

## <a name="next-steps"></a>Další kroky

Jakmile jste si vybrali poskytovatele protokolování pro instrumentaci aplikací a služeb, vaše protokoly a události je třeba agregovat, než je bude možné odeslat na libovolnou platformu analýzy. Přečtěte si o [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) a [EventFlow,](service-fabric-diagnostics-event-aggregation-eventflow.md) abyste lépe porozuměli některým doporučeným možnostem Azure Monitoru.
