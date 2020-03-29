---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712557"
---
Toto `ApplicationInsights` nastavení umožňuje přidat podporu telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontejneru. Application Insights poskytuje podrobné monitorování vašeho kontejneru. Můžete snadno sledovat dostupnost, výkon a využití kontejneru. Můžete také rychle identifikovat a diagnostikovat chyby v kontejneru.

Následující tabulka popisuje nastavení konfigurace podporovaná v části. `ApplicationInsights`

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ne| `InstrumentationKey` | Řetězec | Instrumentace klíč application insights instance, do kterého je odeslána telemetrická data pro kontejner. Další informace naleznete v [tématu Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Příklad:<br>`InstrumentationKey=123456789`|

