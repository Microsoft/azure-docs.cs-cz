---
title: Distribuované trasování v Azure Application Insights | Microsoft Docs
description: Poskytuje informace o podpoře Microsoftu pro distribuované trasování prostřednictvím našeho místního předávání a partnerství v projektu OpenCensus.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0b51617966ad85037ad29f3e5005a17f66602e01
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677121"
---
# <a name="what-is-distributed-tracing"></a>Co je distribuované trasování?

Nástupem moderního cloudu a architektury [mikroslužeb](https://azure.com/microservices) má za následek jednoduché, nezávisle nasazené služby, které můžou snížit náklady a zvýšit dostupnost a propustnost. I když ale tyto přesuny usnadňují pochopení jednotlivých služeb jako celku, provedli jsme jim mnohem obtížnější příčiny a ladění.

V architekturách monolitické jsme se seznámili s používáním ladění pomocí zásobníků volání. Zásobníky volání jsou skvělými nástroji pro zobrazení toku provádění (metoda A s názvem metoda B, která se nazývá metoda C), spolu s podrobnostmi a parametry pro každé z těchto volání. To je skvělé pro monolitů nebo služby běžící na jednom procesu, ale jak ladit, když je volání na hranici procesu, a ne jenom odkaz na místní zásobník? 

To je místo, kde se nachází distribuované trasování.  

Distribuované trasování je ekvivalentem zásobníků volání pro moderní cloudové a mikroslužby, a to s přidáním služby zjednodušený Performance profileru, která je vyvolána v. V Azure Monitor poskytujeme dvě prostředí pro využívání distribuovaných dat trasování. První je naše zobrazení [diagnostiky transakcí](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) , což je jako zásobník volání s přidanou časovou dimenzí. Zobrazení Diagnostika transakcí poskytuje přehled o jedné transakci/žádosti a je užitečné při hledání hlavní příčiny problémů se spolehlivostí a kritických bodů výkonu na základě jednotlivých požadavků.

Azure Monitor také nabízí zobrazení [mapy aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) , které agreguje mnoho transakcí, aby zobrazovalo topologické zobrazení, jak systémy pracují a jaké jsou průměrné výkonové a chybové míry. 

## <a name="how-to-enable-distributed-tracing"></a>Jak povolit distribuované trasování

Povolení distribuovaného trasování napříč službami v aplikaci je jednoduché jako přidání vhodné sady SDK nebo knihovny ke každé službě v závislosti na jazyku, ve kterém byla služba implementována.

## <a name="enabling-via-application-insights-sdks"></a>Povolení přes Application Insights SDK

Rozhraní Application Insights SDK pro .NET, .NET Core, Java, Node. js a JavaScript podporuje nativně distribuované trasování. Pokyny k instalaci a konfiguraci každé Application Insights SDK jsou k dispozici níže:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Když je nainstalovaná a nakonfigurovaná sada SDK s patřičnou Application Insights, trasovací informace se automaticky shromažďují pro oblíbené architektury, knihovny a technologie pomocí automatických kolekcí závislosti sady SDK. Úplný seznam podporovaných technologií je k dispozici v [dokumentaci k automatické kolekci závislostí](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Kromě toho je možné každou technologii sledovat ručně pomocí volání [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) na [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Povolit přes OpenCensus

Kromě Application Insights sady SDK Application Insights podporuje také distribuované trasování prostřednictvím [OpenCensus](https://opencensus.io/). OpenCensus je open source, dodavatel – nezávislá, jedna distribuce knihoven, která poskytuje shromažďování metrik a distribuované trasování pro služby. Umožňuje také komunitě Open Source povolit distribuované trasování s oblíbenými technologiemi, jako je Redis, memcached nebo MongoDB. [Microsoft spolupracuje na OpenCensus s několika dalšími monitorováními a cloudových partnerů](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Pokud chcete přidat funkce distribuované vektorizace do aplikace pomocí OpenCensus, nejdřív [nainstalujte a nakonfigurujte Application Insights místní služby pro](./../../azure-monitor/app/opencensus-local-forwarder.md)zakládání. Odtud nakonfigurujte OpenCensus pro směrování distribuovaných dat trasování prostřednictvím místního předávání. Podporují se [Python](./../../azure-monitor/app/opencensus-python.md) i [Přejít](./../../azure-monitor/app/opencensus-go.md) .

Web OpenCensus udržuje referenční dokumentaci rozhraní API pro [Python](https://opencensus.io/api/python/trace/usage.html) a [Přejít](https://godoc.org/go.opencensus.io)a také různá různá vodítka pro použití OpenCensus. 

## <a name="next-steps"></a>Další kroky

* [Průvodce používáním Pythonu v OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Monitorování výkonu na konci](./../../azure-monitor/learn/tutorial-performance.md)
