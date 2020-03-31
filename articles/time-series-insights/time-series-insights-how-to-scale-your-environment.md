---
title: Jak škálovat prostředí – Azure Time Series Insights| Dokumenty společnosti Microsoft
description: Zjistěte, jak škálovat prostředí Azure Time Series Insights pomocí portálu Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310981"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak škálovat prostředí Time Series Insights

Tento článek popisuje, jak změnit kapacitu prostředí Time Series Insights pomocí [portálu Azure](https://portal.azure.com). Kapacita je násobitel použitý pro rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranou skladovou položkou.

Portál Azure můžete použít ke zvýšení nebo snížení kapacity v rámci dané cenové skladové položky.

Změna skladové položky cenové úrovně však není povolena. Například prostředí s skladovou položkou s cenami S1 nelze převést na S2 nebo naopak.

## <a name="ga-limits"></a>Limity GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Změna kapacity prostředí

1. Na webu Azure Portal vyhledejte a vyberte prostředí Time Series Insights.

1. V nabídce prostředí Time Series Insights vyberte **Možnost Konfigurace úložiště**.

   [![Konfigurace kapacity Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Nastavte jezdec **Kapacita** a vyberte kapacitu, která splňuje požadavky na rychlost příchozího přenosu dat a kapacitu úložiště. Všimněte si **rychlosti příchozího přenosu dat**, **kapacity úložiště**a aktualizace **odhadovaných nákladů** dynamicky, abyste zobrazili dopad změny.

   [![Konfigurace prostředí pomocí posuvníku kapacity](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Případně můžete zadat číslo násobícího kapacity do textového pole napravo od posuvníku.

1. Chcete-li **škálovat prostředí,** vyberte uložit. Indikátor průběhu se zobrazí, dokud není změna potvrzena, na okamžik.

1. Ověřte, zda je nová kapacita [dostatečná k zabránění omezení](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Další kroky

- Další informace naleznete [v nápovědě k principu uchovávání informací v přehledech time series .](time-series-insights-concepts-retention.md)

- Přečtěte si o [konfiguraci uchovávání dat v Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Další informace o [plánování prostředí](time-series-insights-environment-planning.md).