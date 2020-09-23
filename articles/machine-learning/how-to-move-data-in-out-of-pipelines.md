---
title: Přesun dat v kanálech ML
titleSuffix: Azure Machine Learning
description: Přečtěte si o vstupních & výstupech dat v Azure Machine Learningch kanálech.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: fa78ab00e337ee6f2a42e23a2899d79a09a7ea3d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904981"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Přesun dat kroků kanálu ML a mezi nimi (Python)



Tento článek poskytuje kód pro import, transformaci a přesouvání dat mezi kroky v Azure Machine Learningovém kanálu. Přehled toho, jak data fungují v Azure Machine Learning, najdete v tématu [přístup k datům ve službě Azure Storage](how-to-access-data.md). Výhody a strukturu Azure Machine Learningch kanálů najdete v tématu [co jsou Azure Machine Learning kanály?](concept-ml-pipelines.md).

Tento článek vám ukáže, jak:

- Použít `Dataset` objekty pro již existující data
- Přístup k datům v rámci svých kroků
- Rozdělit `Dataset` data na podmnožiny, jako jsou například školení a podmnožiny ověřování
- Vytvoření `PipelineData` objektů pro přenos dat do dalšího kroku kanálu
- Použití `PipelineData` objektů jako vstupu do postupu kanálu
- Vytvořit nové objekty, které chcete `Dataset` `PipelineData` zachovat

