---
title: Co jsou výpočetní cíle
titleSuffix: Azure Machine Learning
description: Definujte, kde chcete trénovat nebo nasazovat váš model pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: f9ca75943eaec2ae018b54145d872fc09294035e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398186"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Co jsou výpočetní cíle v Azure Machine Learning? 

Cíl **výpočetních prostředků** je určený výpočetní prostředek nebo prostředí, ve kterém spustíte školicí skript nebo hostujete nasazení služby. Toto umístění může být místní počítač nebo cloudový výpočetní prostředek. Pomocí výpočetních cílů, aby bylo snadné pro pozdější změnu výpočetního prostředí bez nutnosti měnit kód.  

V typickém životním cyklu vývoje modelu můžete:
1. Začněte vývojem a experimentováním s malým množstvím dat. V této fázi doporučujeme jako výpočetní cíl místní prostředí (místní počítač nebo virtuální počítač na základě cloudu). 
2. Vertikálně navýšit kapacitu na větší data nebo provést distribuované školení pomocí jednoho z těchto [trénovacích výpočetních cílů](#train).  
3. Jakmile je váš model připravený, nasaďte ho do webhostingového prostředí nebo zařízení IoT s jedním z těchto [výpočetních cílů nasazení](#deploy).

Výpočetní prostředky, které používáte pro vaše výpočetní cíle, jsou připojené k [pracovnímu prostoru](concept-workspace.md). Výpočetní prostředky jiné než místní počítač jsou sdíleny uživateli pracovního prostoru.

## <a name="training-compute-targets"></a><a name="train"></a>Trénování výpočetních cílů

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky.  Můžete také připojit vlastní výpočetní prostředek, i když podpora pro různé scénáře se může lišit.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Přečtěte si další informace o [nastavení a používání výpočetního cíle pro trénink modelu](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Cíle nasazení

Následující výpočetní prostředky lze použít k hostování nasazení modelu.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[Zjistěte, kde a jak nasadit model na výpočetní cíl](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Výpočetní prostředky Azure Machine Learning (spravované)

Spravovaný výpočetní prostředek je vytvořený a spravovaný Azure Machine Learning. Tento výpočetní výkon je optimalizovaný pro úlohy strojového učení. Výpočetní clustery Azure Machine Learning a [výpočetní instance](concept-compute-instance.md) jsou jediné spravované výpočetní prostředky. Další spravované výpočetní prostředky mohou být přidány v budoucnu.

Výpočetní instance Azure Machine Learning (preview) nebo výpočetní clustery můžete vytvářet z:
* Azure Machine Learning Studio
* portál Azure
* Třídy [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) sady Python SDK a [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Šablona Resource Manageru

Výpočetní clustery můžete také vytvořit pomocí [rozšíření strojového učení pro azure cli](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training).

Při vytvoření těchto výpočetních prostředků jsou automaticky součástí pracovního prostoru na rozdíl od jiných druhů výpočetních cílů.

### <a name="compute-clusters"></a>Výpočetní clustery

Výpočetní clustery Azure Machine Learning můžete použít pro školení a pro dávkové odvození (náhled).  S tímto výpočetním prostředkem máte:

* Cluster s jedním nebo více uzly
* Automatické škálování při každém odeslání spuštění 
* Automatická správa clusteru a plánování úloh 
* Podpora prostředků CPU i GPU



## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Nespravovaný výpočetní cíl *není* spravovaný Azure Machine Learning. Tento typ výpočetního cíle vytvoříte mimo Azure Machine Learning a pak ho připojíte k pracovnímu prostoru. Nespravované výpočetní prostředky mohou vyžadovat další kroky k údržbě nebo zlepšení výkonu úloh strojového učení.

## <a name="next-steps"></a>Další kroky

Naučte se:
* [Nastavení výpočetního cíle pro trénování modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu na výpočetní cíl](how-to-deploy-and-where.md)