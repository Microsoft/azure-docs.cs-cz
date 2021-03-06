---
title: Výuka s datovými sadami machine learningu
titleSuffix: Azure Machine Learning
description: Naučte se, jak zpřístupnit vaše data místnímu nebo vzdálenému výpočetnímu prostředí pro školení modelů s Azure Machine Learningmi datovými sadami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872384"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Výuka modelů s Azure Machine Learningmi datovými sadami 

V tomto článku se dozvíte, jak pracovat s [Azure Machine Learningmi datovými sadami](/python/api/azureml-core/azureml.core.dataset%28class%29) a naučit modely strojového učení.  V místním nebo vzdáleném výpočetním cíli můžete použít datové sady, aniž byste se museli starat o připojovací řetězce nebo cesty k datům. 

Azure Machine Learning datové sady poskytují bezproblémovou integraci s Azure Machine Learning školicími funkcemi, jako jsou [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive)a [kanály Azure Machine Learning](./how-to-create-machine-learning-pipelines.md).

Pokud nejste připraveni k zpřístupnění dat pro školení modelů, ale chcete načíst data do poznámkového bloku pro zkoumání dat, přečtěte si téma Jak [Prozkoumat data v datové sadě](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Požadavky

K vytváření a školení s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Sada Azure Machine Learning SDK pro Python je nainstalovaná](/python/api/overview/azure/ml/install) (>= 1.13.0), která obsahuje `azureml-datasets` balíček.

> [!Note]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://pypi.org/project/azureml-dataprep/) . Pro uživatele systému Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Využívání datových sad v školicích skriptech pro Machine Learning

Pokud máte strukturovaná data, která ještě nejsou registrovaná jako datová sada, vytvořte TabularDataset a použijte ji přímo ve školicím skriptu pro svůj místní nebo vzdálený experiment.

