---
title: Postup škálování Azure časové řady Přehled prostředí | Microsoft Docs
description: Tento článek popisuje postup škálování prostředí Statistika Azure časové řady. Použití portálu Azure přidejte nebo odstraňte kapacity v rámci cenovou SKU.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 7e603cc9c130de6b65ae1935ac974557f5e74737
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651987"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Postup škálování prostředí Statistika časové řady

Tento článek popisuje, jak změnit kapacitu prostředí prostředí časové řady statistika pomocí portálu Azure. Kapacita je násobitel použita pro příchozí míra, kapacity úložiště a náklady spojené s vaší vybrané SKU. 

Na portálu Azure můžete zvýšit nebo snížit kapacitu v rámci dané cenovou SKU. 

Ale při změně cenové úrovně není povolená SKU. Například v prostředí s S1 ceny SKU nelze převést do S2 nebo naopak. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>Příjem příchozích dat sazby S1 SKU a kapacity

| S1 Kapacita SKU | Příchozí míra | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 1 GB (1 milion událostí) | (30 milionů událostí) 30 GB za měsíc |
| 10 | 10 GB (10 milionů událostí) | 300 GB za měsíc (300 milión událostí) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU příjem příchozích dat rychlostí a kapacity

| S2 Kapacita SKU | Příchozí míra | Maximální kapacita úložiště
| --- | --- | --- |
| 1 | 10 GB (10 milionů událostí) | 300 GB za měsíc (300 milión událostí) |
| 10 | 100 GB (100 miliónů událostí) | 3 TB (3 miliardy událostí) za měsíc |

Kapacity lineárně, takže S1 SKU s kapacitou 2 podporuje 2 GB (2 miliony) událostí za den příchozího rychlost a 60 GB (60 milión událostí) za měsíc.

## <a name="change-the-capacity-of-your-environment"></a>Změna kapacity prostředí
1. Na portálu Azure vyhledejte a vyberte prostředí Statistika časové řady. 

2. V nabídce pro vaše prostředí časové řady Insighs vyberte **konfigurace**.

   ![Configure.PNG](media/scale-your-environment/configure.png)

3. Upravit **kapacity** jezdce vyberte kapacitu, který splňuje požadavky pro příjem příchozích dat sazby a kapacitu úložiště. Upozornění **příchozí míra**, **kapacity úložiště**, a **odhadované náklady na** aktualizace dynamicky zobrazíte dopadu změn. 

   ![Posuvník](media/scale-your-environment/slider.png)

   Alternativně můžete zadat počet násobitel kapacity do textového pole vpravo od jezdce. 

4. Vyberte **Uložit** škálování prostředí. Dokud nebude změna se potvrdí, na okamžik, zobrazí se indikátor průběhu. 

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Ověřte, zda je dostačující k zabránění omezení nové kapacity](time-series-insights-diagnose-and-solve-problems.md).
