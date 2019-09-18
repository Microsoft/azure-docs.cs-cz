---
title: Výuka pomocí souborů AzureML-DataSet
titleSuffix: Azure Machine Learning service
description: Naučte se používat datové sady při školeních.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/16/2019
ms.openlocfilehash: ceccc515b73bd41c7933889c61617c360c678eb7
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059291"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Výuka pomocí datových sad (Preview) v Azure Machine Learning

V tomto článku se dozvíte dva způsoby, jak využívat [Azure Machine Learning datové sady](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) ve vzdálených školicích kurzech, aniž byste se museli starat o připojovací řetězce nebo cesty k datům.

- Možnost 1: Předání datových sad přímo do školicího skriptu.

- Možnost 2: Použijte datové sady k připojení nebo stažení souborů do vzdáleného výpočetního prostředí pro školení.

## <a name="prerequisites"></a>Požadavky

K vytváření a školení s datovými sadami potřebujete:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.

> [!Note]
> Některé třídy DataSet (Preview) mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích:  Red Hat Enterprise Linux, Ubuntu, Fedora a CentOS.

## <a name="option-1-pass-datasets-as-inputs-to-training-scripts"></a>Možnost 1: Předání datových sad jako vstupů do školicích skriptů

Azure Machine Learning datové sady poskytují bezproblémovou integraci s Azure Machine Learning školicími produkty, jako jsou [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) a [Hyperdrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py). V tomto příkladu vytvoříte [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) a použijete ho jako vstup do svého `estimator` objektu pro školení. 

### <a name="create-a-tabulardataset"></a>Vytvoření TabularDataset

Následující kód vytvoří neregistrovanou TabularDataset z webové adresy URL. Můžete také vytvořit datové sady z místních souborů nebo cest v úložišti dat. Přečtěte si další informace o [tom, jak vytvořit datové sady](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="configure-the-estimator"></a>Konfigurace Estimator

K odeslání experimentu se používá objekt [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) . Azure Machine Learning má předem nakonfigurovanou odhady pro běžné architektury strojového učení a také pro obecné Estimator.

Tento kód vytvoří obecný objekt Estimator, `est`který určuje

* Adresář skriptu pro skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Školicí skript *train_titanic. py*.
* Vstupní datová sada pro školení, `titanic`.
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

### <a name="access-the-input-dataset-in-your-training-script"></a>Přístup ke vstupní datové sadě ve školicím skriptu

Objekty TabularDataset poskytují možnost načíst data do datového rámce PANDAS nebo Spark, abyste mohli pracovat se známými knihovnami pro přípravu dat a školení. Pokud chcete tuto funkci využít, můžete předat TabularDataset jako vstup ve vaší konfiguraci školení a pak ho načíst ve svém skriptu.

Provedete to tak, že získáte přístup ke [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) vstupní datové sadě prostřednictvím objektu ve školicím [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) skriptu a použijete metodu. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Možnost 2:  Připojit soubory ke vzdálenému cíli výpočtů

Pokud chcete, aby byly datové soubory k dispozici na výpočetním cíli pro školení, použijte [datovou sadu](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) souborů pro připojení nebo stažení souborů, na které odkazuje.

Když připojíte systém souborů, připojíte tento systém souborů k adresáři (přípojný bod) a zpřístupníte ho v cílovém výpočetním prostředí. Připojení je okamžité, protože soubory jsou načítány pouze v době zpracování. Připojení se podporuje a doporučuje pro výpočetní služby založené na systému Linux, včetně Azure Machine Learning výpočetních, virtuálních počítačů a HDInsight. Pro výpočetní služby, které nejsou založené na systému Linux, se podporuje pouze stahování.  

>[!WARNING]
> Pokud velikost dat překročí limit úložiště cílového výpočetní služby, stahování se nezdaří.

### <a name="create-a-filedataset"></a>Vytvoření datové sady

Následující příklad vytvoří neregistrovanou datovou sadu z adres URL webu. Přečtěte si další informace o [tom, jak vytvořit datové sady](https://aka.ms/azureml/howto/createdatasets) z jiných zdrojů.

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

Místo předání datové sady přes `inputs` parametr v Estimator můžete také předat datovou sadu pomocí `script_params` a získat cestu k datům (bod připojení) ve školicím skriptu pomocí argumentů. Tímto způsobem se můžete vyhnout závislostem na Azure Machine Learning SDK ze školicího skriptu.

[Skriptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objekt Estimator se používá k odeslání běhu pro scikit experimenty.

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

Po odeslání běhu budou datové soubory odkazované `mnist` datovou sadou připojeny k cíli výpočtů. Následující kód ukazuje, jak načíst data ve skriptu.

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

[Ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial) ukazují a rozbalí na základě konceptů v tomto článku, například pomocí datových sad s objekty ScriptRun a [HyperdDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) .

## <a name="next-steps"></a>Další kroky

* [Automatické učení modelů strojového učení](how-to-auto-train-remote.md) pomocí TabularDatasets.

* [Analýza modelů klasifikace obrázků](https://aka.ms/filedataset-samplenotebook) s využitím datových sad
