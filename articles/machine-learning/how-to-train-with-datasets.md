---
title: Výuka pomocí souborů AzureML-DataSet
titleSuffix: Azure Machine Learning
description: Naučte se, jak zpřístupnit vaše data pro místní nebo vzdálené výpočty pro školení modelů ML s Azure Machine Learningmi datovými sadami.
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
ms.openlocfilehash: 8b95c5a45992c895713e0be056856172b14b830d
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740670"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Výuka s datovými sadami v Azure Machine Learning


V tomto článku se dozvíte, jak ve školicích experimentech pracovat s [Azure Machine Learningmi datovými sadami](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) .  V místním nebo vzdáleném výpočetním cíli můžete použít datové sady, aniž byste se museli starat o připojovací řetězce nebo cesty k datům.

Azure Machine Learning datové sady poskytují bezproblémovou integraci s Azure Machine Learning školicími funkcemi, jako jsou [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), [Hyperdrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) a [Azure Machine Learning kanály](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky

K vytváření a školení s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Sada Azure Machine Learning SDK pro Python je nainstalovaná](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0), která zahrnuje balíček AzureML-DataSets.

> [!Note]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) . Pro uživatele systému Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="use-datasets-directly-in-training-scripts"></a>Použití datových sad přímo ve školicích skriptech

Pokud máte strukturovaná data, která ještě nejsou registrovaná jako datová sada, vytvořte TabularDataset a použijte ji přímo ve školicím skriptu pro svůj místní nebo vzdálený experiment.

V tomto příkladu vytvoříte neregistrovanou [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) a zadáte ho jako argument skriptu v objektu ScriptRunConfig pro školení. Pokud chcete tento TabularDataset znovu použít s dalšími experimenty v pracovním prostoru, přečtěte si téma [jak zaregistrovat datové sady do pracovního prostoru](how-to-create-register-datasets.md#register-datasets).

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

Pokud potřebujete načíst připravená data do nové datové sady z PANDAS dataframe v paměti, zapište data do místního souboru, jako je Parquet, a vytvořte z tohoto souboru novou datovou sadu. Můžete také vytvořit datové sady z místních souborů nebo cest v úložišti dat. Přečtěte si další informace o [tom, jak vytvořit datové sady](how-to-create-register-datasets.md).

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
Objekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) se používá ke konfiguraci a odeslání školicího běhu.

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

Pokud máte nestrukturovaná data, vytvořte [datovou sadu](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) souborů a buď připojte nebo Stáhněte své datové soubory, aby byly k dispozici pro váš vzdálený výpočetní cíl pro školení. Další informace o tom, kdy se ke vzdáleným pokusům školení používat [připojení vs. ke stažení](#mount-vs-download) 

Následující příklad vytvoří datovou sadu a připojí datovou sadu k cíli služby COMPUTE předáním jako argumentu do školicího skriptu. 

> [!Note]
> Pokud používáte vlastní image Docker Base, budete ji muset nainstalovat přes `apt-get install -y fuse` jako závislost pro připojení k datové sadě, aby fungovala. Naučte se [vytvořit vlastní image sestavení](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Vytvoření datové sady

Následující příklad vytvoří neregistrovanou datovou sadu z adres URL webu. Přečtěte si další informace o [tom, jak vytvořit datové sady](how-to-create-register-datasets.md) z jiných zdrojů.

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

### <a name="configure-the-training-run"></a>Konfigurace spuštění školení
Při připojování prostřednictvím parametru konstruktoru doporučujeme předat datovou sadu jako argument `arguments` `ScriptRunConfig` . Tím se v školicím skriptu dostanete pomocí argumentů cestu k datům (bod připojení). Tímto způsobem budete moct použít stejný školicí skript pro místní ladění a vzdálené školení na jakékoli cloudové platformě.

Následující příklad vytvoří ScriptRunConfig, který předává do objektu DataSet prostřednictvím `arguments` . Po odeslání běhu budou datové soubory odkazované datovou sadou `mnist_ds` připojeny k cíli výpočtů.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Načtěte data ve školicím skriptu.

Následující kód ukazuje, jak načíst data ve skriptu.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
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


## <a name="directly-access-datasets-in-your-script"></a>Přímý přístup k datovým sadám ve skriptu

Registrované datové sady jsou přístupné místně i vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód pro přístup k pracovnímu prostoru a registrované datové sadě podle názvu. Ve výchozím nastavení [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) metoda na `Dataset` třídě vrátí nejnovější verzi datové sady, která je zaregistrována v pracovním prostoru.

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

## <a name="accessing-source-code-during-training"></a>Přístup ke zdrojovému kódu během školení

Azure Blob Storage má vyšší propustnost než sdílenou složku Azure a bude se škálovat na velký počet spuštěných paralelně. Z tohoto důvodu doporučujeme nakonfigurovat vaše běhy na používání úložiště objektů BLOB pro přenos souborů zdrojového kódu.

Následující příklad kódu určuje v konfiguraci spuštění, které úložiště dat objektu BLOB se má použít pro přenosy zdrojového kódu.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Příklady poznámkových bloků

+ [Poznámkové bloky datové sady](https://aka.ms/dataset-tutorial) ukazují a rozšiřují koncepty v tomto článku.
+ Podívejte se, jak [parametrize datové sady v kanálech ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Řešení potíží

* **Inicializace datové sady se nezdařila: časový limit čekání na bod připojení vypršel**: 
  * Pokud nemáte žádná pravidla odchozích [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) a používáte `azureml-sdk>=1.12.0` , aktualizujete `azureml-dataset-runtime` a jeho závislosti na nejnovější podverzi, nebo pokud ji používáte v běhu, vytvořte znovu prostředí, aby bylo možné provést nejnovější opravu s opravou. 
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

* [Automatické učení modelů strojového učení](how-to-auto-train-remote.md) pomocí TabularDatasets.

* [Analýza modelů klasifikace obrázků](https://aka.ms/filedataset-samplenotebook) s využitím datových sad

* [Výuka pomocí datových sad, které používají kanály](how-to-create-your-first-pipeline.md).
