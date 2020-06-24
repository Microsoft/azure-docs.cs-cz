---
title: Azure Machine Learning vs. Machine Learning Studio (Classic)
description: Jaký je rozdíl mezi Azure Machine Learning a Machine Learning Studio (Classic)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 065fd166fb2ce82a3338322d55ef1ee5886ac56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210367"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (Classic)

V tomto článku se dozvíte rozdíl mezi Azure Machine Learning a Machine Learning Studio (Classic). 

Azure Machine Learning poskytuje sady SDK Pythonu a R **a** návrháře "přetahování" pro sestavování a nasazování modelů strojového učení. Studio (Classic) nabízí jenom samostatné prostředí pro přetahování myší.

Doporučujeme, aby noví uživatelé zvolili Azure Machine Learning pro nejširší škálu nástrojů pro vyjímání strojového učení.

## <a name="quick-comparison"></a>Rychlé porovnání

Následující tabulka shrnuje některé hlavní rozdíly mezi Azure Machine Learning a studia (Classic):

| | Machine Learning Studio (Classic) | Azure Machine Learning |
|---| --- | --- |
| Rozhraní přetažení | Podporuje se | Podporované – [návrhář Azure Machine Learning (Preview)](concept-designer.md) <br/>(Vyžaduje pracovní prostor Enterprise) | 
| Experiment | Škálovatelné (limit pro školicí data z 10 GB) | Škálování s cílem výpočetního prostředí |
| Školení výpočetních cílů | Proprietární výpočetní cíl, jenom podpora procesoru | Široké spektrum přizpůsobitelných [výpočetních cílů](concept-compute-target.md#train). Zahrnuje podporu GPU a procesoru. | 
| Cíle výpočtů nasazení | Speciální formát webové služby, není přizpůsobitelný | Široké škály přizpůsobitelných [výpočetních cílů nasazení](concept-compute-target.md#deploy). Zahrnuje podporu GPU a procesoru. |
| Kanál ML | Nepodporuje se | Vytváření flexibilních modulárních [kanálů](concept-ml-pipelines.md) pro automatizaci pracovních postupů |
| MLOps | Základní Správa modelů a nasazení | Správa verzí entit (model, data, pracovní postupy), automatizace pracovních postupů, integrace s nástroji CICD [a další](concept-model-management-and-deployment.md) |
| Formát modelu | Speciální formát, jenom Studio (Classic) | Více podporovaných formátů v závislosti na typu úlohy školení |
| Automatizované školení modelů a ladění parametrů |  Nepodporuje se | [Podporováno v sadě SDK a pracovním prostoru Visual](concept-automated-ml.md) | 
| Detekce posunu dat | Nepodporuje se | [Podporováno v sadě SDK a v pracovním prostoru Visual](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrace z Machine Learning Studio (Classic)

V současné době neexistuje způsob, jak migrovat prostředky Studio (Classic) do návrháře Azure Machine Learning (Preview). Aktuální Studio (klasické) uživatelé můžou dál používat svoje prostředky strojového učení. Doporučujeme však všem uživatelům zvážit použití návrháře, který poskytuje známé prostředí pro přetahování pomocí vylepšeného **postupu a** škálovatelnosti, správy verzí a podnikového zabezpečení.

## <a name="get-started-with-azure-machine-learning"></a>Začínáme s Azure Machine Learning

Následující zdroje vám pomůžou začít s Azure Machine Learning. 

- Přečtěte si [přehled Azure Machine Learning](overview-what-is-azure-ml.md).

- Vytvořte svůj [první experiment pomocí sady Python SDK](tutorial-1st-experiment-sdk-setup.md).

- [Vytvořte svůj první kanál návrháře](tutorial-designer-automobile-price-train-score.md) pro předpověď automatických cen.

![Příklad návrháře Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Další kroky

Kromě možností přetahování v Návrháři Azure Machine Learning k dispozici další nástroje:  
  + [Použití poznámkových bloků Pythonu k učení & nasazení modelů ML](tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown ke školení & nasazení modelů ML](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení ke studiu & nasazení modelů ML](tutorial-first-experiment-automated-ml.md)  
  + [Použití rozhraní příkazového učení pro strojové učení ke školení a nasazení modelu](tutorial-train-deploy-model-cli.md)

