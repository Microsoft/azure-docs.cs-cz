---
title: 'Cílových výpočetních prostředí: kde trénovat a nasazovat modely'
titleSuffix: Azure Machine Learning service
description: Určete, kde se mají k trénování nebo nasazení modelu pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806052"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Co jsou cílových výpočetních prostředí ve službě Azure Machine Learning? 

A **cílové výpočetní prostředí** je určené výpočetní prostředků/prostředí, ve kterém jste spustili cvičný skript nebo hostitele vašeho nasazení služby. Toto umístění může být místního počítače nebo cloudové výpočetní prostředek. Použití výpočetních cíle usnadňují později změníte výpočetní prostředí bez nutnosti měnit kód.  

Životní cyklus vývoje typické modelu můžete:
1. Začněte vyvíjet a experimentování na malé množství dat. V této fázi, doporučujeme na místní prostředí (místní počítač nebo virtuální počítač založený na cloudu) jako vaše cílové výpočetní prostředí. 
2. Vertikálně navýšit kapacitu pro větší množství dat nebo distribuované trénování jedním z těchto [cílových výpočetních prostředí školení](#train).  
3. Jakmile je model hotový, ji nasadit do webové hostitelské prostředí nebo pro zařízení IoT s jedním z těchto [cílových výpočetních prostředí nasazení](#deploy).

Výpočetní prostředky, které používáte pro vaše cílových výpočetních prostředí jsou připojeny k [pracovní prostor](concept-workspace.md). Výpočetní prostředky než v místním počítači sdílejí uživatelé pracovního prostoru.

## <a name="train"></a> Cílových výpočetních prostředí pro školení

Služba Azure Machine Learning nabízí různé podporu napříč různými výpočetními prostředky.  Můžete také připojit své vlastní výpočetní prostředek, i když se podpora pro různé scénáře se můžou lišit.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Další informace o [nastavení a použití cílové výpočetní prostředí k tréninku modelu](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Cíle nasazení

Následující výpočetní prostředky je možné k hostování vašich nasazení modelu.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Přečtěte si [kde a jak model nasadit do cílového výpočetního prostředí](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (spravované)

Spravované výpočetní prostředek je vytvořen a spravován společností služby Azure Machine Learning. Tato výpočetní je optimalizována pro úlohy machine learning. Azure Machine Learning Compute je pouze spravovaná výpočetní od 30. května 2019. V budoucnu může přidat další spravované výpočetní prostředky.

Azure Machine Learning Compute můžete použít pro trénování a odvozování batch (Preview).  Tento výpočetní prostředek máte:

* Jeden nebo více node clusteru
* Pokaždé, když odešlete spuštění pravidla automatického škálování provedou 
* Správa automatického clusteru a plánování úloh 
* Podpora pro prostředky procesoru a GPU

Azure Machine Learning Compute instance můžete vytvořit webu Azure Portal, sady SDK nebo rozhraní příkazového řádku. Po vytvoření je automaticky součástí pracovního prostoru na rozdíl od jiných typů cílových výpočetních prostředí.

## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Je cíl nespravované výpočetní *není* spravované službou Azure Machine Learning. Vytvoření tohoto typu cílového výpočetního prostředí mimo Azure Machine Learning a pak ho připojit k vašemu pracovnímu prostoru. Nespravované výpočetní prostředky může vyžadovat další kroky pro vás k údržbě nebo ke zlepšení výkonu pro úlohy s machine learning.

## <a name="next-steps"></a>Další postup

Naučte se:
* [Nastavení cílové výpočetní prostředí k natrénování modelu](how-to-set-up-training-targets.md)
* [Nasazení modelu do cílové výpočetní prostředí](how-to-deploy-and-where.md)