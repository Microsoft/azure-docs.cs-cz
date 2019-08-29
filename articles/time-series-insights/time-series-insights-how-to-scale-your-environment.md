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
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 13fa2b892013cf4a3fb96220c901030c6b0aee0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129090"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Postup škálování Time Series Insightsho prostředí

Tento článek popisuje, jak změnit kapacitu vašeho prostředí Time Series Insights prostředí pomocí Azure Portal. Kapacita je násobitel, který se aplikuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranými SKU.

Azure Portal můžete použít ke zvýšení nebo snížení kapacity v rámci dané cenové jednotky.

Změna SKU cenové úrovně ale není povolená. Například prostředí s cenovou JEDNOTKou S1 se nedá převést na S2, nebo naopak.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Míry příchozího přenosu SKU a kapacitu pro S1

| Kapacita SKU S1 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 1 GB (1 000 000 událostí) | 30 GB (30 000 000 událostí) za měsíc |
| 10 | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Sazby za příchozí skladové jednotky a kapacitu v S2

| Kapacita SKU S2 | Míra příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 10 GB (10 000 000 událostí) | 300 GB (300 000 000 událostí) za měsíc |
| 10 | 100 GB (100 000 000 událostí) | 3 TB (3 000 000 000 událostí) za měsíc |

Kapacity se lineárně škálují, takže SKU S1 s kapacitou 2 podporuje 2 GB (2 000 000) událostí za den a 60 GB (60 000 000 událostí) za měsíc.

## <a name="change-the-capacity-of-your-environment"></a>Změna kapacity vašeho prostředí

1. V Azure Portal vyhledejte a vyberte své Time Series Insights prostředí.

1. V nabídce pro prostředí Time Series Insights vyberte **Konfigurovat**.

   [![konfigurovat. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Nastavením posuvníku **kapacity** můžete vybrat kapacitu, která splňuje požadavky na míry příchozího přenosu dat a kapacitu úložiště. Všimněte si, že míra příchozího **přenosu**dat, **kapacita úložiště**a **Odhadovaná cena** se dynamicky aktualizuje, aby se zobrazil dopad změny.

   [![Směrem](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Alternativně můžete do textového pole napravo od posuvníku zadat číslo multiplikátoru kapacity.

1. Vyberte **Uložit** a škálujte prostředí. Indikátor průběhu se zobrazí, dokud se změna nepotvrdí, a to za chvíli.

## <a name="next-steps"></a>Další postup

- Ověřte, jestli je nová kapacita [dostatečná, aby se zabránilo omezování](time-series-insights-diagnose-and-solve-problems.md).