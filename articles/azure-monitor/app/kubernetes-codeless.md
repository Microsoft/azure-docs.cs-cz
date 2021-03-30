---
title: Monitorování aplikací ve službě Azure Kubernetes Service (AKS) s využitím Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor se bezproblémově integruje s vaší aplikací běžící na Kubernetes a umožňuje v žádné době vymezit problémy s vašimi aplikacemi.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87075307"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Nulová monitoring aplikace instrumentace pro Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  V současné době můžete povolit monitorování pro aplikace Java běžící na Kubernetes bez instrumentace kódu – použijte [samostatného agenta Java](./java-in-process-agent.md). Řešení pro plynulé povolování monitorování aplikací je v sadě Works pro jiné jazyky, a to pomocí sad SDK monitorovat aplikace běžící na AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScriptu](./javascript.md)a [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorování aplikací bez instrumentace kódu
V současné době vám jenom Java umožňuje povolit monitorování aplikací bez instrumentace kódu. Chcete-li monitorovat aplikace v jiných jazycích, použijte sady SDK. 

## <a name="java"></a>Java
Po povolení bude agent Java automaticky shromažďovat velké množství požadavků, závislostí, protokolů a metrik z nejčastěji používaných knihoven a architektur.

Postupujte podle [podrobných pokynů](./java-in-process-agent.md) pro monitorování aplikací Java spuštěných v aplikacích Kubernetes a dalších prostředích. 

## <a name="other-languages"></a>Další jazyky

Pro aplikace v jiných jazycích teď doporučujeme používat sady SDK:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure monitor](../overview.md) a [Application Insights](./app-insights-overview.md)
* Získejte přehled o [distribuovaném trasování](./distributed-tracing.md) a podívejte se, co [Mapa aplikace](./app-map.md?tabs=net) může pro vaši firmu dělat.
