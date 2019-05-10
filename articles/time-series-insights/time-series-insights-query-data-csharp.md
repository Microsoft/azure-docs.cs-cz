---
title: Dotazování dat z prostředí Azure čas Series Insights GA pomocí C# kódu | Dokumentace Microsoftu
description: Tento článek popisuje, jak dotazovat data z prostředí Azure Time Series Insights pomocí kódování vlastní aplikace napsané v jazyce C# (C sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8b8d47b04d7d0b93bc699064ee414bf4429c4a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510196"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat pomocí prostředí Azure čas Series Insights GAC#

To C# příklad ukazuje, jak zadávat dotazy na data z prostředí Azure čas Series Insights všeobecné dostupnosti. 

V ukázce najdete několik základních příkladů použití rozhraní API pro dotazy:

1. Jako krok přípravy získejte přístupový token prostřednictvím rozhraní API Azure Active Directory. Předá token `Authorization` záhlaví každého požadavku rozhraní API pro dotazy. Nastavení jako neinteraktivní aplikace, najdete v části [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Také se ujistěte, že jsou správně nastavené všechny konstanty definované na začátku vzorku.
1. Je-li získat seznam prostředí, která má uživatel přístup k. Jedno z prostředí se vybere jako prostředí, které vás zajímají, a další data se dotazují pro toto prostředí.
1. Jako příklad požadavku HTTPS se vyžádají data o dostupnosti pro prostředí, které nás zajímá.
1. Jako příklad požadavku na webový socket se vyžádají agregovaná data o události pro prostředí, které nás zajímá. Data se vyžadují za celý časový rozsah dostupnosti.

> [!NOTE]
> Ukázkový kód je k dispozici na [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-ga-preview-sample).

## <a name="project-dependencies"></a>Závislosti projektu

Přidání balíčků NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` a `Newtonsoft.Json`.

## <a name="c-example"></a>Příklad jazyka C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další postup

- Další informace o vytváření dotazů najdete v článku [referenční dokumentace rozhraní API pro dotazy](/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak k [připojení jednostránkové aplikace JavaScript](tutorial-create-tsi-sample-spa.md) pro Time Series Insights.