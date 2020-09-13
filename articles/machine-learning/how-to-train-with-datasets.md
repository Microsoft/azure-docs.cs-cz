---
title: Výuka pomocí souborů AzureML-DataSet
titleSuffix: Azure Machine Learning
description: Naučte se používat datové sady při školeních.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1db62b77f3b9b1bcfc524a68b52c4aef5c16d851
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648180"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Výuka s datovými sadami v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ve školicích experimentech pracovat s [Azure Machine Learningmi datovými sadami](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) .  V místním nebo vzdáleném výpočetním cíli můžete použít datové sady, aniž byste se museli starat o připojovací řetězce nebo cesty k datům.

Azure Machine Learning datové sady poskytují bezproblémovou integraci s Azure Machine Learning školicími produkty, jako jsou [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true), [Hyperdrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) a [kanály Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky

K vytváření a školení s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), která zahrnuje balíček AzureML-DataSet Sets.

> [!Note]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) . Pro uživatele systému Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="access-and-explore-input-datasets"></a>Přístup k vstupním datovým sadám a jejich zkoumání

K existujícímu TabularDataset můžete přistupovat z školicího skriptu experimentu v pracovním prostoru a načíst tuto datovou sadu do PANDAS dataframe pro další zkoumání v místním prostředí.

Následující kód používá [`get_context()`]() metodu ve [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) třídě pro přístup k existujícímu vstupnímu TabularDatasetu, `titanic` ve školicím skriptu. Pak pomocí [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody načte tuto datovou sadu do PANDAS dataframe pro další zkoumání a přípravu dat před školením.

> [!Note]
> Pokud původní zdroj dat obsahuje NaN, prázdné řetězce nebo prázdné hodnoty, když použijete to_pandas_dataframe (), pak se tyto hodnoty nahradí jako hodnota *null* . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Pokud potřebujete načíst připravená data do nové datové sady z objektu In Memory PANDAS dataframe, zapište data do místního souboru, jako je například Parquet, a vytvořte z tohoto souboru novou datovou sadu. Můžete také vytvořit datové sady z místních souborů nebo cest v úložišti dat. Přečtěte si další informace o [tom, jak vytvořit datové sady](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Použití datových sad přímo ve školicích skriptech

Pokud máte strukturovaná data, která ještě nejsou registrovaná jako datová sada, vytvořte TabularDataset a použijte ji přímo ve školicím skriptu pro svůj místní nebo vzdálený experiment.

V tomto příkladu vytvoříte neregistrovanou [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) a použijete ji jako přímý vstup k vašemu `estimator` objektu pro školení. Pokud chcete tento TabularDataset znovu použít s dalšími experimenty v pracovním prostoru, přečtěte si téma [jak zaregistrovat datové sady do pracovního prostoru](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Následující kód vytvoří neregistrovanou TabularDataset z webové adresy URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Objekty TabularDataset poskytují možnost načíst data ve vaší TabularDataset do dataframe PANDAS nebo Spark, abyste mohli pracovat se známými knihovnami pro přípravu a školení dat, aniž byste museli Poznámkový blok opustit. Pokud chcete tuto funkci využít, přečtěte si téma [přístup a prozkoumání vstupních datových sad](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Konfigurace Estimator

K odeslání experimentu se používá objekt [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) . Azure Machine Learning má předem nakonfigurovanou odhady pro běžné architektury strojového učení a také pro obecné Estimator.

Tento kód vytvoří obecný objekt Estimator, `est` který určuje

* Adresář skriptu pro skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Školicí skript *train_titanic. py*.
* Vstupní datová sada pro školení, `titanic_ds` . `as_named_input()` je nutné, aby na vstupní datovou sadu bylo možné odkazovat pomocí přiřazeného názvu `titanic` ve školicím skriptu. 
* Cílová výpočetní operace experimentu.
* Definice prostředí pro experiment.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>Připojit soubory ke vzdáleným výpočetním cílům

Pokud máte nestrukturovaná data, vytvořte [datovou sadu](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) souborů a buď připojte nebo Stáhněte své datové soubory, aby byly k dispozici pro váš vzdálený výpočetní cíl pro školení. Další informace o tom, kdy se ke vzdáleným pokusům školení používat [připojení vs. ke stažení](#mount-vs-download) 

Následující příklad vytvoří datovou sadu a připojí datovou sadu k cíli výpočtů tím, že je předává jako argument v Estimator pro školení. 

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

### <a name="configure-the-estimator"></a>Konfigurace Estimator

Při připojování doporučujeme předat datovou sadu jako argument. Kromě předání datové sady prostřednictvím `inputs` parametru v Estimator můžete také předat datovou sadu pomocí `script_params` a získat cestu k datům (bod připojení) ve školicím skriptu pomocí argumentů. Tímto způsobem budete moct použít stejný školicí skript pro místní ladění a vzdálené školení na jakékoli cloudové platformě.

[Skriptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py&preserve-view=true) objekt Estimator se používá k odeslání běhu pro scikit experimenty. Po odeslání běhu budou datové soubory odkazované `mnist` datovou sadou připojeny k cíli výpočtů. Přečtěte si další informace o školeních s [skriptu sklearn Estimator](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
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

# retrieve the 2 arguments configured through script_params in estimator
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

## <a name="access-datasets-in-your-script"></a>Přístup k datovým sadám ve skriptu

Registrované datové sady jsou přístupné místně i vzdáleně na výpočetních clusterech, jako je Azure Machine Learning Compute. Chcete-li získat přístup k zadané datové sadě mezi experimenty, použijte následující kód pro přístup k pracovnímu prostoru a registrované datové sadě podle názvu. Ve výchozím nastavení [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) metoda na `Dataset` třídě vrátí nejnovější verzi datové sady, která je zaregistrována v pracovním prostoru.

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
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Příklady poznámkových bloků

[Poznámkové bloky datové sady](https://aka.ms/dataset-tutorial) ukazují a rozšiřují koncepty v tomto článku.

## <a name="next-steps"></a>Další kroky

* [Automatické učení modelů strojového učení](how-to-auto-train-remote.md) pomocí TabularDatasets.

* [Analýza modelů klasifikace obrázků](https://aka.ms/filedataset-samplenotebook) s využitím datových sad

* [Výuka pomocí datových sad, které používají kanály](how-to-create-your-first-pipeline.md).
