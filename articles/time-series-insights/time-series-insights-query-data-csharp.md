---
title: Dotazujte se na data z prostředí GA pomocí kódu C# – Přehledy azure time series | Dokumenty společnosti Microsoft
description: Zjistěte, jak dotazovat data z prostředí Azure Time Series Insights pomocí vlastní aplikace napsané v C#.
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383890"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazovat se na data z prostředí Azure Time Series Insights GA pomocí C #

Tento příklad jazyka C# ukazuje, jak používat [API dotazu GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) k dotazování dat z prostředí Ga Azure Time Series Insights.

> [!TIP]
> Zobrazit ukázky kódu [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)GA C# na .

## <a name="summary"></a>Souhrn

Ukázkový kód níže ukazuje následující funkce:

* Jak získat přístupový token prostřednictvím služby Azure Active Directory pomocí [adresáře Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Jak předat tento získaný přístupový token v `Authorization` záhlaví následujících požadavků rozhraní API dotazu. 

* Ukázka volá každé z GA dotazu API ukazuje, jak jsou požadavky HTTP provedeny na:
    * [Získejte rozhraní API prostředí,](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) které vrátí prostředí, ke kterým má uživatel přístup.
    * [Získat rozhraní API pro dostupnost prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Získání rozhraní API metadat prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) pro načtení metadat prostředí
    * [Rozhraní API událostí prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Získat rozhraní API pro agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Jak pracovat s GA query API pomocí WSS zprávu:

   * [Získat rozhraní API pro streamované události prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Získat streamované rozhraní API pro agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Požadavky a nastavení

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřídit](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) prostředí GA Azure Time Series Insights.
1. Nakonfigurujte prostředí Azure Time Series Insights pro Azure Active Directory, jak je popsáno v [protokolu Ověřování a autorizace](time-series-insights-authentication-and-authorization.md). 
1. Nainstalujte požadované závislosti projektu.
1. Upravte ukázkový kód níže nahrazením každého **#DUMMY#** příslušným identifikátorem prostředí.
1. Spusťte kód uvnitř sady Visual Studio.

## <a name="project-dependencies"></a>Závislosti projektu

Doporučujeme používat nejnovější verzi sady Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - verze 16.4.2+

Ukázkový kód má dvě požadované závislosti:

* [Balíček Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - balení 9.0.1.

Stáhněte si balíčky v Visual Studiu 2019 výběrem **možnosti Sestavení** > **sestavení řešení.**

Případně přidejte balíčky pomocí [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Ukázkový kód jazyka C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v [odkazu rozhraní API dotazu](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit aplikaci JavaScript pomocí sady SDK klienta](https://github.com/microsoft/tsiclient) k přehledům time series.
