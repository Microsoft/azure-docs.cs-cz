---
title: Plánování kanálů Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Naplánujte kanály Azure Machine Learning pomocí sady Azure Machine Learning SDK pro Python. Naplánované kanály umožňují automatizovat rutinní, časově náročné úlohy, jako je zpracování dat, školení a monitorování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 8e1e718fa4e6660d72203ac98bb6d427cdba2059
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024553"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Plánování kanálů strojového učení pomocí Azure Machine Learning SDK pro Python

V tomto článku se dozvíte, jak programově naplánovat kanál pro spuštění v Azure. Můžete vytvořit plán na základě uplynulého času nebo změn systému souborů. Časové plány lze použít k péči o rutinní úkoly, jako je například monitorování posunu dat. Plány založené na změnách lze použít k reakci na nepravidelné nebo nepředvídatelné změny, jako jsou například nová data, která se nahrávají, nebo úpravy starých dat. Poté, co se naučíte vytvářet plány, dozvíte se, jak je načíst a deaktivovat.

## <a name="prerequisites"></a>Požadované součásti

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Prostředí Pythonu, ve kterém je nainstalována sada Azure Machine Learning SDK pro Python. Další informace najdete v [tématu Vytvoření a správa opakovaně použitelných prostředí pro školení a nasazení s Azure Machine Learning.](how-to-use-environments.md)

* Pracovní prostor Machine Learning s publikovaným kanálem. Ten integrovaný v kanálu Vytváření a strojové učení můžete použít [pomocí azure machine learnings SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inicializovat pracovní prostor & získat data

Chcete-li naplánovat kanál, budete potřebovat odkaz na pracovní prostor, identifikátor publikovaného kanálu a název experimentu, ve kterém chcete plán vytvořit. Tyto hodnoty můžete získat pomocí následujícího kódu:

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

## <a name="create-a-schedule"></a>Vytvoření plánu

Chcete-li spustit kanál opakovaně, vytvoříte plán. Přidružený `Schedule` kanál, experiment a aktivační událost. Aktivační událost může`ScheduleRecurrence` být buď, který popisuje čekání mezi spuštění nebo datastore cestu, která určuje adresář sledovat změny. V obou případech budete potřebovat identifikátor kanálu a název experimentu, ve kterém chcete vytvořit plán.

V horní části souboru pythonu importujte `Schedule` třídy a: `ScheduleRecurrence`

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Vytvoření plánu založeného na čase

Konstruktor `ScheduleRecurrence` má požadovaný `frequency` argument, který musí být jeden z následujících řetězců: "Minute", "Hour", "Day", "Week" nebo "Month". Vyžaduje také celé číslo `interval` argument určující, kolik `frequency` jednotek by měla uplynout mezi spuštění plánu. Volitelné argumenty umožňují být konkrétnější o počáteční časy, jak je podrobně popsáno v [ScheduleRecurrence SDK dokumenty](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Vytvořte, `Schedule` který začíná spustit každých 15 minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Vytvoření plánu založeného na změnách

Kanály, které jsou spuštěny změnami souborů, mohou být efektivnější než plány založené na čase. Můžete například provést krok předběžného zpracování při změně souboru nebo při přidání nového souboru do datového adresáře. Můžete sledovat všechny změny úložiště dat nebo změny v rámci určitého adresáře v rámci úložiště dat. Pokud sledujete určitý adresář, změny v podadresářích tohoto adresáře _nespustí_ spuštění.

Chcete-li vytvořit reaktivní `Schedule`soubor , `datastore` musíte nastavit parametr ve volání [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Chcete-li sledovat složku, nastavte `path_on_datastore` argument.

Argument `polling_interval` umožňuje zadat v minutách frekvenci, s jakou je úložiště dat kontrolováno změny.

Pokud byl kanál vytvořen s [parametrem DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter ,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)můžete nastavit tuto proměnnou na název změněného souboru nastavením argumentu. `data_path_parameter_name`

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Volitelné argumenty při vytváření plánu

Kromě argumentů popsaných dříve můžete nastavit `status` argument `"Disabled"` pro vytvoření neaktivního plánu. Nakonec `continue_on_step_failure` umožňuje předat logickou hodnotu, která přepíše výchozí chování selhání kanálu.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Použití aplikací Azure Logic Apps pro složitější pracovní postupy

Azure Logic Apps podporuje složitější pracovní postupy a je mnohem více široce integrované než kanály Azure Machine Learning. Další informace najdete [v tématu Aktivace spuštění kanálu Machine Learning z aplikace logiky.](how-to-trigger-published-pipeline.md)

## <a name="view-your-scheduled-pipelines"></a>Zobrazení naplánovaných kanálů

Ve webovém prohlížeči přejděte na Azure Machine Learning. V části **Koncové body** na navigačním panelu zvolte Koncové **body kanálu**. Tím přejdete na seznam kanálů publikovaných v pracovním prostoru.

![Stránka kanálů AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Na této stránce můžete zobrazit souhrnné informace o všech kanálech v pracovním prostoru: názvy, popisy, stav a tak dále. Přejděte k podrobnostem kliknutím na kanál. Na výsledné stránce jsou další podrobnosti o kanálu a můžete přejít k podrobnostem do jednotlivých spuštění.

## <a name="deactivate-the-pipeline"></a>Deaktivace potrubí

Pokud máte, `Pipeline` který je publikován, ale není naplánováno, můžete zakázat pomocí:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Pokud je kanál naplánován, musíte nejprve zrušit plán. Načtení identifikátoru plánu z portálu nebo spuštěním:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Jakmile budete `schedule_id` mít chcete zakázat, spusťte:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Pokud pak `Schedule.list(ws)` spustit znovu, měli byste získat prázdný seznam.

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili Azure Machine Learning SDK pro Python naplánovat kanál dvěma různými způsoby. Jeden plán se opakuje na základě uplynulého času hodin. Druhý plán se spustí, pokud je `Datastore` soubor změněn v zadaném adresáři nebo v adresáři v tomto úložišti. Viděli jste, jak pomocí portálu prozkoumat kanálu a jednotlivé spuštění. Nakonec jste se naučili zakázat plán tak, aby kanál přestal běžet.

Další informace naleznete v tématu:

> [!div class="nextstepaction"]
> [Použití azure machine learningpotrubí pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)

* Další informace o [kanálech](concept-ml-pipelines.md)
* Další informace o [zkoumání Azure Machine Learning s Jupyter](samples-notebooks.md)

