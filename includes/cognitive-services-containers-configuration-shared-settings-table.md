---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484080"
---
Kontejner má následující nastavení konfigurace:

|Požaduje se|Nastavení|Účel|
|--|--|--|
|Ano|[ApiKey](#apikey-configuration-setting)|Sleduje fakturační informace.|
|Ne|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) .|
|Ano|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Ano|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|Ne|[Fluent](#fluentd-settings)|Zapisuje protokol a volitelně také data metriky na server, který je na něj.|
|Ne|Proxy server HTTP|Konfiguruje proxy server HTTP pro vytváření odchozích požadavků.|
|Ne|[protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|Ne|[Připojí](#mount-settings)|Přečte a zapisuje data z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|
