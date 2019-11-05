---
title: Dotazování dat z prostředí Azure Time Series Insights Preview pomocí C# kódu | Microsoft Docs
description: Tento článek popisuje, jak zadávat dotazy na data z Azure Time Series Insights prostředí pomocí kódování vlastní aplikace napsané v C# jazyce .NET (C-Sharp).
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/08/2019
ms.custom: seodec18
ms.openlocfilehash: 46ade3ed6e8712a074974c81e51b2dd6c834db26
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989668"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Dotazování dat z prostředí Azure Time Series Insights Preview pomocíC#

Tento C# příklad ukazuje, jak zadávat dotazy na data z prostředí Azure Time Series Insights Preview.

V ukázce najdete několik základních příkladů použití rozhraní API pro dotazy:

1. V rámci přípravného kroku získá přístupový token prostřednictvím rozhraní Azure Active Directory API. Tento token předejte v hlavičce `Authorization` každé žádosti rozhraní API pro dotazování. Informace o nastavení neinteraktivních aplikací najdete v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Také se ujistěte, že jsou správně nastaveny všechny konstanty definované na začátku ukázky.
1. Získá se seznam prostředí, ke kterým má uživatel přístup. Jedno z prostředí se vybere jako prostředí zájmu a další data se pro toto prostředí dotazují.
1. Jako příklad požadavku HTTPS se vyžádají data o dostupnosti pro prostředí, které nás zajímá.
1. Jako příklad požadavku na webový socket se vyžádají agregovaná data o události pro prostředí, které nás zajímá. Data se vyžadují za celý časový rozsah dostupnosti.

> [!NOTE]
> Tento vzorový kód je také k dispozici na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#případě

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> Výše uvedený vzor kódu se dá spustit bez změny výchozích hodnot prostředí.

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Time Series Insights.