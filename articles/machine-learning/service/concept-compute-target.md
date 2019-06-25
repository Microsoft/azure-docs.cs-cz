---
title: Cílových výpočetních prostředí
titleSuffix: Azure Machine Learning service
description: Cílové výpočetní prostředí vám umožňují určit výpočetních prostředků, ve kterém jste spustili cvičný skript nebo hostitele vašeho nasazení služby. Toto umístění může být místního počítače nebo cloudové výpočetní prostředek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755347"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Co je cílové výpočetní prostředí ve službě Azure Machine Learning? 

Cílové výpočetní prostředí vám umožňují určit výpočetních prostředků, ve kterém jste spustili cvičný skript nebo hostitele vašeho nasazení služby. Toto umístění může být místního počítače nebo cloudové výpočetní prostředek.

Změna výpočetní prostředí bez úpravy kódu usnadňují cílových výpočetních prostředí.  Životní cyklus vývoje typické modelu:

* Začněte s dev/experimentování na malé množství dat ve službě. V této fázi doporučujeme používat místní prostředí. Například místního počítače nebo virtuálního počítače založené na cloudu.
* Vertikálně navýšit kapacitu trénování na větších datových sad, nebo distribuované trénování jedním z [školení cíle](#train).  
* Nasazení do prostředí hostování několika webů nebo do zařízení IoT pomocí jedné z [cíle nasazení](#deploy).

Výpočetní prostředky, které používáte pro vaše cílových výpočetních prostředí jsou připojeny k [pracovní prostor](concept-workspace.md). Výpočetní prostředky než v místním počítači sdílejí uživatelé pracovního prostoru.

## <a name="train"></a> Školení cíle

Služba Azure Machine Learning nabízí různé podporu napříč různými výpočetními prostředky.  Můžete také připojit své vlastní výpočetní prostředek, ačkoli podpory pro různé scénáře se může lišit jako podrobnosti jsou dole:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Cíle nasazení

Následující výpočetní prostředky je možné k hostování vašich nasazení modelu.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Spravované výpočetní prostředky

Spravované výpočetní prostředek je vytvořen a spravován společností služby Azure Machine Learning. Tato výpočetní je optimalizována pro úlohy machine learning. Azure Machine Learning Compute je pouze spravovaná výpočetní od 30. května 2019. V budoucnu může přidat další spravované výpočetní prostředky.

### <a name="amlcompute"></a> Azure Machine Learning Compute

Azure Machine Learning Compute můžete použít pro trénování a odvozování batch (Preview).  Tento výpočetní prostředek máte:

* Jeden nebo více node clusteru
* Pokaždé, když odešlete spuštění pravidla automatického škálování provedou 
* Správa automatického clusteru a plánování úloh 
* Podpora pro prostředky procesoru a GPU

Azure Machine Learning Compute instance můžete vytvořit s žádným z následujících akcí:

* Azure Portal
* Azure Machine Learning SDK
* Azure CLI

Další výpočetní prostředky musí být vytvořená mimo pracovní prostor a pak k němu připojená.

## <a name="unmanaged-compute"></a>Nespravované výpočetní prostředky

Nespravované výpočetní prostředek je *není* spravované službou Azure Machine Learning. Vytvoření tohoto typu výpočetní prostředky mimo Azure Machine Learning a pak ho připojit k vašemu pracovnímu prostoru. Nespravované výpočetní prostředky může vyžadovat další kroky pro vás k údržbě nebo ke zlepšení výkonu pro úlohy s machine learning.

## <a name="next-steps"></a>Další postup

* [Nastavení cílových výpočetních prostředí pro trénování modelu](how-to-set-up-training-targets.md)
* [Nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md)