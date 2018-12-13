---
title: Škálování Azure Time Series Insights – postupy škálování prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak škálovat vaše prostředí Azure Time Series Insights. Pomocí webu Azure portal můžete přidávat nebo odebírat kapacity v rámci cenové SKU.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: 1adb93d935e16bdce23e35e306592a9575967452
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271857"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak škálovat vaše prostředí Time Series Insights

Tento článek popisuje, jak změnit kapacitu prostředí pro vaše prostředí Time Series Insights pomocí webu Azure portal. Kapacita je multiplikátor na rychlost příchozího přenosu dat, kapacitu a náklady spojené s vybraná skladová položka. 

Pro zvýšení nebo snížení kapacity v rámci dané skladové položky cen, můžete použít na webu Azure portal. 

Však změnit cenovou úroveň nepovoluje SKU. Například v prostředí s S1, ceny SKU nelze převést na S2 nebo naopak. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>Sazby za příchozí přenos dat skladovou Položku S1 a kapacity

| S1 Kapacita skladové položky | Rychlost příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 1 GB (1 milion událostí) | 30 GB (30 milionů událostí) za měsíc |
| 10 | 10 GB (10 milionů událostí) | 300 GB (300 milionů událostí) za měsíc |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Sazby za příchozí přenos dat s2 SKU a kapacity

| S2 Kapacita skladové položky | Rychlost příchozího přenosu dat | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 10 GB (10 milionů událostí) | 300 GB (300 milionů událostí) za měsíc |
| 10 | 100 GB (100 milionů událostí) | 3 TB (3 miliardy událostí) za měsíc |

Kapacity se škálují lineárně, takže S1 SKU s kapacitou 2 podporuje událostí (2 miliony) 2 GB za den rychlost příchozího přenosu dat a 60 GB (60 milionů událostí) za měsíc.

## <a name="change-the-capacity-of-your-environment"></a>Změnit kapacitu vašeho prostředí
1. Na webu Azure Portal vyhledejte a vyberte vaše prostředí Time Series Insights. 

2. V nabídce pro vaše prostředí čas řady Insighs vyberte **konfigurovat**.

   ![Configure.PNG](media/scale-your-environment/configure.png)

3. Upravit **kapacity** posuvníku vyberte kapacity, který splňuje požadavky pro vaše sazby za příchozí přenos dat a úložiště. Všimněte si, že **rychlost příchozího přenosu dat**, **kapacitu úložiště**, a **odhadované náklady** aktualizace dynamicky zobrazíte závažnosti dopadu změn. 

   ![Posuvník](media/scale-your-environment/slider.png)

   Alternativně můžete zadat počet multiplikátor kapacity do textového pole vpravo od jezdce. 

4. Vyberte **Uložit** škálování prostředí. Indikátor průběhu se nezobrazí, dokud se změna se potvrdí, okamžité. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Ověřte, zda je dostatečné k zabránění omezování novou kapacitu](time-series-insights-diagnose-and-solve-problems.md).
