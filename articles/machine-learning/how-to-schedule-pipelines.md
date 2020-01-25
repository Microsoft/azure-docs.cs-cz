---
title: Plánování Azure Machine Learningch kanálů
titleSuffix: Azure Machine Learning
description: Naplánování Azure Machine Learning kanálů pomocí sady Azure Machine Learning SDK pro Python. Naplánované kanály umožňují automatizovat rutiny, časově náročné úlohy, jako je zpracování dat, školení a monitorování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 1766b536043d8c404addb1877aa3ef9b57344ef4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722250"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Plánování kanálů strojového učení pomocí sady Azure Machine Learning SDK pro Python

V tomto článku se dozvíte, jak programově naplánovat spuštění kanálu v Azure. Můžete zvolit vytvoření plánu na základě uplynulého času nebo změny systému souborů. Plány založené na čase se dají využít k zajištění běžné úlohy, jako je monitorování pro posun dat. Plány založené na změnách lze použít k reakci na nepředvídatelné nebo nepředvídatelné změny, například na nahrávání nových dat nebo při úpravách starých dat. Po získání informací o tom, jak vytvořit plány, se dozvíte, jak je načíst a deaktivovat.

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

## <a name="create-a-schedule"></a>Vytvoření plánu

Pro opakované spuštění kanálu vytvoříte plán. `Schedule` přidruží kanál, experiment a Trigger. Trigger může být buď`ScheduleRecurrence`, který popisuje čekání mezi běhy nebo cestou úložiště dat, která určuje adresář, ve kterém se mají sledovat změny. V obou případech budete potřebovat identifikátor kanálu a název experimentu, ve kterém chcete plán vytvořit.

### <a name="create-a-time-based-schedule"></a>Vytvoření plánu založeného na čase

Konstruktor `ScheduleRecurrence` má povinný `frequency` argument, který musí být jeden z následujících řetězců: "Minute", "hour", "Day", "Week" nebo "Month". Také vyžaduje celočíselný `interval` Argument určující, kolik `frequency`ch jednotek by měl uplynout mezi začátkem plánu. Volitelné argumenty vám umožní podrobnější informace o počátečních časech, jak je popsáno v [dokumentaci k sadě SDK pro ScheduleRecurrence](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Vytvořte `Schedule`, který začíná běžet každých 15 minut:

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

Chcete-li vytvořit soubor – reaktivní `Schedule`, je nutné nastavit parametr `datastore` ve volání metody [Schedule. Create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Chcete-li monitorovat složku, nastavte argument `path_on_datastore`.

Argument `polling_interval` umožňuje zadat v minutách četnost změn, ve kterých je úložiště dat kontrolováno.

Pokud byl kanál vytvořen pomocí [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py), můžete tuto proměnnou nastavit na název změněného souboru nastavením argumentu `data_path_parameter_name`.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Nepovinné argumenty při vytváření plánu

Kromě výše popsaných argumentů můžete nastavit argument `status`, aby `"Disabled"` vytvoření neaktivního plánu. Nakonec `continue_on_step_failure` umožňuje předat logickou hodnotu, která přepíše chování výchozího selhání kanálu.

## <a name="view-your-scheduled-pipelines"></a>Zobrazit naplánované kanály

Ve webovém prohlížeči přejděte na Azure Machine Learning. V části **koncové body** v navigačním panelu vyberte možnost **koncové body kanálu**. Tím přejdete na seznam kanálů publikovaných v pracovním prostoru.

![Stránka kanálů v AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Na této stránce si můžete prohlédnout souhrnné informace o všech kanálech v pracovním prostoru: názvy, popisy, stav a tak dále. Kliknutím na svůj kanál přejděte k podrobnostem. Na výsledné stránce jsou k dispozici další podrobnosti o vašem kanálu a můžete přejít k podrobnostem o jednotlivých spuštěních.

## <a name="deactivate-the-pipeline"></a>Deaktivace kanálu

Pokud máte `Pipeline`, která je publikována, ale není naplánována, můžete ji zakázat pomocí:

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

Jakmile budete mít `schedule_id` chcete zakázat, spusťte příkaz:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Pokud pak znovu spustíte `Schedule.list(ws)`, měli byste získat prázdný seznam.

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili sadu SDK Azure Machine Learning pro Python k naplánování kanálu dvěma různými způsoby. Jeden plán se opakuje na základě uplynulých časových taktů. Druhý plán se spustí, pokud je soubor změněn v zadaném `Datastore` nebo v adresáři v tomto úložišti. Zjistili jste, jak použít portál k prohlédnutí kanálu a jednotlivých spuštění. Nakonec jste zjistili, jak zakázat plán, aby kanál přestal běžet.

Další informace:

> [!div class="nextstepaction"]
> [Použití Azure Machine Learningch kanálů pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)

* Další informace o [kanálech](concept-ml-pipelines.md)
* Další informace o [prozkoumání Azure Machine Learning pomocí Jupyter](samples-notebooks.md)
