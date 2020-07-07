---
title: Dotazování dat z prostředí GA pomocí kódu C#-Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak zadávat dotazy na data z Azure Time Series Insights prostředí pomocí vlastní aplikace napsané v jazyce C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81383890"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat z Azure Time Series Insightsho prostředí GA pomocí jazyka C #

Tento příklad v jazyce C# ukazuje, jak použít [rozhraní API pro dotazy GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) k dotazování dat z Azure Time Series Insights GA prostředí.

> [!TIP]
> Podívejte se na ukázky kódů GA C# na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample) .

## <a name="summary"></a>Souhrn

Vzorový kód níže znázorňuje následující funkce:

* Získání přístupového tokenu prostřednictvím Azure Active Directory pomocí [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Postup předání tohoto přístupového tokenu v `Authorization` hlavičce dalších požadavků na rozhraní API pro dotazy. 

* Ukázka volá každé z rozhraní API pro dotazy GA, které demonstrují, jakým způsobem jsou požadavky HTTP provedeny:
    * [Získat prostředí API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) pro vrácení prostředí, ke kterým má uživatel přístup
    * [Získat rozhraní API dostupnosti prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Získat rozhraní API metadat prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) pro načtení metadat prostředí
    * [Získat rozhraní API pro události prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Získat rozhraní API pro agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Jak pracovat s rozhraními API pro dotazy GA pomocí WSS ke zprávě:

   * [Načíst rozhraní API pro streamované události prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Načíst rozhraní API pro agregované datové prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Požadavky a instalace

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřízení Azure Time Series Insightsho prostředí GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Nakonfigurujte Azure Time Series Insights prostředí pro Azure Active Directory, jak je popsáno v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). 
1. Nainstalujte požadované závislosti projektu.
1. Níže uvedený ukázkový kód nahraďte každým **#DUMMY #** a příslušným identifikátorem prostředí.
1. Spusťte kód v rámci sady Visual Studio.

## <a name="project-dependencies"></a>Závislosti projektu

Doporučuje se použít nejnovější verzi sady Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – verze 16.4.2 +

Vzorový kód má dvě požadované závislosti:

* Balíček [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* [Newtonsoft.Jsbalíček on](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1.

Stáhněte si balíčky v aplikaci Visual Studio 2019 tak, **Build**že vyberete  >  možnost**řešení** sestavení sestavení.

Případně přidejte balíčky pomocí [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Ukázkový kód C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Time Series Insights.
