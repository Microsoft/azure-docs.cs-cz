---
title: Vstupní a výstupní data z kanálů ML
titleSuffix: Azure Machine Learning
description: Příprava, využití a generování dat v kanálech Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879762"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Přesouvání dat do a mezi kroky kanálu ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Data jsou pro kanály strojového učení zásadní. Tento článek obsahuje kód pro import, transformaci a přesunutí dat mezi kroky v kanálu Azure Machine Learning. Přehled fungování dat v Azure Machine Learning najdete v [tématu Přístup ovádata ve službách úložiště Azure](how-to-access-data.md). Výhody a struktura kanálů Azure Machine Learning najdete v tématu [Co jsou kanály Azure Machine Learning?](concept-ml-pipelines.md).

Tento článek vám ukáže, jak:

- Použití `Dataset` objektů pro již existující data
- Přístup k datům v rámci kroků
- Rozdělení `Dataset` dat do podskupin, jako je školení a ověřování podmnožiny
- Vytvoření `PipelineData` objektů pro přenos dat do dalšího kroku kanálu
- Použití `PipelineData` objektů jako vstupu ke krokům potrubí
- Vytvořit `Dataset` nové `PipelineData` objekty z chcete zachovat

## <a name="prerequisites"></a>Požadavky

Budete potřebovat:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

