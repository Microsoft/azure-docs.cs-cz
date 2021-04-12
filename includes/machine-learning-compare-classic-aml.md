---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563181"
---
Následující tabulka shrnuje hlavní rozdíly mezi ML Studio (Classic) a Azure Machine Learning.

| Funkce | ML Studio (klasický) | Azure Machine Learning |
|---| --- | --- |
| Rozhraní přetažení | Klasické prostředí | Aktualizované prostředí – [návrhář Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| Sady SDK kódu | Nepodporované | Plně integrovaná s [Azure Machine Learning Python](/python/api/overview/azure/ml/) a [R](https://github.com/Azure/azureml-sdk-for-r) SDK |
| Experiment | Škálovatelné (limit pro školicí data z 10 GB) | Škálování s cílem výpočetního prostředí |
| Školení výpočetních cílů | Proprietární výpočetní cíl, jenom podpora procesoru | Široké spektrum přizpůsobitelných [výpočetních cílů](../articles/machine-learning/concept-compute-target.md#train). Zahrnuje podporu GPU a procesoru. | 
| Cíle výpočtů nasazení | Speciální formát webové služby, není přizpůsobitelný | Široké škály přizpůsobitelných [výpočetních cílů nasazení](../articles/machine-learning/concept-compute-target.md#deploy). Zahrnuje podporu GPU a procesoru. |
| Kanál ML | Nepodporováno | Vytváření flexibilních modulárních [kanálů](../articles/machine-learning/concept-ml-pipelines.md) pro automatizaci pracovních postupů |
| MLOps | Základní Správa modelů a nasazení; Nasazení pouze procesoru | Správa verzí entit (model, data, pracovní postupy), automatizace pracovních postupů, integrace s nástroji pro CICD, nasazení procesoru a GPU [a další](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Formát modelu | Speciální formát, jenom Studio (Classic) | Více podporovaných formátů v závislosti na typu úlohy školení |
| Automatizované školení modelů a ladění parametrů |  Nepodporováno | [Podporuje](../articles/machine-learning/concept-automated-ml.md)se. Možnosti Code-First a No-Code. | 
| Detekce posunu dat | Nepodporováno | [Podporováno](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projekty označování dat | Nepodporováno | [Podporováno](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Řízení přístupu na základě role (RBAC) | Pouze role přispěvatel a vlastník | [Flexibilní definice rolí a řízení RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Galerie AI | Podporováno ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Nepodporované <br><br> Seznamte se s [ukázkami poznámkových bloků Python SDK](https://github.com/Azure/MachineLearningNotebooks). |