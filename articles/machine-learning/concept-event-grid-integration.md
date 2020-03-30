---
title: Využití událostí Azure Machine Learning
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte, jak používat Azure Event Grid k odběru, reakci a odhlášení z odběru událostí generovaných Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139041"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Využití událostí Azure Machine Learning (preview)

Azure Machine Learning spravuje celý životní cyklus procesu strojového učení, včetně školení modelu, nasazení modelu a monitorování. Události Azure Machine Learning umožňují aplikacím reagovat na události během životního cyklu strojového učení, jako je dokončení tréninkových běhů, registrace a nasazení modelů a detekce posunu dat pomocí moderního bezserveru Architektury. 

Tyto události se publikují prostřednictvím [služby Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Pomocí Azure Portal, Powershellu nebo Azure CLI se zákazníci můžou snadno přihlásit k odběru událostí [zadáním jednoho nebo více typů událostí a podmínek filtrování](/azure/event-grid/event-filtering). Zákazníci mají také možnost vytvořit širokou škálu obslužných rutin událostí, jako jsou funkce Azure, Azure Logic Apps nebo obecné webhooky. Azure Machine Learning spolu s Azure Event Grid poskytuje vysokou dostupnou, spolehlivou a chybově odolnou platformu pro doručování událostí, která vám umožňuje vytvářet aplikace řízené událostmi.

Informace o používání Azure Machine Learning s Grid událostí najdete v [tématu vytvoření pracovních postupů strojového učení řízeného událostmi (preview).](how-to-use-event-grid.md)

## <a name="the-event-model"></a>Model události 

Azure Event Grid čte události ze zdrojů, jako je Azure Machine Learning a další služby Azure. Tyto události se pak odešlou do obslužných rutin událostí, jako jsou Azure Event Hubs, Azure Functions, Logic Apps a další. Následující diagram znázorňuje, jak Event Grid spojuje zdroje a obslužné rutiny, ale není úplný seznam podporovaných integrací.

![Funkční model Azure Event Grid](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Další informace o zdrojích událostí a obslužných rutinách událostí naleznete v tématu [Co je event grid?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Typy událostí Azure Machine Learning

Azure Machine Learning poskytuje události v různých bodech životního cyklu strojového učení: 

| Typ události | Popis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Vyvoláno po dokončení experimentu strojového učení |
| `Microsoft.MachineLearningServices.ModelRegistered` | Vyvolána při registraci modelu strojového učení v pracovním prostoru |
| `Microsoft.MachineLearningServices.ModelDeployed` | Vyvolána při dokončení nasazení služby odvození s jedním nebo více modely |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Vyvolána po dokončení úlohy zjišťování posunu dat pro dvě datové sady |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Je aktivována při změně stavu spuštění, aktuálně aktivována pouze v případě, že stav spuštění je "selhání" |

## <a name="subscribe-to-machine-learning-events"></a>Přihlásit se k odběru událostí Machine Learning

Předplatná pro události Azure Machine Learning jsou chráněny řízením přístupu na základě rolí (RBAC). Pouze [přispěvatel nebo vlastník](how-to-assign-roles.md#default-roles) pracovního prostoru můžete vytvořit, aktualizovat a odstranit odběry událostí.

Odběry událostí lze filtrovat na základě různých podmínek. Filtry lze použít na odběry událostí buď při [vytváření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) odběru událostí nebo [později](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrovat podle typu události
Předplatné událostí můžete určit jeden nebo více typů událostí Azure Machine Learning.

### <a name="filter-by-event-subject"></a>Filtrovat podle předmětu události
Azure Event Grid podporuje __filtry__ předmětů na základě začíná a končí __sshodami,__ takže události s odpovídající předmět jsou doručovány odběrateli. Různé události strojového učení mají jiný formát předmětu.

| Typ události | Formát předmětu | Ukázka vzorku |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Rozšířené filtrování

Azure Event Grid také podporuje pokročilé filtrování na základě publikovaného schématu událostí. Podrobnosti o schématu událostí Azure Machine Learning najdete ve [schématu událostí Azure Event Grid pro Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Mezi ukázkové rozšířené filtrování, které můžete provádět, patří:

* Pro `Microsoft.MachineLearningServices.ModelRegistered` událost, chcete-li filtrovat hodnotu značky modelu:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Další informace o použití filtrů najdete v [tématu Filtrování událostí pro mřížku událostí](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Využití událostí strojového učení

Aplikace, které zpracovávají události Machine Learningu, by se měly řídit několika doporučenými postupy:

> [!div class="checklist"]
> * Vzhledem k tomu, že více předplatných lze nakonfigurovat tak, aby směrovalo události do stejné obslužné rutiny událostí, je důležité nepředpokládat, že události pocházejí z určitého zdroje, ale zkontrolovat téma zprávy, abyste se ujistili, že pochází z pracovního prostoru strojového učení, který očekáváte.
> * Podobně zkontrolujte, zda eventType je ten, který jste připraveni ke zpracování a nepředpokládejte, že všechny události, které obdržíte, budou typy, které očekáváte.
> * Vzhledem k tomu, že zprávy mohou být doručeny mimo pořadí a po určité prodlevě, použijte pole etag, abyste pochopili, zda jsou informace o objektech stále aktuální.  Také použijte pole sekvenceru pochopit pořadí událostí na konkrétní objekt.
> * Ignorujte pole, kterým nerozumíte. Tento postup vám pomůže udržet odolnost vůči novým funkcím, které by mohly být přidány v budoucnu.
> * Neúspěšné nebo zrušené operace Azure Machine Learning nespustí událost. Například pokud nasazení modelu selže Microsoft.MachineLearningServices.ModelDeployed se nespustí. Při návrhu aplikací zvažte takový režim selhání. Vždy můžete použít Azure Machine Learning SDK, CLI nebo portál ke kontrole stavu operace a pochopit podrobné příčiny selhání.

Azure Event Grid umožňuje zákazníkům vytvářet de-svázaný zprávy obslužné rutiny, které lze aktivovat události Azure Machine Learning. Některé pozoruhodné příklady obslužné rutiny zpráv jsou:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Kanál Azure Data Factory
* Obecné webové háčky, které mohou být hostované na platformě Azure nebo jinde

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a vyzkoušejte události Azure Machine Learning:

- [Informace o službě Event Grid](../event-grid/overview.md)
- [Schéma událostí Azure Event Grid pro Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Vytváření pracovních postupů řízených událostmi pomocí Azure Machine Learning](how-to-use-event-grid.md)