V tomto příkladu vytvoříte neregistrovanou [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) a zadáte ho jako argument skriptu v objektu [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) pro školení. Pokud chcete tento TabularDataset znovu použít s dalšími experimenty v pracovním prostoru, přečtěte si téma [jak zaregistrovat datové sady do pracovního prostoru](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Následující kód vytvoří neregistrovanou TabularDataset z webové adresy URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Objekty TabularDataset poskytují možnost načíst data ve vaší TabularDataset do dataframe PANDAS nebo Spark, abyste mohli pracovat se známými knihovnami pro přípravu a školení dat, aniž byste museli Poznámkový blok opustit.

### <a name="access-dataset-in-training-script"></a>Přístup k datové sadě ve školicím skriptu

Následující kód konfiguruje argument skriptu `--input-data` , který určíte při konfiguraci vašeho školicího běhu (viz další část). Když je Tabulková datová sada předána jako hodnota argumentu, Azure ML ho přeloží na ID datové sady, kterou pak můžete použít pro přístup k datové sadě v školicím skriptu (bez nutnosti nekódujte pevně název nebo ID datové sady ve skriptu). Pak pomocí [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody načte tuto datovou sadu do PANDAS dataframe pro další zkoumání a přípravu dat před školením.

> [!Note]
> Pokud Váš původní zdroj dat obsahuje NaN, prázdné řetězce nebo prázdné hodnoty, když použijete `to_pandas_dataframe()` , tyto hodnoty se nahradí jako hodnota *null* .

Pokud potřebujete načíst připravená data do nové datové sady z PANDAS dataframe v paměti, zapište data do místního souboru, jako je Parquet, a vytvořte z tohoto souboru novou datovou sadu. Přečtěte si další informace o [tom, jak vytvořit datové sady](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Konfigurace spuštění školení

Objekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) se používá ke konfiguraci a odeslání školicího běhu.

Tento kód vytvoří objekt ScriptRunConfig, `src` který určuje

* Adresář skriptu pro skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Školicí skript *train_titanic. py*.
* Vstupní datová sada pro školení, `titanic_ds` jako argument skriptu. Azure ML tuto chybu vyřeší s odpovídajícím ID datové sady, když se předává do vašeho skriptu.
* Cíl výpočtů pro běh.
* Prostředí pro běh.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Připojit soubory ke vzdáleným výpočetním cílům

Pokud máte nestrukturovaná data, vytvořte [datovou sadu](/python/api/azureml-core/azureml.data.filedataset) souborů a buď připojte nebo Stáhněte své datové soubory, aby byly k dispozici pro váš vzdálený výpočetní cíl pro školení. Další informace o tom, kdy se ke vzdáleným pokusům školení používat [připojení vs. ke stažení](#mount-vs-download) 

Následující příklad: 

* Vytvoří vstupní datovou sadu `mnist_ds` pro vaše školicí údaje.
* Určuje, kam se mají zapsat výsledky školení, a na to, jak tyto výsledky povýšit jako datovou sadu.
* Připojí vstupní datovou sadu k cíli výpočtů.

> [!Note]
> Pokud používáte vlastní image Docker Base, budete ji muset nainstalovat přes `apt-get install -y fuse` jako závislost pro připojení k datové sadě, aby fungovala. Naučte se [vytvořit vlastní image sestavení](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

Příklad poznámkového bloku najdete v tématu [Postup konfigurace školicího běhu s datovým vstupem a výstupem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

### <a name="create-a-filedataset"></a>Vytvoření datové sady

Následující příklad vytvoří neregistrovanou datovou sadu, `mnist_data` z webových adres URL. Tato datová sada je vstupní data pro váš školicí běh.

Přečtěte si další informace o [tom, jak vytvořit datové sady](how-to-create-register-datasets.md) z jiných zdrojů.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Kam zapsat školicí výstup

Můžete zadat, kam chcete zapisovat výsledky školení s [objektem OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

OutputFileDatasetConfig objekty umožňují: 

* Připojte nebo nahrajte výstup spuštění do cloudového úložiště, které zadáte.
* Uložte výstup jako datovou sadu do těchto podporovaných typů úložiště:
    * Objekt blob Azure
    * Sdílená složka Azure
    * Azure Data Lake Storage generace 1 a 2
* Umožňuje sledovat datovou řadu mezi běžícími cvičeními.

Následující kód určuje, že výsledky školení by měly být uloženy jako datová sada ve `outputdataset` složce ve výchozím úložišti objektů BLOB `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Konfigurace spuštění školení

Při připojování prostřednictvím parametru konstruktoru doporučujeme předat datovou sadu jako argument `arguments` `ScriptRunConfig` . Díky tomu získáte cestu k datům (bod připojení) ve školicím skriptu pomocí argumentů. Tímto způsobem můžete použít stejný školicí skript pro místní ladění a vzdálené školení na jakékoli cloudové platformě.

Následující příklad vytvoří ScriptRunConfig, který předává do objektu DataSet prostřednictvím `arguments` . Po odeslání běhu jsou datové soubory, na které se odkazuje datová sada, `mnist_ds` připojené k cíli výpočtů a výsledky školení se uloží do zadané `outputdataset` složky ve výchozím úložišti dat.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Jednoduchý školicí skript

Následující skript se odešle přes ScriptRunConfig. Přečte `mnist_ds ` datovou sadu jako vstup a zapíše soubor do `outputdataset` složky ve výchozím úložišti dat objektu BLOB `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Připojit ke stažení vs

Pro datové sady vytvořené ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL se podporují připojení nebo stahování souborů libovolného formátu. 

Když **připojíte** datovou sadu, připojíte soubory, na které datová sada odkazuje, do adresáře (přípojný bod) a zpřístupníte ji na cílovém výpočetním cíli. Připojení se podporuje pro výpočetní služby založené na systému Linux, včetně Azure Machine Learning výpočetních, virtuálních počítačů a HDInsight. 

Když **stáhnete** datovou sadu, všechny soubory, na které datová sada odkazuje, se stáhnou do cílového výpočetního prostředí. Stahování je podporováno pro všechny typy výpočtů. 

Pokud váš skript zpracovává všechny soubory, na které datová sada odkazuje, a váš výpočetní disk může vyhovovat vaší celé datové sadě, doporučujeme stažení se doporučuje, abyste se vyhnuli režii streamování dat ze služby úložiště. Pokud velikost dat překročí výpočetní velikost disku, není stahování možné. V tomto scénáři doporučujeme připojení, protože jenom datové soubory používané skriptem se načtou v době zpracování.

Následující kód se připojí `dataset` k dočasnému adresáři na adrese `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Získání datových sad ve skriptech Machine Learning

Registrované datové sady jsou přístupné místně i vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód pro přístup k pracovnímu prostoru a získejte datovou sadu, která byla použita v předchozím odeslaném běhu. Ve výchozím nastavení [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) metoda na `Dataset` třídě vrátí nejnovější verzi datové sady, která je zaregistrována v pracovním prostoru.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Azure Blob Storage má vyšší propustnost než sdílenou složku Azure a bude se škálovat na velký počet spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat vaše běhy na používání úložiště objektů BLOB pro přenos souborů zdrojového kódu.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat objektu BLOB se má použít pro přenosy zdrojového kódu.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Příklady poznámkových bloků

+ Další příklady a koncepty datových sad najdete v [poznámkových blocích datových sad](https://aka.ms/dataset-tutorial).
+ Podívejte se, jak [parametrize datové sady v kanálech ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Řešení potíží

* **Inicializace datové sady se nezdařila: časový limit čekání na bod připojení vypršel**: 
  * Pokud nemáte žádná pravidla odchozích [skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md) a používáte `azureml-sdk>=1.12.0` , aktualizujete `azureml-dataset-runtime` a jeho závislosti na nejnovější podverzi, nebo pokud ji používáte v běhu, vytvořte znovu prostředí, aby bylo možné provést nejnovější opravu s opravou. 
  * Pokud používáte `azureml-sdk<1.12.0` , upgradujte na nejnovější verzi.
  * Pokud máte odchozí pravidla NSG, ujistěte se, že existuje odchozí pravidlo, které povoluje veškerý provoz pro značku služby `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí chybová zpráva `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , použijte následující alternativní řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je třeba přenos dat, doporučuje se použít objekty blob, aby bylo možné používat pro odeslání spuštění sdílení souborů. Úlohy můžete rozdělit také mezi dva různé pracovní prostory.

### <a name="passing-data-as-input"></a>Předávání dat jako vstupů

*  **TypeError: FileNotFound: žádný takový soubor nebo adresář**: k této chybě dochází, pokud se v souboru nenachází cesta, kterou zadáte. Je nutné zajistit, aby se způsob, jakým na soubor odkazujete, shodoval s tím, kam jste připojili datovou sadu na výpočetním cíli. Pro zajištění deterministického stavu doporučujeme při připojování datové sady k cíli výpočtů použít abstraktní cestu. Například v následujícím kódu připojíme datovou sadu do kořenového adresáře systému souborů cíle služby COMPUTE `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Pokud nezadáte úvodní lomítko, "/", budete muset zadat předponu pracovního adresáře, např. `/mnt/batch/.../tmp/dataset` na výpočetním cíli, abyste označili, kam chcete datovou sadu připojit.


## <a name="next-steps"></a>Další kroky

* [Automatické učení modelů strojového učení](how-to-configure-auto-train.md#data-source-and-format) pomocí TabularDatasets.

* [Analýza modelů klasifikace obrázků](https://aka.ms/filedataset-samplenotebook) s využitím datových sad

* [Výuka pomocí datových sad, které používají kanály](./how-to-create-machine-learning-pipelines.md).
