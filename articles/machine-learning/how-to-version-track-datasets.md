---
title: Správa verzí datové sady
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nejlépe vytvořit verzi datových sad a jak funguje správa verzí s kanály strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: 5bd4436fc63fb570f052606ab557dbcf243cf5e7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476869"
---
# <a name="version-and-track-datasets-in-experiments"></a>Verze a sledování datových sad v experimentech
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak verzi a sledovat azure machine learning datové sady pro reprodukovatelnost. Správa verzí datové sady je způsob, jak zařadit do záložek stav dat, abyste mohli použít určitou verzi datové sady pro budoucí experimenty.

Typické scénáře správy verzí:

* Jsou-li k dispozici nové údaje pro rekvalifikaci
* Při použití různých přístupů k přípravě dat nebo k inženýrským prvkům

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu potřebujete:

- [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Tato sada SDK obsahuje balíček [azureml-datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- Pracovní [prostor Azure Machine Learning](concept-workspace.md). Načtěte existující pomocí následujícího kódu nebo [vytvořte nový pracovní prostor](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Datová sada Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrace a načtení verzí datové sady

Registrací datové sady můžete ji používat, znovu používat a sdílet mezi experimenty a s kolegy. Můžete zaregistrovat více datových sad pod stejným názvem a načíst konkrétní verzi podle názvu a čísla verze.

### <a name="register-a-dataset-version"></a>Registrace verze datové sady

Následující kód registruje novou verzi `titanic_ds` datové sady `create_new_version` nastavením `True`parametru na . Pokud neexistuje žádná `titanic_ds` existující datová sada registrovaná v pracovním prostoru, kód `titanic_ds` vytvoří novou datovou sadu s názvem a nastaví její verzi na 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Načtení datové sady podle názvu

Ve výchozím nastavení vrátí metoda `Dataset` [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) ve třídě nejnovější verzi datové sady registrované v pracovním prostoru. 

Následující kód získá verzi `titanic_ds` 1 datové sady.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Osvědčený postup správy verzí

Když vytvoříte verzi datové sady, *nevytváříte* další kopii dat s pracovním prostorem. Vzhledem k tomu, že datové sady jsou odkazy na data ve službě úložiště, máte jediný zdroj pravdy, spravovaný službou úložiště.

>[!IMPORTANT]
> Pokud jsou data odkazovaná vaší datovou sadou přepsána nebo odstraněna, volání určité verze datové sady změnu *nevrátí.*

Při načítání dat z datové sady se vždy načte aktuální datový obsah, na který datová sada odkazuje. Pokud se chcete ujistit, že každá verze datové sady je reprodukovatelná, doporučujeme neupravovat datový obsah, na který odkazuje verze datové sady. Když přijdou nová data, uložte nové datové soubory do samostatné datové složky a vytvořte novou verzi datové sady, která bude obsahovat data z této nové složky.

Následující obrázek a ukázkový kód ukazují doporučený způsob, jak strukturovat datové složky a vytvořit verze datové sady, které odkazují na tyto složky:

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

## <a name="version-a-pipeline-output-dataset"></a>Verze výstupní datové sady kanálu

Datovou sadu můžete použít jako vstup a výstup každého kroku kanálu Machine Learning. Při opětovném spuštění potrubí je výstup každého kroku kanálu registrován jako nová verze datové sady.

Vzhledem k tomu, že kanály Machine Learning naplní výstup každého kroku do nové složky při každém opakování kanálu, jsou výstupní datové sady s verzí reprodukovatelné. Další informace o [datových sadách v kanálech](how-to-create-your-first-pipeline.md#steps).

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

## <a name="track-datasets-in-experiments"></a>Sledování datových sad v experimentech

Pro každý experiment Machine Learning můžete snadno sledovat datové sady `Run` použité jako vstup prostřednictvím objektu experimentu.

Následující kód používá [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) metodu ke sledování vstupních datových sad, které byly použity při spuštění experimentu:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Můžete také najít `input_datasets` z experimentů pomocí https://ml.azure.com/. 

Následující obrázek ukazuje, kde najít vstupní datovou sadu experimentu ve studiu Azure Machine Learning. V tomto příkladu přejděte do podokna **Experimenty** a otevřete `keras-mnist`kartu **Vlastnosti** pro konkrétní běh experimentu .

![Vstupní datové sady](./media/how-to-version-track-datasets/input-datasets.png)

K registraci modelů s datovými sadami použijte následující kód:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po registraci můžete zobrazit seznam modelů registrovaných v datové sadě https://ml.azure.com/pomocí Pythonu nebo přejít na .

Následující zobrazení je z podokna **Datové sady** v části **Datové zdroje**. Vyberte datovou sadu a pak vyberte kartu **Modely** pro seznam modelů, které jsou registrovány v datové sadě. 

![Modely vstupních datových sad](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Další kroky

* [Trénování s datovými sadami](how-to-train-with-datasets.md)
* [Další ukázkové poznámkové bloky datových sad](https://aka.ms/dataset-tutorial)
