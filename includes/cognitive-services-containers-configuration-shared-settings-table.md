---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479007"
---
Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Fakturace](#billing-setting)|Určuje identifikátor URI prostředku služby koncového bodu v Azure.|
|Ano|[Smlouva EULA](#eula-setting)| Označuje, že jste přijali licenční pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Konfigurace proxy serveru HTTP pro provedení odchozích požadavků.|
|Ne|[Protokolování](#logging-settings)|Poskytuje podporu pro váš kontejner protokolování ASP.NET Core. |
|Ne|[Připojí](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru však zpět na hostitelském počítači.|
