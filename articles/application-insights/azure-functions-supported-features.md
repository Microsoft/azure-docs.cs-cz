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
ms.devlang: multiple
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 0f4eaaefb7d2080218e19574621a4962e61057c3
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394302"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funkce podporované Application Insights pro službu Azure Functions

Azure Functions nabízí [vestavěná integrace](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) pomocí Application Insights, která je dostupná přes rozhraní objektu ILogger. Níže je seznam aktuálně podporovaných funkcí. Přečtěte si průvodce Azure Functions pro [Začínáme](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Podporované funkce

| Azure Functions                       | V1                | V2 (2018 ke konferenci Ignite)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights SDK pro .NET**   | **2.5.0**       | **2.7.2**         |
| | | | 
| **Automatické shromažďování**        |                 |                   |               
| &bull; Požadavky                     | Ano             | Ano               | 
| &bull; Výjimky                   | Ano             | Ano               | 
| &bull; Závislosti                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; služby Service Bus|                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; centra událostí  |                 | Ano               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ano               | 
| | | | 
| **Podporované funkce**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ano             | Ano               | 
| &bull; Vzorkování                     | Ano             | Ano               | 
| &bull; Prezenční signály                   |                 | Ano               | 
| | | | 
| **Korelace**                       |                   |                   |               
| &bull; služby Service Bus                     |                   | Ano               | 
| &bull; centra událostí                       |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                      |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Zobrazit [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pokyny.<br/>Zobrazit [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pro všechny možnosti.               |                   | Ano                   | 


## <a name="sampling"></a>Vzorkování

Služba Azure Functions umožňuje v jejich konfigurace ve výchozím nastavení vzorkování. Další informace najdete v tématu [konfigurace vzorkování](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).
