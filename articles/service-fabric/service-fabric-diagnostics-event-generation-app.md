---
title: Monitorování úrovně aplikace v Azure Service Fabric
description: Přečtěte si informace o událostech a protokolech na úrovni služby, které slouží k monitorování a diagnostice clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a1df5d033701195f4fe5f6b7174f3883b84393ab
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791269"
---
# <a name="application-logging"></a>Protokolování aplikací

Instrumentace kódu není pouze způsob, jak získat přehled o vašich uživatelích, ale také pouze o tom, jak můžete zjistit, zda je v aplikaci něco špatného, a diagnostikovat, co je potřeba opravit. I když je technicky možné připojit ladicí program k produkční službě, nejedná se o běžný postup. Proto je důležité mít podrobná data instrumentace.

Některé produkty automaticky instrumentují váš kód. I když tato řešení můžou dobře fungovat, ruční instrumentace je téměř vždy nutná, aby byla specifická pro vaši obchodní logiku. Na konci musíte mít dostatek informací, aby bylo možné aplikaci Forensically ladit. Service Fabric aplikace lze instrumentovat pomocí jakéhokoli protokolovacího rozhraní. Tento dokument popisuje několik různých přístupů k instrumentaci kódu a kdy zvolit jeden přístup přes jiný. 

Příklady použití těchto návrhů najdete v tématu [Přidání protokolování do aplikace Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Sada Application Insights SDK

Application Insights má bohatou integraci s Service Fabric vycházejícími z boxu. Uživatelé můžou přidávat balíčky NuGet Service Fabric AI a přijímat data a protokoly vytvořené a shromážděné v Azure Portal. Kromě toho se uživatelům doporučuje přidat vlastní telemetrii, aby bylo možné diagnostikovat a ladit své aplikace a sledovat, které služby a části jejich aplikace jsou používány nejvíc. Třída [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) v sadě SDK poskytuje mnoho způsobů, jak sledovat telemetrii ve vašich aplikacích. Podívejte se na příklad, jak instrumentovat a přidat Application Insights do aplikace v našem kurzu pro [monitorování a diagnostiku aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md) .

## <a name="eventsource"></a>EventSource

Při vytváření řešení Service Fabric ze šablony v aplikaci Visual Studio je vygenerována třída odvozená od objektu **EventSource**(**ServiceEventSource** nebo **ActorEventSource**). Vytvoří se šablona, do které můžete přidat události pro svou aplikaci nebo službu. Název **EventSource** **musí** být jedinečný a měl by být přejmenován z výchozí šablony řetězce společnost – &lt; řešení &gt; - &lt; &gt; . Pokud máte více definic **EventSource** , které používají stejný název, způsobí problém v době běhu. Každá definovaná událost musí mít jedinečný identifikátor. Pokud identifikátor není jedinečný, dojde k chybě modulu runtime. Některé organizace předřadí rozsah hodnot pro identifikátory, aby nedocházelo ke konfliktům mezi samostatnými vývojovými týmy. Další informace najdete v [blogu Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) nebo v [dokumentaci MSDN](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>Protokolování ASP.NET Core

Je důležité pečlivě naplánovat způsob, jakým budete svůj kód instrumentovat. Správný plán instrumentace vám může přispět k tomu, abyste se vyhnuli potenciálně destabilizující základu kódu a pak museli kód znovu instrumentovat. Chcete-li snížit riziko, můžete zvolit knihovnu instrumentace, například [Microsoft. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), která je součástí Microsoft ASP.NET Core. ASP.NET Core má rozhraní [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) , které můžete použít s poskytovatelem dle vašeho výběru a současně minimalizovat efekt na stávající kód. Můžete použít kód v ASP.NET Core v systému Windows a Linux a v úplném .NET Framework, aby byl kód instrumentace standardizován.

## <a name="next-steps"></a>Další kroky

Jakmile vyberete poskytovatele protokolování pro instrumentaci vašich aplikací a služeb, musí být vaše protokoly a události agregované předtím, než bude možné je odeslat do jakékoli analytické platformy. Přečtěte si o [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) a [využitím eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md) , abyste lépe pochopili některé z Azure monitor doporučených možností.
