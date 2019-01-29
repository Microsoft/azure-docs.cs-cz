---
title: Funkce podporované služby Azure Application Insights – služba Azure Functions | Dokumentace Microsoftu
description: Funkce podporované Application Insights pro službu Azure Functions
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 06feece050835b2b9188eb702210770b44a6b49c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185803"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funkce podporované Application Insights pro službu Azure Functions

Azure Functions nabízí [vestavěná integrace](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) pomocí Application Insights, která je dostupná přes rozhraní objektu ILogger. Níže je seznam aktuálně podporovaných funkcí. Přečtěte si průvodce Azure Functions pro [Začínáme](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Podporované funkce

| Azure Functions                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Automatické shromažďování**        |                 |                   |               
| &bull; Požadavky                     | Ano             | Ano               | 
| &bull; Výjimky                   | Ano             | Ano               | 
| &bull; Závislosti                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ano               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ano             | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; Zabezpečený kanál ovládacího prvku|                 | Ano               | 
| &bull; Vzorkování                     | Ano             | Ano               | 
| &bull; Prezenční signály                   |                 | Ano               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ano               | 
| &bull; EventHub                       |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Zobrazit [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pokyny.<br/>Zobrazit [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pro všechny možnosti.               |                   | Ano                   | 


## <a name="live-metrics--secure-control-channel"></a>Živé metriky a zabezpečený kanál ovládacího prvku

Vlastní kritéria filtry, které jste zadali odesílají zpět do komponenty Live Metrics v Application Insights SDK. Filtry můžou potenciálně obsahovat citlivé informace, jako je například customerIDs. Kanál můžete zabezpečit pomocí tajného klíče rozhraní API. Zobrazit [zabezpečený kanál ovládací prvek](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) pokyny.

## <a name="sampling"></a>Vzorkování

Služba Azure Functions umožňuje v jejich konfigurace ve výchozím nastavení vzorkování. Další informace najdete v tématu [konfigurace vzorkování](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
