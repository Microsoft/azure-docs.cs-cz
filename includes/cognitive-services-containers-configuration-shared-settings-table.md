---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 5228e4986993f792dceb7324bb6ba9f836f29aea
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212985"
---
Tento kontejner má následující nastavení:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-configuration-setting)|Lze sledovat fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) podporu telemetrická data do kontejneru.|
|Ano|[Billing](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Fluentd](#fluentd-settings)|Zápis protokolu a volitelně data metriky Fluentd server.|
|Ne|[Http Proxy](#http-proxy-credentials-settings)|Konfigurace proxy serveru HTTP pro provedení odchozích požadavků.|
|Ne|[Logging](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Ne|[Mounts](#mount-settings)|Čtení a zápis dat z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|
