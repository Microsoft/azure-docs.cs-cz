---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116763"
---
Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-configuration-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Billing](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|Http Proxy|Konfigurace proxy serveru HTTP pro provedení odchozích požadavků.|
|Ne|[Logging](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Ne|[Mounts](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|
