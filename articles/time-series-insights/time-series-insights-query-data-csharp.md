---
title: Dotazování dat z prostředí GA pomocí C# Azure Time Series Insights kódu | Microsoft Docs
description: Naučte se, jak zadávat dotazy na data z Azure Time Series Insights prostředí pomocí vlastní aplikace C#napsané v.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987967"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat z Azure Time Series Insightsho prostředí GA pomocíC#

Tento C# příklad ukazuje, jak použít [rozhraní API pro dotazy GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) k dotazování dat z Azure Time Series Insights GA prostředí.

> [!TIP]
> Podívejte se C# na ukázky kódů GA na [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Souhrn

Vzorový kód níže znázorňuje následující funkce:

* Získání přístupového tokenu prostřednictvím Azure Active Directory pomocí [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Postup předání tohoto přístupového tokenu v `Authorization` hlavičce dalších požadavků na rozhraní API pro dotazování. 

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
* Balíček [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1.

Stáhněte balíčky v aplikaci Visual Studio 2019 výběrem možnosti **sestavit** > **Sestavit řešení** .

Případně přidejte balíčky pomocí [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#vzorový kód

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Time Series Insights.
