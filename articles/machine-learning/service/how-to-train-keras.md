---
title: Trénování a zaregistrujte Keras modely spuštěné na TensorFlow
titleSuffix: Azure Machine Learning service
description: Tento článek popisuje, jak pro trénování a zaregistrujte model Keras systémem TensorFlow pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: e070b80f86cb6c8b1d9e7575e19022b5cb08f340
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165558"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Trénování a registraci Keras modely ve velkém měřítku ve službě Azure Machine Learning

Tento článek popisuje, jak pro trénování a zaregistrujte model Keras založená na TensorFlow pomocí služby Azure Machine Learning. Využívá Oblíbené [datovou sadu mnist ručně](http://yann.lecun.com/exdb/mnist/) klasifikovat ručně číslic, pomocí hluboké neuronové sítě (DNN) vytvořené pomocí [knihovna Python pro Keras](https://keras.io) systémem nad [TensorFlow](https://www.tensorflow.org/overview) .

Keras je vysoké úrovně rozhraní API umožňující spouštění horní dalších oblíbených architektur DNN ke zjednodušení vývoje neuronové sítě. Pomocí služby Azure Machine Learning můžete rychle horizontální navýšení kapacity pomocí elastických cloudových výpočetních prostředků úlohy trénování. Můžete také sledovat tréninkových spuštění, verze modely nasazení modelů a mnoho dalšího.

Ať už vyvíjíte modelu Keras od základů nebo existující model spojili do cloudu, můžete pomocí služby Azure Machine Learning sestavovat modely připravené pro produkční prostředí.

## <a name="prerequisites"></a>Požadavky

Tento kód spusťte v jedné z těchto prostředí:

 - Azure Machine Learning poznámkového bloku virtuálního počítače – žádné soubory ke stažení nebo instalace

     - Dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md) vytvořit poznámkový blok vyhrazený server už načtené pomocí sady SDK a ukázkové úložiště.
    - Ve složce samples na serveru Poznámkový blok, najít poznámkový blok dokončené a rozšířená tak, že přejdete do tohoto adresáře: **postupy-k-použití azureml > školení s obsáhlého learningu > train-hyperparameter-tune-deploy-with-keras** složka. 
 
 - Váš vlastní server poznámkového bloku Jupyter

     - [Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python](setup-create-workspace.md#sdk)
    - [Vytvořte konfigurační soubor pracovního prostoru](setup-create-workspace.md#write-a-configuration-file)
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` a `utils.py`
     
    Můžete také vyhledat dokončené [Poznámkový blok Jupyter verze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) tohoto průvodce na stránce ukázky na Githubu. Poznámkový blok obsahuje rozšířené oddíly věnované inteligentní hyperparametrů, model nasazení a widgetů poznámkového bloku.

## <a name="set-up-the-experiment"></a>Nastavení testu

Tato část výukového experimentu nastaví načtením balíčky požadované pythonu, inicializuje se pracovní prostor, vytvoření experimentu a nahrávání trénovacích dat a trénovací skripty.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte nezbytné knihovny jazyka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Pracovního prostoru služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně služby. To vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte. V sadě Python SDK, dostanete artefakty pracovního prostoru tak, že vytvoříte [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvoření objektu z pracovního prostoru `config.json` soubor vytvořený v [části s předpoklady](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvoření experimentu a složku pro uložení trénovací skripty. V tomto příkladu vytvoření experimentu nazývá "keras mnist ručně".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Nahrání datové sady a skripty

[Datastore](how-to-access-data.md) je místo, kde můžete data ukládají a získávají připojením nebo kopírování dat do cílového výpočetního prostředí. Každý pracovní prostor poskytuje výchozí úložiště. Nahrání dat a skriptů školení k úložišti dat tak, aby k nim mohou snadno přistupovat během cvičení.

1. Stáhněte datovou sadu mnist ručně místně.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Nahrajte datovou sadu mnist ručně do výchozího úložiště.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Uložení skriptu školení Keras, `keras_mnist.py`a pomocný soubor `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Vytvořte cílové výpočetní prostředí pro vaši úlohu TensorFlow ke spuštění na. V tomto příkladu vytvořte výpočetní cluster s podporou grafického procesoru Azure Machine Learning.

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

Další informace o cílových výpočetních prostředí najdete v článku [co je cílové výpočetní prostředí](concept-compute-target.md) článku.

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Odhad TensorFlow umožňuje vytvořit a importovat Keras

[TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob spouštění úlohy trénování TensorFlow na cílové výpočetní prostředí. Keras běží nad TensorFlow, můžete použít TensorFlow estimator a import pomocí knihovny Keras `pip_packages` argument.

Odhad TensorFlow se implementuje pomocí obecného [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídu, která slouží k podpoře libovolné architektury. Další informace o školení modely s využitím obecných odhad, naleznete v tématu [trénování modelů Azure Machine Learning pomocí odhad](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
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

## <a name="submit-a-run"></a>Odeslat spustit

[Spustit objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historie spuštění, když úloha běží, a po jejím dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jak spustit provádí, prochází následujících fází:

- **Příprava**: Podle TensorFlow odhaduje se vytvoří image dockeru. Na obrázku je odeslat do registru kontejnerů pracovního prostoru a uložená v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spouštění a lze je zobrazit můžete sledovat průběh.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spustit, než je aktuálně k dispozici více uzlů.

- **Spuštění**: Všechny skripty ve složce script se nahrají do cílové výpočetní prostředí, úložiště dat se připojí, tedy zkopírují a entry_script provádí. Výstupy z výstupu stdout a. / složky protokolů se streamují do historie spouštění a slouží k monitorování spuštění.

- **Následné zpracování**: . / Výstupy, složka spuštění se kopíruje do historie spuštění.

## <a name="register-the-model"></a>Zaregistrujte model

Když jsme natrénovali model, můžete ho zaregistrovat do pracovního prostoru. Registrace modelu vám umožní úložiště a verzí vašich modelů v pracovním prostoru pro zjednodušení [model nasazení a správu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Můžete také stáhnout místní kopii modelu. To může být užitečné k provádění další model ověřování místně práce. Ve skriptu školení `mnist-keras.py`, objekt spořič TensorFlow nevyřeší modelu do místní složky (místní cílové výpočetní prostředí). Stáhnout kopii z úložiště dat můžete spustit objektu.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Další postup

V tomto článku školení a zaregistrován Keras modelu ve službě Azure Machine Learning. Informace o nasazení modelu najdete dál náš model nasazení článkem.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
