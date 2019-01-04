---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 57e54c4c3b8ae44d42051c87356f1feeaa5a3f10
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977204"
---
Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Fakturace](#billing-setting)|Určuje identifikátor URI prostředku služby koncového bodu v Azure.|
|Ano|[Smlouva EULA](#eula-setting)| Označuje, že jste přijali licenční pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Protokolování](#logging-settings)|Poskytuje podporu pro váš kontejner protokolování ASP.NET Core. |
|Ne|[Připojí](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru však zpět na hostitelském počítači.|
