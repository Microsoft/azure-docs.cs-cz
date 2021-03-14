---
title: Dotazování dat z prostředí Gen2 pomocí C#-Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak zadávat dotazy na data z prostředí Azure Time Series Insights Gen2 pomocí aplikace napsané v jazyce C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: aecd18fd0d568904f9704b749525204ced05f3ef
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463421"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Dotazování dat z prostředí Azure Time Series Insights Gen2 pomocí jazyka C Sharp

Tento příklad v jazyce C# ukazuje, jak zadávat dotazy na data z [rozhraní API pro přístup k datům Gen2](/rest/api/time-series-insights/reference-data-access-overview) v prostředích Azure Time Series Insights Gen2.

> [!TIP]
> Prohlédněte si ukázky kódu C# Gen2 na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Souhrn

Vzorový kód níže znázorňuje následující funkce:

* Podpora automatické generace sady SDK z [Azure AutoRest](https://github.com/Azure/AutoRest)
* Získání přístupového tokenu prostřednictvím Azure Active Directory pomocí [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Jak předat token přístupu, který získal přístup v `Authorization` hlavičce dalších požadavků na přístup k rozhraní API pro přístup k datům.
* Ukázka poskytuje rozhraní konzoly, které demonstruje, jak se provádí požadavky HTTP na následující:
  * [Rozhraní API pro prostředí Gen2](/rest/api/time-series-insights/reference-environments-apis)
    * Získat rozhraní API pro [dostupnost prostředí](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) a [získat rozhraní API pro schéma událostí](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [Rozhraní API pro dotazy Gen2](/rest/api/time-series-insights/reference-query-apis)
    * Získat [API pro události](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), získat rozhraní [API řady](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)a [získat agregované rozhraní API řady](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [Rozhraní API modelu časové řady](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Rozhraní](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) API pro [dávkování](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch) hierarchií a hierarchií
    * Rozhraní API pro [získání typů](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) a [typů rozhraní Batch API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * Rozhraní [API pro dávku](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch) instancí a instance rozhraní API pro [načtení instancí](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances)

* Rozšířené možnosti [vyhledávání](/rest/api/time-series-insights/reference-model-apis#search-features) a [TSX](/rest/api/time-series-insights/reference-time-series-expression-syntax)

## <a name="prerequisites-and-setup"></a>Požadavky a instalace

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřídí prostředí Gen2 Azure Time Series Insights](./how-to-create-environment-using-portal.md) .
1. Nakonfigurujte Azure Time Series Insights prostředí pro Azure Active Directory, jak je popsáno v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md).
1. Spusťte [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) , jak je uvedeno v [Readme.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) a vygenerujte závislosti klienta Azure Time Series Insights Gen2.
1. Otevřete `TSIPreviewDataPlaneclient.sln` řešení a nastavte `DataPlaneClientSampleApp` jako výchozí projekt v sadě Visual Studio.
1. Požadované závislosti projektu nainstalujte pomocí kroků popsaných [níže](#project-dependencies) a zkompilujte příklad do spustitelného `.exe` souboru.
1. Spusťte `.exe` soubor dvojitým kliknutím na něj.

## <a name="project-dependencies"></a>Závislosti projektu

Doporučuje se použít nejnovější verzi sady Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – verze 16.4.2 +

Vzorový kód má několik požadovaných závislostí, které lze zobrazit v souboru [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) .

Stáhněte si balíčky v aplikaci Visual Studio 2019 tak, že vyberete  >  možnost **řešení** sestavení sestavení.

Případně přidejte jednotlivé balíčky pomocí [NuGet 2.12 +](https://www.nuget.org/). Například:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Ukázkový kód C#

Chcete-li získat přístup k ukázkovému kódu jazyka C#, přečtěte si prosím úložiště [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

> [!NOTE]
>
> * Ukázku kódu lze provést bez změny výchozích proměnných prostředí.
> * Ukázka kódu se zkompiluje do spustitelné aplikace spustitelného rozhraní .NET.

## <a name="next-steps"></a>Další kroky

* Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](/rest/api/time-series-insights/reference-query-apis).

* Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Azure Time Series Insights.