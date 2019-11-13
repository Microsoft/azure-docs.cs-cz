---
title: Jak škálovat prostředí – Azure Time Series Insights | Microsoft Docs
description: Naučte se škálovat Azure Time Series Insights prostředí pomocí Azure Portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: b17cdb2ec27676d5d20d6f12bad309368fe32aa3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006806"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Postup škálování Time Series Insightsho prostředí

Tento článek popisuje, jak změnit kapacitu Time Series Insights prostředí pomocí [Azure Portal](https://portal.azure.com). Kapacita je násobitel, který se aplikuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranými SKU.

Azure Portal můžete použít ke zvýšení nebo snížení kapacity v rámci dané cenové jednotky.

Změna SKU cenové úrovně ale není povolená. Například prostředí s cenovou JEDNOTKou S1 se nedá převést na S2, nebo naopak.

## <a name="ga-limits"></a>Omezení GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Změna kapacity vašeho prostředí

1. V Azure Portal vyhledejte a vyberte své Time Series Insights prostředí.

1. V nabídce pro prostředí Time Series Insights vyberte **Konfigurovat**.

   [![configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Nastavením posuvníku **kapacity** můžete vybrat kapacitu, která splňuje požadavky na míry příchozího přenosu dat a kapacitu úložiště. Všimněte si, že míra příchozího **přenosu**dat, **kapacita úložiště**a **Odhadovaná cena** se dynamicky aktualizuje, aby se zobrazil dopad změny.

   [Posuvník ![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativně můžete do textového pole napravo od posuvníku zadat číslo multiplikátoru kapacity.

1. Vyberte **Uložit** a škálujte prostředí. Indikátor průběhu se zobrazí, dokud se změna nepotvrdí, a to za chvíli.

1. Ověřte, jestli je nová kapacita [dostatečná, aby se zabránilo omezování](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v informacích o uchování v Time Series Insights](time-series-insights-concepts-retention.md).

- Přečtěte si o [konfiguraci uchovávání dat v Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Seznamte [se s plánováním svého prostředí](time-series-insights-environment-planning.md).