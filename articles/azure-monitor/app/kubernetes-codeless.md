---
title: Monitorování aplikací ve službě Azure Kubernetes Service (AKS) s využitím Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor hladce se integruje s vaší aplikací běžící na Kubernetes a umožňuje vám monitorovat problémy s aplikacemi v žádné době.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: aaca07febedc609fb0dc9b9ca0e536510f9ca8d1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704517"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Nulová monitoring aplikace instrumentace pro Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  V současné době můžete povolit monitorování pro aplikace Java běžící na Kubernetes bez instrumentace kódu – použijte [samostatného agenta Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Řešení pro plynulé povolování monitorování aplikací je v sadě Works pro jiné jazyky, a to pomocí sad SDK monitorovat aplikace běžící na AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node. js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)a [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorování aplikací bez instrumentace kódu
V současné době vám jenom Java umožňuje povolit monitorování aplikací bez instrumentace kódu. Chcete-li monitorovat aplikace v jiných jazycích, použijte sady SDK. 

## <a name="java"></a>Java
Po povolení bude agent Java automaticky shromažďovat velké množství požadavků, závislostí, protokolů a metrik z nejčastěji používaných knihoven a architektur.

Postupujte podle [podrobných pokynů](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) pro monitorování aplikací Java spuštěných v aplikacích Kubernetes a dalších prostředích. 

## <a name="other-languages"></a>Jiné jazyky

Pro aplikace v jiných jazycích teď doporučujeme používat sady SDK:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Získejte přehled o [distribuovaném trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) a podívejte se, co [Mapa aplikace](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) může pro vaši firmu dělat.