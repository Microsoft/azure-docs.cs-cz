---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484080"
---
Kontejner má následující nastavení konfigurace:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-configuration-setting)|Sleduje fakturační údaje.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidání podpory telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do vašeho kontejneru.|
|Ano|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Eula](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Plynulý](#fluentd-settings)|Zapisuje protokol a volitelně metrická data na server Fluentd.|
|Ne|HTTP Proxy|Konfiguruje proxy server HTTP pro vytváření odchozích požadavků.|
|Ne|[protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET core pro váš kontejner. |
|Ne|[Koně](#mount-settings)|Čte a zapisuje data z hostitelského počítače do kontejneru a z kontejneru zpět do hostitelského počítače.|
