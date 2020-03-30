---
title: Trénujte modely Keras s hlubokým učením
titleSuffix: Azure Machine Learning
description: Naučte se trénovat a registrovat model klasifikace hluboké neuronové sítě Keras běžící na TensorFlow pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269962"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Trénování a registrace klasifikačního modelu Keras pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek ukazuje, jak trénovat a registrovat model klasifikace Keras postavený na TensorFlow pomocí Azure Machine Learning. Používá populární [datovou sadu MNIST](http://yann.lecun.com/exdb/mnist/) ke klasifikaci ručně psaných číslic pomocí hluboké neuronové sítě (DNN) postavené pomocí [knihovny Keras Python](https://keras.io) běžící na vrcholu [TensorFlow](https://www.tensorflow.org/overview).

Keras je rozhraní API neuronové sítě na vysoké úrovni schopné provozovat špičkové další populární architektury DNN pro zjednodušení vývoje. S Azure Machine Learning můžete rychle škálovat úlohy školení pomocí elastických cloudových výpočetních prostředků. Můžete také sledovat vaše tréninkové běhy, modely verzí, nasazovat modely a mnoho dalšího.

Ať už vyvíjíte model Keras od základu nebo přinášíte existující model do cloudu, Azure Machine Learning vám pomůže vytvářet modely připravené pro produkční prostředí.

Informace o rozdílech mezi strojovým učením a hloubkovým učením najdete v [koncepčním článku.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v některém z těchto prostředí:

- Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

     - Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.
    - Ve složce ukázky na serveru notebooku vyhledejte vyplněný a rozšířený poznámkový blok tak, že přejdete do tohoto adresáře: **> školení s hloubkovým učením s podrobným učením > vlak-hyperparameter-tune-deploy-with-keras.**

 - Váš vlastní Jupyter Notebook server

    - [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stáhněte si ukázkové soubory skriptů](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` a`utils.py`

    Můžete také najít vyplněnou [verzi jupyterského poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) této příručky na stránce ukázek GitHub. Poznámkový blok obsahuje rozšířené sekce, které zahrnují inteligentní ladění hyperparametrů, nasazení modelu a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastaví trénovací experiment načtením požadovaných balíčků pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním trénovacích dat a trénovacích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte potřebné knihovny Pythonu.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializaci pracovního prostoru

[Pracovní prostor Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje vám centralizované místo pro práci se všemi artefakty, které vytvoříte. V pythonu SDK můžete přistupovat k artefaktům [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) pracovního prostoru vytvořením objektu.

Vytvořte objekt pracovního `config.json` prostoru ze souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvořte experiment s názvem "keras-mnist" ve vašem pracovním prostoru.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Vytvoření datové sady souborů

Objekt `FileDataset` odkazuje na jeden nebo více souborů v úložišti dat pracovního prostoru nebo na veřejné adresy URL. Soubory mohou mít libovolný formát a třída vám poskytuje možnost stáhnout nebo připojit soubory k výpočtu. Vytvořením `FileDataset`aplikace vytvoříte odkaz na umístění zdroje dat. Pokud jste na sadu dat použili nějaké transformace, budou uloženy také v sadě dat. Data zůstanou ve stávajícím umístění, takže nevznikají žádné další náklady na úložiště. Další [how-to](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) informace naleznete v `Dataset` návodu k balíčku.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Pomocí `register()` této metody můžete zaregistrovat sadu dat do pracovního prostoru, aby mohla být sdílena s ostatními, znovu použita v různých experimentech a v trénovacím skriptu označována podle názvu.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Vytvoření výpočetního cíle

Vytvořte výpočetní cíl pro úlohu TensorFlow, na které se má spustit. V tomto příkladu vytvořte výpočetní cluster Azure Machine Learning s podporou GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Další informace o výpočetních cílech najdete v článku, [co je cíl výpočetního cíle.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Vytvoření odhadu Tentendotu a import kerasu

[Odhad TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob spuštění úloh y tenterového školení na výpočetní cíl. Vzhledem k tomu, že Keras běží nad TensorFlow, můžete použít odhad `pip_packages` TensorFlow a importovat knihovnu Keras pomocí argumentu.

Nejprve získat data z úložiště dat `Dataset` pracovního prostoru pomocí třídy.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

Odhad TentenzorFlow je implementován prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy, kterou lze použít pro podporu libovolného rámce. Kromě toho vytvořte `script_params` slovník, který obsahuje nastavení hyperparameter DNN. Další informace o trénovacích modelech pomocí obecného odhadu najdete v tématu [trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Odeslat běh

Run [Objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historii spuštění, zatímco úloha je spuštěna a po dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jako run je proveden, prochází následujícími fázemi:

- **Příprava**: Obrázek dockeru je vytvořen podle odhadu TensorFlow. Bitová kopie se nahraje do registru kontejnerů pracovního prostoru a uloží do mezipaměti pro pozdější spuštění. Protokoly jsou také přenášeny do historie spuštění a lze je zobrazit za účelem sledování průběhu.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spuštění více uzlů, než je aktuálně k dispozici.

- **Spuštění**: Všechny skripty ve složce skriptu jsou odeslány do výpočetního cíle, úložiště dat jsou připojena nebo zkopírována a entry_script se spustí. Výstupy ze složky stdout a ./logs jsou přenášeny do historie spuštění a lze je použít ke sledování běhu.

- **Post-Processing**: Složka ./výstupy spuštění je zkopírována do historie spuštění.

## <a name="register-the-model"></a>Registrace modelu

Jakmile vycvičíte model DNN, můžete jej zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Model, který jste právě zaregistrovali, se nasadí přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který odhad jste použili pro školení. Postup nasazení obsahuje oddíl o registraci modelů, ale můžete přeskočit přímo k [vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

Můžete si také stáhnout místní kopii modelu. To může být užitečné pro provedení další ověření modelu pracovat místně. V trénovací skript , `mnist-keras.py`TensorFlow spořič objekt uchovává model do místní složky (místní výpočetní cíl). Objekt Spustit můžete použít ke stažení kopie z úložiště dat.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vyškolili a zaregistrovali model Keras na Azure Machine Learning. Chcete-li se dozvědět, jak nasadit model, pokračujte v našem článku nasazení modelu.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Referenční architektura pro distribuované školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
