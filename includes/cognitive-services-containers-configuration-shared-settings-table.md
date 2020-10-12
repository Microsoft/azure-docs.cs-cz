---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "73484080"
---
Kontejner má následující nastavení konfigurace:

|Vyžadováno|Nastavení|Účel|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Sleduje fakturační informace.|
|No|[ApplicationInsights](#applicationinsights-setting)|Umožňuje přidat do svého kontejneru podporu telemetrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) .|
|Yes|[Fakturace](#billing-configuration-setting)|Určuje identifikátor URI koncového bodu prostředku služby v Azure.|
|Yes|[Konkrétní](#eula-setting)| Označuje, že jste přijali licenci pro kontejner.|
|No|[Fluent](#fluentd-settings)|Zapisuje protokol a volitelně také data metriky na server, který je na něj.|
|No|Proxy server HTTP|Konfiguruje proxy server HTTP pro vytváření odchozích požadavků.|
|No|[Protokolování](#logging-settings)|Poskytuje podporu protokolování ASP.NET Core pro váš kontejner. |
|No|[Připojí](#mount-settings)|Přečte a zapisuje data z hostitelského počítače do kontejneru a z kontejneru zpátky do hostitelského počítače.|
