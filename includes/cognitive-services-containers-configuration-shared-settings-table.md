---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96006865"
---
Kontejner má následující nastavení konfigurace:

|Vyžadováno|Nastavení|Účel|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Sleduje fakturační informace.|
|No|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](/azure/application-insights) .|
|Yes|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Yes|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|No|[Fluent](#fluentd-settings)|Zapisuje protokol a volitelně také data metriky na server, který je na něj.|
|No|Proxy server HTTP|Konfiguruje proxy server HTTP pro vytváření odchozích požadavků.|
|No|[Protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|No|[Připojí](#mount-settings)|Přečte a zapisuje data z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|