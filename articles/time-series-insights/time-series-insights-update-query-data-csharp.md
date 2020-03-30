---
title: Dotazovat se na data z prostředí Preview pomocí jazyka C# – Přehledy časové řady Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak dotazovat data z prostředí Azure Time Series Insights pomocí aplikace napsané v C#.
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980983"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Dotazování na data z prostředí Azure Time Series Insights Preview pomocí C #

Tento příklad Jazyka C# ukazuje, jak dotazovat data z [api pro přístup k datům preview](https://docs.microsoft.com/rest/api/time-series-insights/preview) v prostředích Azure Time Series Insights Preview.

> [!TIP]
> Zobrazit náhled c# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)ukázky kódu na .

## <a name="summary"></a>Souhrn

Ukázkový kód níže ukazuje následující funkce:

* Podpora automatického generování sady SDK z [Azure AutoRest](https://github.com/Azure/AutoRest).
* Jak získat přístupový token prostřednictvím služby Azure Active Directory pomocí [adresáře Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Jak předat tento získaný přístupový token v `Authorization` záhlaví následných požadavků rozhraní API pro přístup k datům. 
* Ukázka obsahuje rozhraní konzoly, které ukazuje, jak jsou požadavky HTTP prováděny pro:

    * [Rozhraní API preview prostředí](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Získat rozhraní API dostupnosti prostředí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) a [získat rozhraní API schématu událostí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Náhled rozhraní API dotazu](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Získat rozhraní API pro události](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), rozhraní API pro získání [řady](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)a získat rozhraní API [pro agregační řady](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [AP( Časová řada) modelových api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Získat rozhraní API hierarchií](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) a [dávkové rozhraní hierarchií](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Získat typy rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) a [dávkové rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Získat rozhraní API instancí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) [a dávkové rozhraní INStancí](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Pokročilé [možnosti vyhledávání](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) a [TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Požadavky a nastavení

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřídit prostředí Preview Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Nakonfigurujte prostředí Azure Time Series Insights pro Azure Active Directory, jak je popsáno v [protokolu Ověřování a autorizace](time-series-insights-authentication-and-authorization.md). 
1. Spusťte [soubor GenerateCode.bat,](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) jak je uvedeno v [Readme.md,](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) a vygenerujte závislosti klienta Time Series Insights Preview.
1. Otevřete `TSIPreviewDataPlaneclient.sln` řešení `DataPlaneClientSampleApp` a nastavte jako výchozí projekt v sadě Visual Studio.
1. Nainstalujte požadované závislosti projektu pomocí [níže](#project-dependencies) popsaných kroků a `.exe` zkompilujte příklad do spustitelného souboru.
1. Spusťte `.exe` soubor poklepáním na něj.

## <a name="project-dependencies"></a>Závislosti projektu

Doporučujeme používat nejnovější verzi sady Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - verze 16.4.2+

Ukázkový kód obsahuje několik požadovaných závislostí, které lze zobrazit v souboru [packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Stáhněte si balíčky v Visual Studiu 2019 výběrem **možnosti Sestavení** > **sestavení řešení.** 

Případně můžete přidat každý balíček pomocí [NuGet 2.12+](https://www.nuget.org/). Například:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Ukázkový kód jazyka C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Ukázka kódu může být spuštěna bez změny výchozích proměnných prostředí.
> * Ukázka kódu se zkompiluje do spustitelné konzolové aplikace .NET.

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v [odkazu rozhraní API dotazu](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Přečtěte si, jak [připojit aplikaci JavaScript pomocí sady SDK klienta](https://github.com/microsoft/tsiclient) k přehledům time series.