> [!TIP]
> Vylepšené prostředí pro předávání dočasných dat mezi jednotlivými kroky kanálu a uchování dat po spuštění kanálu jsou k dispozici ve třídách veřejné verze Preview  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) a [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) .  Tyto třídy jsou [experimentální](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#&preserve-view=truestable-vs-experimental) funkce ve verzi Preview a můžou se kdykoli změnit.


## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

- Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte existující sadu pomocí sady Python SDK. Importujte `Workspace` třídu a a `Datastore` načtěte ze souboru informace o svém předplatném `config.json` pomocí funkce `from_config()` . Tato funkce ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete také zadat parametr cesty, který bude odkazovat na soubor pomocí `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Některá již existující data. V tomto článku se krátce zobrazuje použití [kontejneru objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Volitelné: existující kanál strojového učení, který je popsaný v tématu [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Použít `Dataset` objekty pro již existující data 

Upřednostňovaným způsobem, jak ingestovat data do kanálu, je použít objekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) . `Dataset` objekty reprezentují trvalá data dostupná v celém pracovním prostoru.

Existuje mnoho způsobů, jak vytvářet a registrovat `Dataset` objekty. Tabulkové datové sady jsou pro data s oddělovači k dispozici v jednom nebo více souborech. Datové sady souborů jsou pro binární data (například obrázky) nebo pro data, která budete analyzovat. Nejjednodušším programovým způsobem, jak vytvářet `Dataset` objekty, je použít existující objekty BLOB v úložišti pracovních prostorů nebo veřejných adresách URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Další možnosti vytváření datových sad s různými možnostmi a z různých zdrojů, jejich registrace a kontrola v uživatelském rozhraní Azure Machine Learning, porozumět způsobu, jakým velikost dat komunikuje s výpočetní kapacitou a jejich verze, najdete v tématu [vytvoření Azure Machine Learning datových sad](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Předání datových sad vašemu skriptu

Chcete-li předat cestu datové sady ke skriptu, použijte `Dataset` `as_named_input()` metodu objektu. Výsledný objekt můžete buď předat `DatasetConsumptionConfig` vašemu skriptu jako argument nebo pomocí `inputs` argumentu pro skript kanálu, můžete načíst datovou sadu pomocí `Run.get_context().input_datasets[]` .

Po vytvoření pojmenovaného vstupu můžete zvolit režim přístupu: `as_mount()` nebo `as_download()` . Pokud váš skript zpracovává všechny soubory v datové sadě a disk na výpočetním prostředku je pro datovou sadu dostatečně velký, je režim přístupu ke stažení lepší volbou. Režim přístupu ke stažení se vyhne režie streamování dat za běhu. Pokud váš skript přistupuje k podmnožině datové sady nebo je pro výpočetní prostředky příliš velký, použijte režim přístupu k připojení. Další informace najdete v tématu [připojení pro čtení a stahování.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Předání datové sady do vašeho kroku kanálu:

1. `TabularDataset.as_named_input()` `FileDataset.as_named_input()` Pro vytvoření objektu použijte nebo (ne ' na konci). `DatasetConsumptionConfig`
1. Použijte `as_mount()` nebo `as_download()` k nastavení režimu přístupu
1. Předání datových sad do kroků kanálu pomocí `arguments` `inputs` argumentů nebo

Následující fragment kódu ukazuje běžný vzor kombinace těchto kroků v rámci `PythonScriptStep` konstruktoru: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

Můžete také použít metody, jako jsou `random_split()` a, a `take_sample()` vytvořit několik vstupů nebo snížit množství dat předávaných do vašeho kroku kanálu:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Přístup k datovým sadám v rámci skriptu

Pojmenované vstupy skriptu kroku kanálu jsou k dispozici jako slovník v rámci `Run` objektu. Načtěte aktivní `Run` objekt pomocí `Run.get_context()` a pak načtěte slovník pojmenovaných vstupů pomocí `input_datasets` . Pokud jste předali `DatasetConsumptionConfig` objekt pomocí `arguments` argumentu místo `inputs` argumentu, přístup k datům pomocí `ArgParser` kódu. Obě metody jsou znázorněny v následujícím fragmentu kódu.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Předaná hodnota bude cesta k souborům DataSet.

Je také možné získat přímý přístup k zaregistrovanému `Dataset` . Vzhledem k tomu, že registrované datové sady jsou trvalé a sdílené v pracovním prostoru, můžete je načíst přímo:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Použít `PipelineData` pro mezilehlé data

Zatímco `Dataset` objekty představují trvalá data, objekty [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) se používají pro dočasná data, která jsou v režimu výstupu z kroků kanálu. Vzhledem k tomu, že životnost `PipelineData` objektu je delší než jeden krok kanálu, je třeba je definovat ve skriptu definice kanálu. Při vytváření objektu je `PipelineData` nutné zadat název a úložiště dat, na kterém budou data umístěna. Předání `PipelineData` objektů do vaší `PythonScriptStep` pomocí _both_ `arguments` `outputs` argumentů a:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Můžete se rozhodnout vytvořit `PipelineData` objekt pomocí režimu přístupu, který poskytuje okamžité nahrání. V takovém případě, když vytvoříte `PipelineData` , nastavte na `upload_mode` `"upload"` a pomocí `output_path_on_compute` argumentu Určete cestu, do které budete zapisovat data:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!TIP]
> Vylepšené prostředí pro předávání mezilehlých dat mezi kroky kanálu je k dispozici s třídou Public Preview [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Příklad kódu, který používá `OutputFileDatasetConfig` , najdete v tématu Postup [vytvoření dvou kroků kanálu ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Použít `PipelineData` jako výstupy krok školení
V rámci vašeho kanálu `PythonScriptStep` můžete načíst dostupné výstupní cesty pomocí argumentů programu. Pokud je tento krok první a Inicializuje výstupní data, je nutné vytvořit adresář v zadané cestě. Pak můžete napsat libovolné soubory, které chcete zahrnout do `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Pokud jste vytvořili `PipelineData` s `is_directory` argumentem nastaveným na `True` , bylo by stačit pouze provést `os.makedirs()` volání a pak byste měli zadarmo napsat jakékoli soubory, které jste si do cesty chtěli zapisovat. Další podrobnosti najdete v referenční dokumentaci k [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) .


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Číst `PipelineData` jako vstupy do jiných než počátečních kroků

Jakmile krok počátečního kanálu zapíše do cesty nějaká data `PipelineData` a bude se jednat o výstup tohoto počátečního kroku, dá se použít jako vstup do pozdějšího kroku:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Hodnota `PipelineData` vstupu je cesta k předchozímu výstupu. 

> [!TIP]
> Vylepšené prostředí pro předávání mezilehlých dat mezi kroky kanálu je k dispozici s třídou Public Preview [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Příklad kódu, který používá `OutputFileDatasetConfig` , najdete v tématu Postup [vytvoření dvou kroků kanálu ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

Pokud, jak je uvedeno výše, první krok zapsal jeden soubor, což může vypadat například takto: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Převést `PipelineData` objekty na `Dataset` s

Pokud byste chtěli, aby byly `PipelineData` k dispozici déle než po dobu běhu, použijte její `as_dataset()` funkci k jejímu převedení na `Dataset` . Pak ho můžete zaregistrovat `Dataset` a vytvořit jako občana první třídy ve vašem pracovním prostoru. Vzhledem k `PipelineData` tomu, že váš objekt bude mít při každém spuštění kanálu jinou cestu, důrazně doporučujeme, abyste `create_new_version` `True` při registraci `Dataset` vytvořeného z objektu nastavili `PipelineData` .

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Vylepšené prostředí pro uchování mezilehlých dat mimo vaše spuštění kanálu je k dispozici ve třídě Public Preview [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Příklad kódu, který používá `OutputFileDatasetConfig` , najdete v tématu Postup [vytvoření dvou kroků kanálu ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady služby Azure Machine Learning](how-to-create-register-datasets.md)
* [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
