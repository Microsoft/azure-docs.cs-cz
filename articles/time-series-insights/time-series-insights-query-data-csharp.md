---
title: Dotazování dat z prostředí Gen1 pomocí kódu C#-Azure Time Series Insights Gen1 | Microsoft Docs
description: Naučte se, jak zadávat dotazy na data z prostředí Azure Time Series Insights Gen1 pomocí vlastní aplikace napsané v jazyce C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020040"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Dotazování dat z prostředí Azure Time Series Insights Gen1 pomocí jazyka C Sharp

> [!CAUTION]
> Toto je Gen1 článek.

Tento příklad v jazyce C# ukazuje, jak používat [rozhraní API pro dotazy Gen1](/rest/api/time-series-insights/gen1-query) k dotazování dat z prostředí Azure Time Series Insights Gen1.

> [!TIP]
> Prohlédněte si ukázky kódu C# Gen1 na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Souhrn

Vzorový kód níže znázorňuje následující funkce:

* Získání přístupového tokenu prostřednictvím Azure Active Directory pomocí [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Postup předání tohoto přístupového tokenu v `Authorization` hlavičce dalších požadavků na rozhraní API pro dotazy.

* Ukázka volá všechna rozhraní API pro dotazy Gen1, která demonstrují, jak jsou požadavky HTTP provedeny:
  * [Získat prostředí API](/rest/api/time-series-insights/gen1-query-api#get-environments-api) pro vrácení prostředí, ke kterým má uživatel přístup
  * [Získat rozhraní API dostupnosti prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Získat rozhraní API metadat prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) pro načtení metadat prostředí
  * [Získat rozhraní API pro události prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Získat rozhraní API pro agregace prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Jak pracovat s rozhraními API pro dotazy Gen1 pomocí WSS ke zprávě:

  * [Načíst rozhraní API pro streamované události prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Načíst rozhraní API pro agregované datové prostředí](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Požadavky a instalace

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřídí prostředí Gen1 Azure Time Series Insights](./time-series-insights-get-started.md) .
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

Stáhněte si balíčky v aplikaci Visual Studio 2019 tak, **Build** že vyberete  >  možnost **řešení** sestavení sestavení.

Případně přidejte balíčky pomocí [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Ukázkový kód C#

Chcete-li získat přístup k ukázkovému kódu jazyka C#, přečtěte si prosím [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] úložiště.

## <a name="next-steps"></a>Další kroky

* Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](/rest/api/time-series-insights/gen1-query-api).

* Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Time Series Insights.
Azure-Samples/Azure-Time-Series-Insights/Gen1-Sample/CSharp-TSI-Gen1-Sample/program. cs