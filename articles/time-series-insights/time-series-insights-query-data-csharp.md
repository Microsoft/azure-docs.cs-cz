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
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 353e3463c2be552210847ac3fe17bbfe2cec58c7
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958121"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat z Azure Time Series Insightsho prostředí GA pomocíC#

Tento C# příklad ukazuje, jak zadávat dotazy na data z Azure Time Series Insights GA prostředí.

Ukázka ukazuje několik základních příkladů použití rozhraní API pro dotazování:

1. V rámci přípravného kroku získá přístupový token prostřednictvím rozhraní Azure Active Directory API. Tento token předejte v hlavičce `Authorization` každého požadavku rozhraní API pro dotazy. Informace o nastavení neinteraktivních aplikací najdete v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Také se ujistěte, že jsou správně nastaveny všechny konstanty definované na začátku ukázky.
1. Získá se seznam prostředí, ke kterým má uživatel přístup. Jedno z prostředí se vybere jako prostředí zájmu a další data se pro toto prostředí dotazují.
1. Jako příklad požadavku HTTPS se vyžádají data o dostupnosti pro prostředí, které vás zajímá.
1. Jako příklad požadavku webového soketu se pro prostředí zájmu vyžadují data agregace událostí. Data se vyžadují pro celý časový rozsah dostupnosti.

> [!NOTE]
> Vzorový kód je k dispozici na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Závislosti projektu

Přidejte balíčky NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` a `Newtonsoft.Json`.

## <a name="c-example"></a>C#případě

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit jednostránkové aplikaci v JavaScriptu](tutorial-create-tsi-sample-spa.md) k Time Series Insights.