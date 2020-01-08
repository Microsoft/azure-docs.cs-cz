---
title: Co jsou výpočetní cíle
titleSuffix: Azure Machine Learning
description: Určete, kam chcete model vyškolit nebo nasadit pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 58df0fe6a598d97e0a9d985052cf3a8c198eb4cc
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541332"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Jaké jsou výpočetní cíle v Azure Machine Learning? 

**Cíl služby COMPUTE** je určený výpočetní prostředek nebo prostředí, ve kterém spouštíte školicí skript nebo hostuje nasazení služby. Toto umístění může být váš místní počítač nebo cloudový výpočetní prostředek. Použití výpočetních cílů usnadňuje pozdější změnu prostředí COMPUTE, aniž byste museli měnit kód.  

Typický životní cyklus vývoje modelu vám může:
1. Začněte vývojem a experimentováním s malým množstvím dat. V této fázi doporučujeme jako cíl výpočtů použít místní prostředí (místní počítač nebo cloudový virtuální počítač). 
2. Můžete škálovat až na větší objem dat nebo distribuované školení pomocí jednoho z těchto [školicích cílů](#train).  
3. Jakmile je model připravený, nasaďte ho do prostředí pro hostování webů nebo zařízení IoT s jedním z těchto [výpočetních cílů nasazení](#deploy).

Výpočetní prostředky, které používáte pro cíle výpočtů, jsou připojené k [pracovnímu prostoru](concept-workspace.md). Výpočetní prostředky jiné než místní počítač sdílí uživatelé pracovního prostoru.

## <a name="train"></a>Školení výpočetních cílů

Azure Machine Learning má různou podporu v různých výpočetních prostředcích.  Můžete také připojit vlastní výpočetní prostředek, i když se podpora různých scénářů může lišit.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Přečtěte si další informace o [nastavení a používání výpočetního cíle pro školení modelů](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cíle nasazení

K hostování nasazení modelu lze použít následující výpočetní prostředky.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Naučte se [, jak a jak model nasadit do cílového výpočetního prostředí](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning COMPUTE (spravované)

Spravovaný výpočetní prostředek je vytvořený a spravovaný pomocí Azure Machine Learning. Tato výpočetní prostředí jsou optimalizovaná pro úlohy strojového učení. Jediným spravovaným výpočetním prostředím jsou Azure Machine Learning výpočetní clustery a [výpočetní instance](concept-compute-instance.md) . V budoucnu může být přidáno více spravovaných výpočetních prostředků.

Můžete vytvořit Azure Machine Learning výpočetních instancí (Preview) nebo výpočetních clusterů v nástroji:

| | Azure Machine Learning Studio | Portál Azure | SDK | Šablona Resource Manageru | Rozhraní příkazového řádku |
|---| ----- | ----- | ----- | ----- | ----- |
| Instance služby Compute | ano | ano | ano | ano |  |
| Výpočtový cluster | ano | ano | ano | ano | ano |

Po vytvoření těchto výpočetních prostředků se tento pracovní prostor automaticky dojedná na rozdíl od jiných druhů výpočetních cílů.

> [!NOTE]
> Výpočetní instance jsou k dispozici pouze pro pracovní prostory s oblastí **střed USA – sever** nebo **Velká Británie – jih**.
>Pokud je váš pracovní prostor v jakékoli jiné oblasti, můžete místo toho vytvořit a použít [virtuální počítač s poznámkovým blokem](concept-compute-instance.md#notebookvm) . 

### <a name="compute-clusters"></a>Výpočetní clustery

Můžete použít výpočetní clustery Azure Machine Learning pro školení a pro Batch Inferencing (Preview).  S tímto výpočetním prostředkem máte tyto:

* Cluster s jedním nebo několika uzly
* Automatické škálování pokaždé, když odešlete běh 
* Automatická správa clusteru a plánování úloh 
* Podpora pro prostředky CPU a GPU



## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Nespravovaný cíl výpočetní služby není *spravován nástrojem* Azure Machine Learning. Tento typ cíle výpočetní služby vytvoříte mimo Azure Machine Learning a pak ho připojíte k pracovnímu prostoru. Nespravované výpočetní prostředky můžou vyžadovat další kroky, abyste mohli udržovat nebo zvýšit výkon úloh strojového učení.

## <a name="next-steps"></a>Další kroky

Získáte informace o těchto tématech:
* [Nastavení cílového výpočetního prostředí pro výuku modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu do cíle služby COMPUTE](how-to-deploy-and-where.md)