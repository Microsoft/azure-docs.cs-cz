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
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3729bedf7591ffecc558b88660486f7e336fa717
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705899"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dotazování dat z Azure Time Series Insightsho prostředí GA pomocíC#

Tento C# příklad ukazuje, jak zadávat dotazy na data z Azure Time Series Insights GA prostředí.

V ukázce najdete několik základních příkladů použití rozhraní API pro dotazy:

1. V rámci přípravného kroku získá přístupový token prostřednictvím rozhraní Azure Active Directory API. Tento token předejte v hlavičce `Authorization` každé žádosti rozhraní API pro dotazování. Informace o nastavení neinteraktivních aplikací najdete v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Také se ujistěte, že jsou správně nastaveny všechny konstanty definované na začátku ukázky.
1. Získá se seznam prostředí, ke kterým má uživatel přístup. Jedno z prostředí se vybere jako prostředí zájmu a další data se pro toto prostředí dotazují.
1. Jako příklad požadavku HTTPS se vyžádají data o dostupnosti pro prostředí, které nás zajímá.
1. Jako příklad požadavku na webový socket se vyžádají agregovaná data o události pro prostředí, které nás zajímá. Data se vyžadují za celý časový rozsah dostupnosti.

> [!NOTE]
> Vzorový kód je k dispozici na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Závislosti projektu

Přidejte `Microsoft.IdentityModel.Clients.ActiveDirectory` a `Newtonsoft.Json`balíčky NuGet.

## <a name="c-example"></a>C#případě

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Další kroky

- Další informace o dotazování najdete v referenčních informacích k [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Přečtěte si, jak [připojit aplikaci JavaScriptu pomocí klientské sady SDK](https://github.com/microsoft/tsiclient) pro Time Series Insights.