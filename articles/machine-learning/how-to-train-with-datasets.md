---
title: Trénování pomocí azureml-datových sad
titleSuffix: Azure Machine Learning
description: Naučte se používat datové sady v trénování
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283495"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Trénování pomocí datových sad v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte dva způsoby, jak využívat [datové sady Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) ve vzdáleném experimentu školení běží bez obav o připojovací řetězce nebo datové cesty.

- Možnost 1: Pokud máte strukturovaná data, vytvořte tabularDataset a použijte ji přímo v trénovacím skriptu.

- Možnost 2: Pokud máte nestrukturovaná data, vytvořte soubor FileDataset a připojte nebo sbalte soubory do vzdáleného výpočetního zařízení pro školení.

Datové sady Azure Machine Learning poskytují bezproblémovou integraci s trénovanými produkty Azure Machine Learning, jako [jsou kanály ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) a [Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit a trénovat pomocí datových sad, potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md).

* Nainstalovaná [sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která obsahuje balíček azureml-datasets.

> [!Note]
> Některé třídy datových sad mají závislosti na balíčku [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Pro uživatele Linuxu jsou tyto třídy podporovány pouze na následujících distribucích: Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Možnost 1: Použití datových sad přímo v trénovacích skriptech

V tomto příkladu vytvoříte [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) a použít jako `estimator` přímý vstup do objektu pro školení. 

### <a name="create-a-tabulardataset"></a>Vytvoření tabulkové datové sady

Následující kód vytvoří neregistrovanou tabulardataset z webové adresy URL. Můžete také vytvořit datové sady z místních souborů nebo cest v úložišti dat. Další informace o [vytváření datových sad](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Přístup ke vstupní datové sadě v trénovacím skriptu

Objekty TabularDataset poskytují možnost načíst data do pandy nebo jiskru DataFrame, takže můžete pracovat se známými knihovnami pro přípravu dat a školení. Chcete-li využít tuto funkci, můžete předat TabularDataset jako vstup v konfiguraci školení a načíst ve skriptu.

Chcete-li tak učinit, přístup [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) k vstupní datové sady [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) prostřednictvím objektu v trénovacím skriptu a použít metodu. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Konfigurace odhadu

K odeslání experimentu se používá objekt [odhadu.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) Azure Machine Learning má předem nakonfigurované odhady pro běžné architektury strojového učení, stejně jako obecný odhad.

Tento kód vytvoří obecný objekt `est`odhadu , který určuje

* Adresář skriptů pro vaše skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Školicí *skript, train_titanic.py*.
* Vstupní datová sada `titanic`pro školení . `as_named_input()`je vyžadována tak, aby vstupní datová sada mohla být odkazována přiřazeným názvem v trénovacím skriptu. 
* Výpočetní cíl pro experiment.
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


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Možnost 2: Připojení souborů ke vzdálenému výpočetnímu cíli

Pokud chcete zpřístupnit datové soubory v cílovém výpočetním prostředí pro trénování, použijte [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) k připojení nebo stahování souborů, které jsou podle něj.

### <a name="mount-vs-download"></a>Připojit vs. Stáhnout

Připojení nebo stahování souborů libovolného formátu je podporované pro datové sady vytvořené z úložiště objektů Blob Azure, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL. 

Když připojíte datovou sadu, připojíte soubory, na které odkazuje datová sada, do adresáře (přípojného bodu) a zpřístupníte je na výpočetním cíli. Montáž je podporovaná pro výpočetní prostředky založené na Linuxu, včetně Azure Machine Learning Compute, virtuálních počítačů a HDInsight. Při stahování datové sady se všechny soubory, na které odkazuje datová sada, stáhnou do výpočetního cíle. Stahování je podporováno pro všechny typy výpočetních prostředků. 

Pokud skript zpracovává všechny soubory, na které odkazuje datová sada, a výpočetní disk se vejde do celé datové sady, doporučujeme stáhnout, aby se zabránilo režii datových proudů dat ze služeb úložiště. Pokud velikost dat překročí velikost výpočetního disku, stahování není možné. V tomto scénáři doporučujeme připojení, protože v době zpracování jsou načteny pouze datové soubory používané skriptem.

Následující kód se `dataset` připojí k dočasnému adresáři na adrese`mounted_path`

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

### <a name="create-a-filedataset"></a>Vytvoření datové sady souborů

Následující příklad vytvoří neregistrovanou sadu FileDataset z webových adres URL. Přečtěte si další informace o [tom, jak vytvářet datové sady](https://aka.ms/azureml/howto/createdatasets) z jiných zdrojů.

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

### <a name="configure-the-estimator"></a>Konfigurace odhadu

Kromě předávání datové sady `inputs` prostřednictvím parametru v odhadu můžete `script_params` také předat datovou sadu a získat datovou cestu (montážní bod) ve školicím skriptu prostřednictvím argumentů. Tímto způsobem můžete udržovat váš školicí skript nezávislý na azureml-sdk. Jinými slovy, budete moci použít stejný školicí skript pro místní ladění a vzdálené školení na libovolné cloudové platformě.

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) odhad objekt se používá k odeslání spustit pro scikit-learn experimenty. Další informace o školení s [odhadem SKlearn](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Načtení dat v trénovacím skriptu

Po odeslání spuštění datové soubory uvedené `mnist` datové sady budou připojeny k výpočetnímu cíli. Následující kód ukazuje, jak načíst data ve skriptu.

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

## <a name="notebook-examples"></a>Příklady poznámkových bloků

[Poznámkové bloky datové sady](https://aka.ms/dataset-tutorial) ukazují a rozšiřují koncepty v tomto článku.

## <a name="next-steps"></a>Další kroky

* [Automatické trénování modelů strojového učení](how-to-auto-train-remote.md) pomocí tabulardatasets

* [Modely klasifikace obrázků trénování](https://aka.ms/filedataset-samplenotebook) pomocí souborů FileDatasets

* [Trénování pomocí datových sad pomocí kanálů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
