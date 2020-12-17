---
title: Aktivace kanálů Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Aktivované kanály umožňují automatizovat rutiny, časově náročné úlohy, jako je zpracování dat, školení a monitorování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 12/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 15d8a198df4769b94bced49b82f7be827c771994
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630903"
---
# <a name="trigger-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Aktivace kanálů strojového učení s Azure Machine Learning SDK pro Python

V tomto článku se dozvíte, jak programově naplánovat spuštění kanálu v Azure. Můžete zvolit vytvoření plánu na základě uplynulého času nebo změny systému souborů. Plány založené na čase se dají využít k zajištění běžné úlohy, jako je monitorování pro posun dat. Plány založené na změnách lze použít k reakci na nepředvídatelné nebo nepředvídatelné změny, například na nahrávání nových dat nebo při úpravách starých dat. Po získání informací o tom, jak vytvořit plány, se dozvíte, jak je načíst a deaktivovat. Nakonec se naučíte používat aplikaci logiky Azure, která umožňuje složitější aktivaci logiky nebo chování.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Prostředí Pythonu, ve kterém je nainstalovaná sada Azure Machine Learning SDK pro Python. Další informace najdete v tématu [vytváření a Správa opakovaně použitelných prostředí pro školení a nasazení pomocí Azure Machine Learning.](how-to-use-environments.md)

