---
title: Změna velikosti clusteru Azure Stream Analytics
description: Přečtěte si, jak škálovat velikost Azure Stream Analytics clusteru směrem nahoru a dolů.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020022"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Změna velikosti clusteru Azure Stream Analytics

Kapacita Stream Analyticsho clusteru se měří v jednotkách streamování (SU). Více úloh může běžet paralelně ve stejném clusteru, pokud součet služby SUs přiřazené ke všem spuštěným úlohám nepřekročí kapacitu clusteru.

Kapacitu clusteru je možné škálovat nahoru a dolů, aby odpovídala velikosti vašich úloh streamování. Škálování clusteru trvá čas a časté škálování není určené. Doporučujeme, abyste naplánovali a zřídili cluster s přesným počtem služby SUs, kterou máte v plánu využívat.

## <a name="change-the-scale-of-your-cluster"></a>Změna rozsahu clusteru

1. V Azure Portal Najděte cluster Stream Analytics a vyberte ho.

1. V části **Přehled** vyberte možnost **škálovat**. Můžete zjistit, kolik služby SUs je přiřazeno k vašemu clusteru. Pomocí tohoto selektoru můžete podle potřeby zvýšit nebo snížit službu SUs.

   ![škálování clusteru](./media/scale-cluster/scale-cluster.png)

Operace škálování nemá vliv na žádné aktuálně spuštěné úlohy.

## <a name="next-steps"></a>Další kroky

Nyní víte, jak škálovat Stream Analytics clusterů směrem nahoru a dolů. V dalším kroku se můžete seznámit se správou privátních koncových bodů a automatickým škálováním úloh:

* [Správa privátních koncových bodů v clusteru Azure Stream Analytics](private-endpoints.md)
* [Správa úloh v clusteru Azure Stream Analytics](manage-jobs-cluster.md)
