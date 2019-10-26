---
title: Správa verzí datových sad
titleSuffix: Azure Machine Learning service
description: Seznamte se s osvědčenými postupy pro používání verzí datových sad a způsobu fungování verzí s kanály strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901999"
---
# <a name="version-and-track-datasets-in-experiments"></a>Verze a sledování datových sad v experimentech

V tomto postupu se naučíte, jak verze a sledování Azure Machine Learning datových sad pro reprodukovatelnost. Správa verzí datových sad je způsob, jak můžete založit stav vašich dat, abyste pro budoucí experimenty mohli použít určitou verzi datové sady.

Typické scénáře, které je potřeba vzít v úvahu pro správu verzí:

* Když jsou nová data dostupná pro rekurzi.
* Při použití různých přístupů k přípravě dat nebo analýze funkcí.

## <a name="prerequisites"></a>Předpoklady

V tomto postupu budete potřebovat:

- [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček [AzureML-DataSet Sets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- [Pracovní prostor Azure Machine Learning](concept-workspace.md). Načtěte existující kód s následujícím kódem nebo [vytvořte nový pracovní prostor](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning datovou sadu](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrovat a načíst verze datové sady

Registrace datové sady umožňuje jejich verzi, opakované použití a sdílení v rámci experimentů a s kolegy. Můžete registrovat více datových sad pod stejným názvem a načíst konkrétní verzi podle názvu a čísla verze.

### <a name="register-a-dataset-version"></a>Registrace verze datové sady

Následující kód zaregistruje novou verzi datové sady, `titanic_ds`nastavením parametru `create_new_version` na `True`. Pokud není v pracovním prostoru registrována žádná `titanic_ds`, vytvoří novou datovou sadu s názvem `titanic_ds` a nastaví její verzi na 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Načíst datovou sadu podle názvu

Ve výchozím nastavení metoda [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) na třídě `Dataset` vrátí nejnovější verzi datové sady registrované v pracovním prostoru. 

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

Při vytváření verze datové sady **nevytváříte** další kopii dat v pracovním prostoru. Datové sady odkazují na data ve vaší službě úložiště, takže máte jenom jeden jeden zdroj pravdy spravovaný službou úložiště. 

>[!IMPORTANT]
> Pokud jsou data, na která datová sada odkazuje, přepsána nebo odstraněna, volání konkrétní verze datové sady nemůže vrátit zpět změnu. 

Při načítání dat z datové sady bude vždy načten aktuální datový obsah, na který odkazuje datová sada. Pokud chcete zajistit změnu reprodukovatelnosti každé datové sady, doporučujeme Neupravovat datový obsah, na který odkazuje verze datové sady. Když jsou nová data dostupná v, uložte nové datové soubory do samostatné datové složky a vytvořte novou verzi datové sady, která bude obsahovat data z této nové složky dat.

Následující obrázek a ukázkový kód ukazují doporučený způsob, jak strukturovat vaše složky dat, a vytvářet verze datových sad odkazujících na tyto složky.

![Struktura složek](media/how-to-version-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Verze výstupní datové sady kanálu

Datovou sadu můžete použít jako vstup a výstup každého kroku kanálu pro strojové učení (ML). Při opětovném spuštění kanálů bude výstup každého kroku kanálu zaregistrován jako nová verze datové sady. 

Vzhledem k tomu, že kanály ML naplní výstup každého kroku do nové složky při každém opětovném spuštění kanálu, budou výstupní datové sady s verzí reprodukovatelné.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Sledovat datové sady v experimentech

Pro každý experiment strojového učení můžete snadno trasovat datové sady používané jako vstup prostřednictvím objektu `Run` registrovaného modelu.

Použijte následující kód k registraci modelů s datovými sadami.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po registraci můžete zobrazit seznam modelů registrovaných s datovou sadou pomocí Pythonu nebo [cílové stránky pracovního prostoru](https://ml.azure.com/).

Následující kód používá metodu [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) pro sledování, které vstupní datové sady byly použity s tímto experimentem.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

`input_datasets` můžete také vyhledat z experimentů pomocí [cílové stránky pracovního prostoru (Preview)](https://ml.azure.com/). 

Následující obrázek ukazuje, kde najít vstupní datovou sadu experimentu na úvodní stránce pracovního prostoru. V tomto příkladu přejděte do podokna **experimenty** a otevřete kartu **vlastnosti** pro konkrétní spuštění experimentu, `keras-mnist`. 

![Vstupní datové sady](media/how-to-version-datasets/input-datasets.png)

Modely, které používají datovou sadu, můžete také najít na úvodní stránce pracovního prostoru. Následující zobrazení je v okně datové sady v části Assety. Vyberte datovou sadu a přejděte na kartu modely, kde zobrazíte seznam modelů, které tuto datovou sadu používají. 

![Vstupní modely datových sad](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Další kroky

* [Výuka s datovými sadami](how-to-train-with-datasets.md)
* [Další ukázkové Poznámkový blok datových sad](https://aka.ms/dataset-tutorial)
