---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175275"
---
`ApplicationInsights` Nastavení slouží k přidání [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru. Služba Application Insights nabízí podrobné monitorování vašeho kontejneru. Umožňuje snadné monitorování vašeho kontejneru dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vašem kontejneru.

V následující tabulce jsou popsaná nastavení konfigurace podporované v rámci `ApplicationInsights` oddílu.

|Požaduje se| Název | Typ dat | Popis |
|--|------|-----------|-------------|
|Ne| `InstrumentationKey` | Řetězec | Instrumentační klíč Application Insights instance, do jaké telemetrická data pro kontejner se odesílají. Další informace najdete v tématu [Application Insights pro ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Příklad:<br>`InstrumentationKey=123456789`|