* Machine Learning pracovní prostor s publikovaným kanálem. V sadě Azure Machine Learning SDK můžete použít jeden z vestavěných [kanálů pro vytváření a spouštění strojového učení](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicializujte pracovní prostor & získat data

K naplánování kanálu budete potřebovat odkaz na svůj pracovní prostor, identifikátor publikovaného kanálu a název experimentu, ve kterém chcete plán vytvořit. Tyto hodnoty můžete získat pomocí následujícího kódu:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Vytvořit plán

Pro opakované spuštění kanálu vytvoříte plán. `Schedule`Přidruží kanál, experiment a Trigger. Aktivační událost může být buď typu `ScheduleRecurrence` , který popisuje čekání mezi spuštěními nebo cestou úložiště dat, která určuje adresář, ve kterém se mají sledovat změny. V obou případech budete potřebovat identifikátor kanálu a název experimentu, ve kterém chcete plán vytvořit.

V horní části souboru Pythonu importujte `Schedule` `ScheduleRecurrence` třídy a:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Vytvoření plánu založeného na čase

`ScheduleRecurrence`Konstruktor má povinný `frequency` argument, který musí být jeden z následujících řetězců: "Minute", "hour", "Day", "Week" nebo "Month". Také vyžaduje celočíselný `interval` argument určující, kolik `frequency` jednotek by měl uplynout mezi začátkem plánu. Volitelné argumenty vám umožní podrobnější informace o počátečních časech, jak je popsáno v [dokumentaci k sadě SDK pro ScheduleRecurrence](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Vytvořte `Schedule` , který začíná běžet každých 15 minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Vytvořit plán na základě změny

Kanály aktivované změnami souborů můžou být efektivnější než plány založené na čase. Například můžete chtít provést krok předzpracování při změně souboru nebo při přidání nového souboru do datového adresáře. Můžete sledovat všechny změny v úložišti dat nebo změny v rámci určitého adresáře v úložišti dat. Pokud monitorete konkrétní adresář, změny v podadresářích tohoto adresáře nebudou _aktivovat běh_ .

Chcete-li vytvořit soubor – reaktivní `Schedule` , musíte nastavit `datastore` parametr ve volání metody [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Chcete-li monitorovat složku, nastavte `path_on_datastore` argument.

`polling_interval`Argument umožňuje zadat, v minutách četnosti změn, ve kterých je úložiště dat kontrolováno.

Pokud byl kanál vytvořen pomocí [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py), můžete tuto proměnnou nastavit na název změněného souboru nastavením `data_path_parameter_name` argumentu.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Nepovinné argumenty při vytváření plánu

Kromě výše popsaných argumentů můžete nastavit `status` argument na `"Disabled"` k vytvoření neaktivního plánu. Nakonec `continue_on_step_failure` vám umožní předat logickou hodnotu, která přepíše chování výchozího selhání kanálu.

## <a name="view-your-scheduled-pipelines"></a>Zobrazit naplánované kanály

Ve webovém prohlížeči přejděte na Azure Machine Learning. V části **koncové body** v navigačním panelu vyberte možnost **koncové body kanálu**. Tím přejdete na seznam kanálů publikovaných v pracovním prostoru.

![Stránka kanálů v AML](./media/how-to-trigger-published-pipeline/scheduled-pipelines.png)

Na této stránce si můžete prohlédnout souhrnné informace o všech kanálech v pracovním prostoru: názvy, popisy, stav a tak dále. Kliknutím na svůj kanál přejděte k podrobnostem. Na výsledné stránce jsou k dispozici další podrobnosti o vašem kanálu a můžete přejít k podrobnostem o jednotlivých spuštěních.

## <a name="deactivate-the-pipeline"></a>Deaktivace kanálu

Pokud máte, `Pipeline` který je publikovaný, ale není naplánovaný, můžete ho zakázat pomocí:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Pokud je tento kanál naplánovaný, musíte nejdřív zrušit plán. Načtěte identifikátor plánu z portálu nebo spuštěním:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Jakmile budete `schedule_id` chtít zakázat, spusťte příkaz:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Pokud pak znovu spustíte `Schedule.list(ws)` , měli byste mít prázdný seznam.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Použití Azure Logic Apps pro komplexní triggery 

S využitím [Aplikace logiky Azure](../logic-apps/logic-apps-overview.md)se dá vytvořit složitější pravidla triggeru nebo chování.

Pokud chcete použít aplikaci logiky Azure k aktivaci kanálu Machine Learning, budete potřebovat koncový bod REST pro publikovaný Machine Learning kanál. [Vytvořte a publikujte svůj kanál](how-to-create-your-first-pipeline.md). Pak vyhledejte koncový bod REST s `PublishedPipeline` použitím ID kanálu:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Teď vytvořte instanci [Aplikace logiky Azure](../logic-apps/logic-apps-overview.md) . Pokud chcete, [použijte prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a [nastavte klíč spravovaný zákazníkem](../logic-apps/customer-managed-keys-integration-service-environment.md) pro použití vaší aplikace logiky.

Po zřízení aplikace logiky můžete pomocí těchto kroků nakonfigurovat aktivační událost pro svůj kanál:

1. [Vytvořte spravovanou identitu přiřazenou systémem](../logic-apps/create-managed-service-identity.md) , která aplikaci poskytne přístup k vašemu pracovní prostor Azure Machine Learning.

1. Přejděte do zobrazení návrháře aplikací logiky a vyberte šablonu prázdná aplikace logiky. 
    > [!div class="mx-imgBorder"]
    > ![Prázdná šablona](media/how-to-trigger-published-pipeline/blank-template.png)

1. V Návrháři vyhledejte **objekt BLOB**. Vyberte aktivační událost **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)** a přidejte tuto aktivační událost do aplikace logiky.
    > [!div class="mx-imgBorder"]
    > ![Přidání triggeru](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Zadejte informace o připojení pro účet úložiště objektů blob, který chcete monitorovat pro doplňky nebo úpravy objektu BLOB. Vyberte kontejner, který chcete monitorovat. 
 
    Vyberte **interval** a **frekvenci** dotazování na aktualizace, které pro vás budou fungovat.  

    > [!NOTE]
    > Tato aktivační událost bude monitorovat vybraný kontejner, ale nebude sledovat podsložky.

1. Přidejte akci HTTP, která se spustí při zjištění nového nebo upravovaného objektu BLOB. Vyberte **+ Nový krok**, vyhledejte a vyberte akci HTTP.

  > [!div class="mx-imgBorder"]
  > ![Vyhledat akci HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Ke konfiguraci akce použijte následující nastavení:

  | Nastavení | Hodnota | 
  |---|---|
  | Akce HTTP | POST |
  | Identifikátor URI |koncový bod publikovaného kanálu, který jste našli jako [požadavek](#prerequisites) |
  | Režim ověřování | Spravovaná identita |

1. Nastavte svůj plán tak, aby se nastavila hodnota libovolné [PipelineParametersy DataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) , kterou jste mohli mít:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Použijte `DataStoreName` jako [součást](#prerequisites), kterou jste přidali do svého pracovního prostoru.
     
    > [!div class="mx-imgBorder"]
    > ![Nastavení HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Vyberte **Uložit** a váš plán je teď připravený.

> [!IMPORTANT]
> Pokud ke správě přístupu k vašemu kanálu používáte řízení přístupu na základě role Azure (Azure RBAC), [nastavte oprávnění pro váš scénář kanálu (školení nebo bodování)](how-to-assign-roles.md#common-scenarios).

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili sadu SDK Azure Machine Learning pro Python k naplánování kanálu dvěma různými způsoby. Jeden plán se opakuje na základě uplynulých časových taktů. Druhý plán se spustí, pokud je soubor změněn na zadaném `Datastore` nebo v adresáři v daném úložišti. Zjistili jste, jak použít portál k prohlédnutí kanálu a jednotlivých spuštění. Zjistili jste, jak zakázat plán, aby kanál přestal běžet. Nakonec jste vytvořili aplikaci logiky Azure, která aktivuje kanál. 

Další informace najdete tady:

> [!div class="nextstepaction"]
> [Použití Azure Machine Learningch kanálů pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)

* Další informace o [kanálech](concept-ml-pipelines.md)
* Další informace o [prozkoumání Azure Machine Learning pomocí Jupyter](samples-notebooks.md)