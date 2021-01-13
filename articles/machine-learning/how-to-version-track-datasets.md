---
title: Správa verzí datových sad
titleSuffix: Azure Machine Learning
description: Naučte se, jak nejlépe nastavovat datové sady a jak funguje Správa verzí s kanály strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 7eb8010853ba370f4502b6bc155c6a098e64965b
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133442"
---
# <a name="version-and-track-datasets-in-experiments"></a>Verze a sledování datových sad v experimentech

V tomto článku se dozvíte, jak verze a sledování Azure Machine Learning datových sad pro reprodukovatelnost. Správa verzí datových sad je způsob, jak můžete založit stav vašich dat, abyste pro budoucí experimenty mohli použít konkrétní verzi datové sady.

Typické scénáře správy verzí:

* Když jsou nová data dostupná pro přeškolení
* Při použití různých přístupů k přípravě dat nebo technickému řízení funkcí

## <a name="prerequisites"></a>Požadavky

Pro tento kurz potřebujete:

- [Sada Azure Machine Learning SDK pro Python je nainstalována](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Tato sada SDK obsahuje balíček [AzureML-DataSets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py) .
    
- [Pracovní prostor Azure Machine Learning](concept-workspace.md). Načtěte existující kód spuštěním následujícího kódu nebo [vytvořte nový pracovní prostor](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning datovou sadu](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrovat a načíst verze datové sady

Když zaregistrujete datovou sadu, můžete ji verze, znovu použít a sdílet mezi experimenty a kolegy. Můžete registrovat více datových sad pod stejným názvem a načíst konkrétní verzi podle názvu a čísla verze.

### <a name="register-a-dataset-version"></a>Registrace verze datové sady

Následující kód zaregistruje novou verzi `titanic_ds` datové sady nastavením `create_new_version` parametru na `True` . Pokud není `titanic_ds` v pracovním prostoru registrována žádná datová sada, kód vytvoří novou datovou sadu s názvem `titanic_ds` a nastaví její verzi na hodnotu 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Načíst datovou sadu podle názvu

Ve výchozím nastavení metoda [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) `Dataset` třídy vrací nejnovější verzi datové sady registrované v pracovním prostoru. 

Následující kód Získá verzi 1 `titanic_ds` datové sady.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Osvědčené postupy správy verzí

Při vytváření verze datové sady *nevytváříte* další kopii dat v pracovním prostoru. Vzhledem k tomu, že datové sady odkazují na data ve vaší službě úložiště, máte k dispozici jeden zdroj pravdy, který spravuje vaše služba úložiště.

>[!IMPORTANT]
> Pokud jsou data, na která datová sada odkazuje, přepsána nebo odstraněna, volání konkrétní verze datové sady *nevrátí změnu* .

Při načítání dat z datové sady je aktuální datový obsah odkazovaný datovou sadou vždy načten. Pokud se chcete ujistit, že je každá verze datové sady reprodukovatelná, doporučujeme Neupravovat datový obsah, na který odkazuje verze datové sady. Když jsou nová data dostupná v, uložte nové datové soubory do samostatné datové složky a pak vytvořte novou verzi datové sady, která bude obsahovat data z této nové složky.

Následující obrázek a ukázkový kód ukazují doporučený způsob, jak strukturovat vaše složky dat a vytvářet verze datových sad, které na tyto složky odkazují:

![Struktura složek](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>Verze výstupní datové sady kanálu ML

Datovou sadu můžete použít jako vstup a výstup jednotlivých kroků [kanálu ml](concept-ml-pipelines.md) . Při opětovném spuštění kanálů bude výstup každého kroku kanálu zaregistrován jako nová verze datové sady.

Kanály ML naplní výstup každého kroku do nové složky pokaždé, když se kanál znovu spustí. Toto chování umožňuje reprodukovat výstupní datové sady verzí. Přečtěte si další informace o [datových sadách v kanálech](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Sledování dat v experimentech

Azure Machine Learning sleduje data v rámci experimentu jako vstupní a výstupní datové sady.  

Následují scénáře, kdy jsou vaše data sledována jako **vstupní datová sada**. 

* Jako `DatasetConsumptionConfig` objekt pomocí `inputs` `arguments` parametru nebo `ScriptRunConfig` objektu při odesílání experimentu. 

* V případě, že jsou ve skriptu volány metody jako, get_by_name () nebo get_by_id (). V tomto případě je název přiřazený k datové sadě při jeho registraci do pracovního prostoru zobrazen. 

Následují scénáře, kdy jsou vaše data sledována jako **výstupní datová sada**.  

* Předání `OutputFileDatasetConfig` objektu buď pomocí `outputs` `arguments` parametru nebo při odesílání experimentu. `OutputFileDatasetConfig` objekty lze také použít k uchování dat mezi jednotlivými kroky kanálu. Viz [přesunutí dat mezi kroky kanálu ml.](how-to-move-data-in-out-of-pipelines.md)
  
* Zaregistrujte datovou sadu ve vašem skriptu. V tomto případě je název přiřazený k datové sadě při jeho registraci do pracovního prostoru zobrazen. V následujícím příkladu `training_ds` je název, který se zobrazí.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Odeslání podřízeného běhu s neregistrovanou datovou sadou ve skriptu Výsledkem je anonymní uložená datová sada.

### <a name="trace-datasets-in-experiment-runs"></a>Trasovat datové sady v experimentových běhůch

Pro každý Machine Learning experiment můžete snadno trasovat datové sady používané jako vstup s `Run` objektem experiment.

Následující kód používá [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) metodu ke sledování, které vstupní datové sady byly použity při spuštění experimentu:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Můžete také vyhledat `input_datasets` z experimentů pomocí nástroje [Azure Machine Learning Studio](). 

Následující obrázek ukazuje, kde najít vstupní datovou sadu experimentu v Azure Machine Learning Studiu. V tomto příkladu přejdete do podokna **experimenty** a otevřete kartu **vlastnosti** pro konkrétní spuštění experimentu `keras-mnist` .

![Vstupní datové sady](./media/how-to-version-track-datasets/input-datasets.png)

K registraci modelů s datovými sadami použijte následující kód:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po registraci můžete zobrazit seznam modelů registrovaných s datovou sadou pomocí Pythonu nebo přejít do [studia](https://ml.azure.com/).

Následující zobrazení je z podokna datové **sady** v části **assety**. Vyberte datovou sadu a pak vyberte kartu **modely** pro seznam modelů, které jsou zaregistrované s datovou sadou. 

![Vstupní modely datových sad](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Další kroky

* [Trénování s datovými sadami](how-to-train-with-datasets.md)
* [Další ukázkové poznámkové bloky datové sady](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)