---
title: Azure Machine Learning vs. Machine Learning Studio (klasické)
description: Jaký je rozdíl mezi Azure Machine Learning a Machine Learning Studio (klasické)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371842"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klasické)

V tomto článku se dozvíte rozdíl mezi Azure Machine Learning a Machine Learning Studio (klasické). 

Azure Machine Learning poskytuje python a r sady SDK **a** návrháře "drag-and-drop" k vytváření a nasazování modelů strojového učení. Studio (klasické) nabízí pouze samostatný drag-and-drop zážitek.

Doporučujeme, aby noví uživatelé zvolili Azure Machine Learning pro nejširší škálu špičkových nástrojů strojového učení.

## <a name="quick-comparison"></a>Rychlé porovnání

Následující tabulka shrnuje některé klíčové rozdíly mezi Azure Machine Learning a Studio (klasické):

| | Machine Learning Studio (Classic) | Azure Machine Learning |
|---| --- | --- |
| Přetažení rozhraní | Podporuje se | Podporované – [Návrhář Azure Machine Learning (preview)](concept-designer.md) | 
| Experiment | Škálovatelné (limit dat školení 10 GB) | Škálování pomocí výpočetního cíle |
| Trénování výpočetních cílů | Proprietární výpočetní cíl, pouze podpora PROCESORU | Široká škála přizpůsobitelných [výpočetních cílů školení](concept-compute-target.md#train). Zahrnuje podporu GPU a CPU | 
| Výpočetní cíle nasazení | Proprietární formát webové služby, který nelze přizpůsobit | Široká škála přizpůsobitelných [výpočetních cílů nasazení](concept-compute-target.md#deploy). Zahrnuje podporu GPU a CPU |
| Kanál ML | Nepodporuje se | Vytváření [flexibilních modulárních kanálů](concept-ml-pipelines.md) pro automatizaci pracovních postupů |
| MlOps | Základní správa a nasazení modelu | Správa verzí entit (model, data, pracovní postupy), automatizace pracovních postupů, integrace s nástroji CICD [a další](concept-model-management-and-deployment.md) |
| Formát modelu | Proprietární formát, Studio (klasické) pouze | Více podporovaných formátů v závislosti na typu úlohy školení |
| Automatické školení modelů a hyperparametrické ladění |  Nepodporuje se | [Podporováno v sadě SDK a vizuálnípracovní ploše](concept-automated-ml.md) | 
| Detekce posunu dat | Nepodporuje se | [Podporováno v sadě SDK a vizuálním pracovním prostoru](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrace ze studia Machine Learning Studio (klasika)

V současné době neexistuje žádný způsob, jak migrovat studio (klasické) datové zdroje do návrháře Azure Machine Learning (preview). Aktuální studio (klasické) uživatelé mohou i nadále používat své prostředky strojového učení. Doporučujeme však všem uživatelům, aby zvážili použití návrháře, který poskytuje známé prostředí drag-and-drop s vylepšeným pracovním postupem **a** škálovatelností, správou verzí a zabezpečením rozlehlé sítě.

## <a name="get-started-with-azure-machine-learning"></a>Začínáme s Azure Machine Learning

Následující materiály vám můžou pomoct začít s Azure Machine Learning. 

- Přečtěte si [přehled Azure Machine Learning](overview-what-is-azure-ml.md).

- Vytvořte [svůj první experiment s pythonovou sadou SDK](tutorial-1st-experiment-sdk-setup.md).

- [Vytvořte si první návrhářský kanál](tutorial-designer-automobile-price-train-score.md) pro předpověď cen automobilů.

![Příklad návrháře Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Další kroky

Kromě funkcí přetažení v návrháři má Azure Machine Learning k dispozici další nástroje:  
  + [Použití poznámkových bloků Pythonu k trénování & nasazení modelů ML](tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown utrénovat & nasazení modelů ML](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení k trénování & nasazení modelů ML](tutorial-first-experiment-automated-ml.md)  
  + [Použití cli strojového učení k trénování a nasazování modelu](tutorial-train-deploy-model-cli.md)

