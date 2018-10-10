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
ms.openlocfilehash: 05958f35f80a53da27e020d367799519ef5a9bd7
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901579"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Funkce podporované Application Insights pro službu Azure Functions

Níže je seznam aktuálně podporovaných funkcí pro [integrace Application Insights s využitím Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring). Přečtěte si průvodce Azure Functions pro [Začínáme](https://github.com/Azure/Azure-Functions/wiki/App-Insights).


| Azure Functions                       | V1                | V2 (2018 ke konferenci Ignite)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights SDK pro .NET**         | **2.5.0**             | **2.7.2**                 |
| | | | 
| **Automatické shromažďování**              |                   |                   |               
| &bull; Požadavky                           | Ano               | Ano               | 
| &bull; Výjimky                         | Ano               | Ano               | 
| &bull; Závislosti               |                   |                   |               
| &mdash; HTTP                              |                   | Ano               | 
| &mdash; služby Service Bus                        |                   | Ano               | 
| &mdash; centra událostí                          |                   | Ano               | 
| &mdash; SQL                               |                   | Ano               | 
| | | | 
| **Podporované funkce**                    |                   |                   |               
| &bull; QuickPulse/LiveMetrics                         | Ano               | Ano               | 
| &bull; Vzorkování                           | Ano               | Ano               | 
| &bull; Prezenční signály                         |       | Ano               | 
| | | | 
| **Korelace**                           |                   |                   |               
| &bull; služby Service Bus                         |                   | Ano               | 
| &bull; centra událostí                           |                   | Ano               | 
| | | | 
| **Konfigurovatelné**                  |                   |                   |           
| &bull;Plně konfigurovatelné.<br/>Zobrazit [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) pokyny.<br/>Zobrazit [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) pro všechny možnosti.               |                   | Ano                   | 
