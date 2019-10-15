---
title: Jak škálovat Azure Time Series Insights prostředí | Microsoft Docs
description: Tento článek popisuje, jak škálovat Azure Time Series Insights prostředí. Pomocí Azure Portal můžete přidat nebo odečíst kapacitu v rámci cenové jednotky.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: a899de22137decc1eb1578369a2751710c17abda
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332891"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Postup škálování Time Series Insightsho prostředí

Tento článek popisuje, jak změnit kapacitu Time Series Insights prostředí pomocí [Azure Portal](https://portal.azure.com). Kapacita je násobitel, který se aplikuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranými SKU.

Azure Portal můžete použít ke zvýšení nebo snížení kapacity v rámci dané cenové jednotky.

Změna SKU cenové úrovně ale není povolená. Například prostředí s cenovou JEDNOTKou S1 se nedá převést na S2, nebo naopak.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Míry příchozího přenosu SKU a kapacitu pro S1

| Kapacita SKU S1 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1\. místo | 1 GB (1 000 000 událostí) | 30 GB (30 000 000 událostí) za měsíc |
| 10 | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Sazby za příchozí skladové jednotky a kapacitu v S2

| Kapacita SKU S2 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1\. místo | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |
| 10 | 100 GB (100 000 000 událostí) | 3 TB (3 000 000 000 událostí) za měsíc |

Kapacity se lineárně škálují, takže SKU S1 s kapacitou 2 podporuje 2 GB (2 000 000) událostí za den a 60 GB (60 000 000 událostí) za měsíc.

## <a name="change-the-capacity-of-your-environment"></a>Změna kapacity vašeho prostředí

1. V Azure Portal vyhledejte a vyberte své Time Series Insights prostředí.

1. V nabídce pro prostředí Time Series Insights vyberte **Konfigurovat**.

   [@no__t -1configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Nastavením posuvníku **kapacity** můžete vybrat kapacitu, která splňuje požadavky na míry příchozího přenosu dat a kapacitu úložiště. Všimněte si, že míra příchozího **přenosu**dat, **kapacita úložiště**a **Odhadovaná cena** se dynamicky aktualizuje, aby se zobrazil dopad změny.

   [@no__t – 1Slider](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativně můžete do textového pole napravo od posuvníku zadat číslo multiplikátoru kapacity.

1. Vyberte **Uložit** a škálujte prostředí. Indikátor průběhu se zobrazí, dokud se změna nepotvrdí, a to za chvíli.

1. Ověřte, jestli je nová kapacita [dostatečná, aby se zabránilo omezování](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v informacích o uchování v Time Series Insights](time-series-insights-concepts-retention.md).

- Přečtěte si o [konfiguraci uchovávání dat v Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Seznamte [se s plánováním svého prostředí](time-series-insights-environment-planning.md).