- [Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k azure machine learning [studio](https://ml.azure.com/).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [vytvořte pracovní prostor Azure Machine Learning,](how-to-manage-workspace.md) nebo použijte existující pracovní prostor prostřednictvím sady Python SDK. Importujte `Workspace` `Datastore` třídu a a načtěte `config.json` informace `from_config()`o předplatném ze souboru pomocí funkce . Tato funkce ve výchozím nastavení vyhledá soubor JSON v aktuálním adresáři, ale můžete `from_config(path="your/file/path")`také určit parametr cesty, který bude ukazovat na soubor pomocí .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Některá již existující data. Tento článek stručně ukazuje použití [kontejneru objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Volitelné: Existující kanál strojového učení, jako je například ten popsaný v [příkazovém kanálu Vytvořit a spustit strojové učení pomocí sady Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Použití `Dataset` objektů pro již existující data 

Upřednostňovaným způsobem ingestování dat do kanálu je použití objektu [Dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`objekty představují trvalá data dostupná v celém pracovním prostoru.

Existuje mnoho způsobů, jak `Dataset` vytvořit a zaregistrovat objekty. Tabulkové datové sady jsou určeny pro oddělená data dostupná v jednom nebo více souborech. Datové sady souborů jsou určeny pro binární data (například obrázky) nebo pro data, která budete analyzovat. Nejjednodušší programové způsoby `Dataset` vytváření objektů jsou použití existujících objektů BLOB v úložišti pracovního prostoru nebo veřejných adres URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Další možnosti vytváření datových sad s různými možnostmi a z různých zdrojů, jejich registraci a jejich kontrolu v uzlovém prostředí Azure Machine Learning, pochopení toho, jak velikost dat interaguje s výpočetní kapacitou, a jejich správa verzí najdete v [tématu Vytvoření datových sad Azure Machine Learning](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Předání datových sad do skriptu

Chcete-li předat cestu datové sady do `Dataset` skriptu, `as_named_input()` použijte metodu objektu. Výsledný `DatasetConsumptionConfig` objekt můžete buď předat skriptu jako argument, nebo `inputs` pomocí argumentu do skriptu kanálu `Run.get_context().input_datasets[]`můžete datovou sadu načíst pomocí .

Po vytvoření pojmenovaného vstupu můžete zvolit jeho režim `as_mount()` `as_download()`přístupu: nebo . Pokud skript zpracovává všechny soubory v datové sadě a disk na výpočetním prostředku je dostatečně velký pro datovou sadu, je režim přístupu ke stažení lepší volbou. Režim přístupu ke stažení zabrání režii streamování dat za běhu. Pokud váš skript přistupuje k podmnožině datové sady nebo je příliš velký pro vaše výpočty, použijte režim připojení přístupu. Další informace naleznete v článku [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Předání datové sady kroku kanálu:

1. Použití `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` nebo (žádné 's' na `DatasetConsumptionConfig` konci) k vytvoření objektu
1. Použití `as_mount()` `as_download()` nebo nastavení režimu přístupu
1. Předejte datové sady krokům kanálu `arguments` pomocí `inputs` argumentu nebo

Následující úryvek ukazuje společný vzor kombinování těchto `PythonScriptStep` kroků v rámci konstruktoru: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Můžete také použít metody, jako `random_split()` je například a `take_sample()` vytvořit více vstupů nebo snížit množství dat předaných do kroku kanálu:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Přístup k datovým souborům ve skriptu

Pojmenované vstupy do skriptu krok kanálu jsou `Run` k dispozici jako slovník v rámci objektu. Načtěte `Run` aktivní `Run.get_context()` objekt pomocí a potom načtěte slovník pojmenovaných vstupů pomocí `input_datasets`. Pokud jste `DatasetConsumptionConfig` předali `arguments` objekt pomocí `inputs` argumentu spíše než `ArgParser` argument, přístup k datům pomocí kódu. Obě techniky jsou demonstrovány v následujícím úryvku.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Předaná hodnota bude cesta k souborům datové sady.

Je také možné získat přístup `Dataset` k registrované přímo. Vzhledem k tomu, že registrované datové sady jsou trvalé a sdílené v pracovním prostoru, můžete je načíst přímo:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Použití `PipelineData` pro zprostředkující data

Zatímco `Dataset` objekty představují trvalá data, objekty [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) se používají pro dočasná data, která jsou výstupem z kroků kanálu. Vzhledem k tomu, `PipelineData` že životnost objektu je delší než jeden krok kanálu, definujete je ve skriptu definice kanálu. Při vytváření `PipelineData` objektu je nutné zadat název a úložiště dat, ve kterém budou data umístěna. Předejte `PipelineData` `PythonScriptStep` objekty pomocí `outputs` `arguments` _argumentů i_ argumentů:

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

Objekt můžete vytvořit `PipelineData` pomocí režimu přístupu, který poskytuje okamžité nahrání. V takovém případě při `PipelineData`vytváření nastavíte `upload_mode` `"upload"` `output_path_on_compute` na a použijte argument k určení cesty, na kterou budete zapisovat data:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Použití `PipelineData` jako výstupy kroku školení

V rámci kanálu `PythonScriptStep`můžete načíst dostupné výstupní cesty pomocí argumentů programu. Pokud je tento krok první a inicializuje výstupní data, je nutné vytvořit adresář na zadané cestě. Poté můžete napsat libovolné soubory, které chcete `PipelineData`být obsaženy v .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Pokud jste `PipelineData` vytvořili `is_directory` s `True`argumentem nastaveným na , `os.makedirs()` stačí pouze provést volání a pak byste měli možnost napsat jakékoli soubory, které jste chtěli na cestu. Další podrobnosti naleznete v referenční dokumentaci [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Čtení `PipelineData` jako vstupy k nepočátečním krokům

Po počáteční krok kanálu zapíše `PipelineData` některá data do cesty a stane se výstupem tohoto počátečního kroku, lze jej použít jako vstup do pozdějšího kroku:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

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

Hodnota `PipelineData` vstupu je cesta k předchozímu výstupu. Pokud, jak je uvedeno dříve, první krok napsal jeden soubor, náročné to může vypadat takto: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Převést `PipelineData` `Dataset`objekty na s

Chcete-li `PipelineData` zpřístupnit ji déle, než je doba trvání běhu, převeďte ji pomocí funkce `as_dataset()` na `Dataset`. Potom můžete zaregistrovat `Dataset`, takže je prvotřídní občan ve vašem pracovním prostoru. Vzhledem `PipelineData` k tomu, že váš objekt bude mít jinou cestu `create_new_version` `True` při každém `Dataset` spuštění kanálu, důrazně doporučujeme nastavit při registraci vytvořené z objektu. `PipelineData`

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady Azure pro strojové učení](how-to-create-register-datasets.md)
* [Vytváření a spouštění kanálů strojového učení pomocí Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
