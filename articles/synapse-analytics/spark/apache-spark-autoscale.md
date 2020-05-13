---
title: Automatické škálování Apache Spark instancí Azure synapse
description: Automatické škálování Apache Spark instancí pomocí funkce automatického škálování Azure synapse
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210511"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Automatické škálování Apache Sparkch fondů Azure synapse Analytics

Funkce automatického škálování fondu Azure synapse Spark automaticky škáluje počet uzlů v instanci clusteru směrem nahoru a dolů. Během vytváření nového fondu Azure synapse Spark je možné nastavit minimální a maximální počet uzlů, pokud je vybraná možnost automatické škálování. Automatické škálování pak monitoruje požadavky na prostředky pro zatížení a škáluje počet uzlů nahoru nebo dolů. Pro tuto funkci se neúčtují žádné další poplatky.

## <a name="metrics-monitoring"></a>Monitorování metrik

Automatické škálování nepřetržitě monitoruje instanci Spark a shromažďuje následující metriky:

|Metrika|Popis|
|---|---|
|Celkový počet vyřízených PROCESORů|Celkový počet jader potřebných ke spuštění provádění všech nevyřízených uzlů.|
|Celkový počet nevyřízených paměti|Celková paměť (v MB) požadovaná k zahájení provádění všech nevyřízených uzlů.|
|Celkový bezplatný procesor|Součet všech nevyužitých jader na aktivních uzlech.|
|Celková volná paměť|Součet nevyužité paměti (v MB) na aktivních uzlech.|
|Využitá paměť na uzel|Zatížení uzlu. Uzel, na kterém se používá 10 GB paměti, se považuje za větší zatížení než pracovní proces se 2 GB využité paměti.|

Výše uvedené metriky se kontrolují každých 30 sekund. Automatické škálování provádí rozhodování na úrovni horizontálního škálování a horizontálního škálování na základě těchto metrik.

## <a name="load-based-scale-conditions"></a>Podmínky škálování založené na zatížení

Při zjištění následujících podmínek bude automatické škálování vydávat požadavek na škálování:

|Vertikální navýšení kapacity|Horizontální navýšení kapacity|
|---|---|
|Celkový počet vyřízených PROCESORů je větší než 1 minuta a celkový počet volných PROCESORů.|Celkový počet nevyřízených PROCESORů je menší než celkový bezplatný procesor po dobu delší než 2 minuty.|
|Celkový počet nevyřízených paměti je větší než celková volná paměť více než 1 minuta.|Celková paměť, která čeká na vyřízení, je menší než celková volná paměť po dobu více než 2 minut.|

Pro horizontální navýšení kapacity služba Azure synapse AutoScale počítá, kolik nových uzlů je potřeba pro splnění aktuálních požadavků na procesor a paměť, a pak vydá požadavek na horizontální navýšení kapacity k přidání požadovaného počtu uzlů.

Pro horizontální navýšení kapacity na základě počtu prováděcích modulů, hlavních aplikací na uzel a aktuálních požadavků na procesor a paměť, automatické škálování vydává požadavek na odebrání určitého počtu uzlů. Služba také detekuje, které uzly jsou kandidáty na odebrání na základě aktuálního spuštění úlohy. Operace horizontálního snížení kapacity nejprve vyřadí uzly z provozu a pak je z clusteru odebere.

## <a name="get-started"></a>Začínáme

### <a name="create-a-spark-pool-with-autoscaling"></a>Vytvoření fondu Spark s automatickým škálováním

Pokud chcete povolit funkci automatického škálování, proveďte v rámci procesu normálního vytváření fondu následující kroky:

1. Na kartě **základy** zaškrtněte políčko **Povolit automatické škálování** .
1. Zadejte požadované hodnoty pro následující vlastnosti:  

    * **Minimální** počet uzlů.
    * **Maximální** počet uzlů

Počáteční počet uzlů bude minimální. Tato hodnota určuje počáteční velikost instance při jejím vytvoření. Minimální počet uzlů nemůže být menší než tři.

## <a name="best-practices"></a>Osvědčené postupy

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Zvažte latenci operací horizontálního navýšení nebo snížení kapacity.

Dokončení operace škálování může trvat 1 až 5 minut.

### <a name="preparation-for-scaling-down"></a>Příprava na horizontální navýšení kapacity

Během procesu horizontálního navýšení kapacity se automatické škálování umístí uzly do stavu vyřazení z provozu, aby na tomto uzlu nemohly být spuštěny žádné nové prováděcí moduly.

Běžící úlohy budou i nadále spouštěny a dokončeny. Čekající úlohy budou čekat na plánování jako normální s menším počtem dostupných uzlů.

## <a name="next-steps"></a>Další kroky

Rychlý Start k nastavení nového fondu Spark [Vytvoření fondu Spark](..\quickstart-create-apache-spark-pool.md)
