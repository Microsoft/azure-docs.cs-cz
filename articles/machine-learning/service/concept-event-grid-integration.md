---
title: Využívání Azure Machine Learningch událostí
titleSuffix: Azure Machine Learning
description: V tomto článku se naučíte, jak použít Azure Event Grid k přihlášení k odběru událostí generovaných Azure Machine Learning a jejich odhlášení z nich.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: 2fe2d07b29b8799712d59cdf21aeb3ce989ca3b2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158475"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Využívání Azure Machine Learningch událostí (Preview)

Azure Machine Learning spravuje celý životní cyklus procesu strojového učení, včetně školení modelů, nasazení modelů a monitorování. Azure Machine Learning události umožňují aplikacím reagovat na události během životního cyklu strojového učení, jako je třeba dokončení školicích běhů, registrace a nasazení modelů a detekce propustnosti dat pomocí moderních serverů bez serveru. architektury. 

Tyto události jsou publikovány prostřednictvím [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Pomocí Azure Portal, PowerShellu nebo Azure CLI můžou zákazníci snadno přihlásit k odběru událostí [zadáním jednoho nebo více typů událostí a podmínek filtrování](/azure/event-grid/event-filtering). Zákazníci také mají možnost vytvořit si široké spektrum obslužných rutin událostí, jako jsou Azure Functions, Azure Logic Apps nebo obecné Webhooky. Azure Machine Learning společně s Azure Event Grid poskytuje vysokou dostupnou, spolehlivou a spolehlivou platformu pro doručování událostí odolnou proti chybám pro vytváření aplikací řízených událostmi.

Informace o použití Azure Machine Learning s Event Grid najdete v tématu [vytvoření Machine Learning pracovní postupy řízené událostmi (Preview)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Model události 

Azure Event Grid čte události ze zdrojů, jako jsou Azure Machine Learning a další služby Azure. Tyto události se pak odesílají do obslužných rutin událostí, jako je například Azure Event Hubs, Azure Functions, Logic Apps a další. Následující diagram ukazuje, jak Event Grid propojuje zdroje a obslužné rutiny, ale není vyčerpávajícím seznamem podporovaných integrací.

![Azure Event Grid funkční model](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Další informace o zdrojích událostí a obslužných rutinách událostí najdete v tématu [co je Event Grid?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Typy událostí Azure Machine Learning

Azure Machine Learning poskytuje události v různých místech životního cyklu machine learningu: 

| Typ události | Popis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Vyvoláno, když je dokončeno spuštění experimentu machine learningu. |
| `Microsoft.MachineLearningServices.ModelRegistered` | Vyvolá se v případě, že je model strojového učení zaregistrován v pracovním prostoru. |
| `Microsoft.MachineLearningServices.ModelDeployed` | Vyvolá se, když se dokončí nasazení odvozené služby s jedním nebo více modely. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Vyvolá se, když se dokončí úloha detekce posunu dat pro dvě datové sady. |

## <a name="subscribe-to-machine-learning-events"></a>Přihlášení k odběru Machine Learningch událostí

Odběry pro události Azure Machine Learning jsou chráněny pomocí řízení přístupu na základě role (RBAC). Jenom [Přispěvatel nebo vlastník](how-to-assign-roles.md#default-roles) pracovního prostoru můžou vytvářet, aktualizovat a odstraňovat odběry událostí.

Odběry událostí lze filtrovat na základě nejrůznějších podmínek. Filtry lze použít na odběry událostí při [vytváření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) odběru události nebo [později](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrovat podle typu události
Odběr události může určit jeden nebo více Azure Machine Learningch typů událostí.

### <a name="filter-by-event-subject"></a>Filtrovat podle předmětu události
Azure Event Grid podporuje filtry pro závislosti na __začátku__ a __konci__ shody, takže se do odběratele doručí události s odpovídajícím subjektem. Různé události strojového učení mají jiný formát předmětu.

| Typ události | Formát předmětu | Ukázkový předmět |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |

### <a name="advanced-filtering"></a>Rozšířené filtrování

Azure Event Grid podporuje také rozšířené filtrování založené na publikovaných schématech událostí. Podrobnosti o schématu události Azure Machine Learning najdete v [Azure Event Grid schématu událostí pro Azure Machine Learning](../../event-grid/event-schema-machine-learning.md).

Mezi příklady rozšířených filtrů, které můžete provádět, patří:

* Pro událost `Microsoft.MachineLearningServices.ModelRegistered` pro filtrování hodnoty značky modelu:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Další informace o tom, jak používat filtry, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Využívání Machine Learningch událostí

Aplikace, které zpracovávají Machine Learning události, by měly dodržovat několik doporučených postupů:

> [!div class="checklist"]
> * Vzhledem k tomu, že je možné nakonfigurovat více předplatných pro směrování událostí ke stejné obslužné rutině události, je důležité, aby se události nepředpokládaly z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z pracovního prostoru Machine Learning, který očekáváte.
> * Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
> * Vzhledem k tomu, že zprávy mohou docházet mimo pořadí a po nějaké prodlevě, použijte pole ETag k pochopení, zda jsou informace o objektech stále aktuální.  Pomocí polí Sequencer můžete také pochopit pořadí událostí u libovolného konkrétního objektu.
> * Ignorujte pole, která nerozumíte. Tento postup vám pomůže zajistit odolný přístup k novým funkcím, které se v budoucnu můžou přidat.
> * Neúspěšné nebo zrušené operace Azure Machine Learning neaktivují událost. Například pokud nasazení modelu neproběhne úspěšně, Microsoft. MachineLearningServices. ModelDeployed se neaktivuje. Při návrhu aplikací Vezměte v úvahu takový režim selhání. Rozhraní Azure Machine Learning SDK, CLI nebo portál můžete vždy použít ke kontrole stavu operace a pochopení podrobných důvodů selhání.

Azure Event Grid umožňuje zákazníkům vytvářet nespojené obslužné rutiny zpráv, které mohou být aktivovány Azure Machine Learningmi událostmi. Mezi významné příklady obslužných rutin zpráv patří:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Kanál Azure Data Factory
* Obecné Webhooky, které můžou být hostované na platformě Azure nebo jinde

## <a name="next-steps"></a>Další kroky

Další informace o Event Grid a poskytnutí Azure Machine Learningch událostí a try:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Azure Event Grid schéma událostí pro Azure Machine Learning](../../event-grid/event-schema-machine-learning.md)
- [Vytváření pracovních postupů řízených událostmi pomocí Azure Machine Learning](how-to-use-event-grid.md)
