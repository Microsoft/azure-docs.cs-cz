---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67712557"
---
Toto `ApplicationInsights` nastavení umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) . Application Insights poskytuje podrobné monitorování vašeho kontejneru. Svůj kontejner můžete snadno monitorovat z důvodu dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru.

Následující tabulka popisuje nastavení konfigurace podporovaná v `ApplicationInsights` části.

|Vyžadováno| Name | Datový typ | Popis |
|--|------|-----------|-------------|
|No| `InstrumentationKey` | Řetězec | Klíč instrumentace instance Application Insights, do které se odesílají data telemetrie kontejneru. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Příklad:<br>`InstrumentationKey=123456789`|

