---
title: Distribuované trasování v Azure Application Insights | Microsoft Docs
description: Poskytuje informace o podpoře Microsoftu pro distribuované trasování prostřednictvím našeho partnerství v projektu OpenCensus.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579517"
---
# <a name="what-is-distributed-tracing"></a>Co je distribuované trasování?

Nástupem moderního cloudu a architektury [mikroslužeb](https://azure.com/microservices) má za následek jednoduché, nezávisle nasazené služby, které můžou snížit náklady a zvýšit dostupnost a propustnost. I když ale tyto přesuny usnadňují pochopení jednotlivých služeb jako celku, provedli jsme jim mnohem obtížnější příčiny a ladění.

V architekturách monolitické jsme se seznámili s používáním ladění pomocí zásobníků volání. Zásobníky volání jsou skvělými nástroji pro zobrazení toku provádění (metoda A s názvem metoda B, která se nazývá metoda C), spolu s podrobnostmi a parametry pro každé z těchto volání. To je skvělé pro monolitů nebo služby běžící na jednom procesu, ale jak ladit, když je volání na hranici procesu, a ne jenom odkaz na místní zásobník? 

To je místo, kde se nachází distribuované trasování.  

Distribuované trasování je ekvivalentem zásobníků volání pro moderní cloudové a mikroslužby, a to s přidáním služby zjednodušený Performance profileru, která je vyvolána v. V Azure Monitor poskytujeme dvě prostředí pro využívání distribuovaných dat trasování. První je naše zobrazení [diagnostiky transakcí](./transaction-diagnostics.md) , což je jako zásobník volání s přidanou časovou dimenzí. Zobrazení Diagnostika transakcí poskytuje přehled o jedné transakci/žádosti a je užitečné při hledání hlavní příčiny problémů se spolehlivostí a kritických bodů výkonu na základě jednotlivých požadavků.

Azure Monitor také nabízí zobrazení [mapy aplikace](./app-map.md) , které agreguje mnoho transakcí, aby zobrazovalo topologické zobrazení, jak systémy pracují a jaké jsou průměrné výkonové a chybové míry. 

## <a name="how-to-enable-distributed-tracing"></a>Jak povolit distribuované trasování

Povolení distribuovaného trasování napříč službami v aplikaci je jednoduché jako přidání správného agenta, sady SDK nebo knihovny do každé služby na základě jazyka, ve kterém byla služba implementována.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Povolení prostřednictvím Application Insights prostřednictvím automatické instrumentace nebo sad SDK

Všechna Application Insights agenti a sady SDK pro .NET, .NET Core, Java, Node.js a JavaScript podporují nativně distribuované trasování. Pokyny k instalaci a konfiguraci každé Application Insights SDK jsou k dispozici níže:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Když je nainstalovaná a nakonfigurovaná sada SDK s patřičnou Application Insights, trasovací informace se automaticky shromažďují pro oblíbené architektury, knihovny a technologie pomocí automatických kolekcí závislosti sady SDK. Úplný seznam podporovaných technologií je k dispozici v [dokumentaci k automatické kolekci závislostí](./auto-collect-dependencies.md).

 Kromě toho je možné každou technologii sledovat ručně pomocí volání [TrackDependency](./api-custom-events-metrics.md) na [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Povolit přes OpenCensus

Kromě Application Insights sady SDK Application Insights podporuje také distribuované trasování prostřednictvím [OpenCensus](https://opencensus.io/). OpenCensus je open source, dodavatel – nezávislá, jedna distribuce knihoven, která poskytuje shromažďování metrik a distribuované trasování pro služby. Umožňuje také komunitě Open Source povolit distribuované trasování s oblíbenými technologiemi, jako je Redis, memcached nebo MongoDB. [Microsoft spolupracuje na OpenCensus s několika dalšími monitorováními a cloudových partnerů](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Web OpenCensus udržuje referenční dokumentaci rozhraní API pro [Python](https://opencensus.io/api/python/trace/usage.html) a [Přejít](https://godoc.org/go.opencensus.io)a také různá různá vodítka pro použití OpenCensus. 

## <a name="next-steps"></a>Další kroky

* [Průvodce používáním Pythonu v OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikace](./app-map.md)
* [Monitorování výkonu na konci](../app/tutorial-performance.md)

