---
title: Dotazování dat z Azure Time Series Insightsho prostředí GA C# pomocí kódu | Microsoft Docs
description: Tento článek popisuje, jak zadávat dotazy na data z Azure Time Series Insights prostředí pomocí kódování vlastní aplikace napsané v C# jazyce .NET (C-Sharp).
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 28f255c66c0e4a929914d037ee9c93b509a3602b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677786"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat z Azure Time Series Insightsho prostředí GA pomocíC#

Tento C# příklad ukazuje, jak zadávat dotazy na data z Azure Time Series Insights GA prostředí.

V ukázce najdete několik základních příkladů použití rozhraní API pro dotazy:

1. V rámci přípravného kroku získá přístupový token prostřednictvím rozhraní Azure Active Directory API. Tento token předejte v `Authorization` hlavičce každé žádosti rozhraní API pro dotazy. Informace o nastavení neinteraktivních aplikací najdete v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Také se ujistěte, že jsou správně nastaveny všechny konstanty definované na začátku ukázky.
1. Získá se seznam prostředí, ke kterým má uživatel přístup. Jedno z prostředí se vybere jako prostředí zájmu a další data se pro toto prostředí dotazují.
1. Jako příklad požadavku HTTPS se vyžádají data o dostupnosti pro prostředí, které nás zajímá.
1. Jako příklad požadavku na webový socket se vyžádají agregovaná data o události pro prostředí, které nás zajímá. Data se vyžadují za celý časový rozsah dostupnosti.

> [!NOTE]
> Vzorový kód je k dispozici [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)na adrese.

## <a name="project-dependencies"></a>Závislosti projektu

Přidejte balíčky `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet a `Newtonsoft.Json`.

## <a name="c-example"></a>C#případě

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další postup

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit jednostránkové aplikaci v JavaScriptu](tutorial-create-tsi-sample-spa.md) k Time Series Insights.