---
title: Keras modely výukového vzdělávání
titleSuffix: Azure Machine Learning
description: Naučte se naučit a registrovat model klasifikace Keras hloubkové neuronové sítě běžící na TensorFlow pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518360"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Keras se škálováním modelů pomocí Azure Machine Learning

V tomto článku se dozvíte, jak spustit Keras školicí skripty pomocí Azure Machine Learning.

Vzorový kód v tomto článku vám ukáže, jak vytvořit model klasifikace Keras sestavený pomocí back-endu TensorFlow s Azure Machine Learning. Používá oblíbenou [datovou sadu mnist ručně zapsaných](http://yann.lecun.com/exdb/mnist/) ke klasifikaci psaných číslic pomocí neuronové sítě (DNN) sestavené pomocí [Keras Python Library](https://keras.io) běžící nad [TensorFlow](https://www.tensorflow.org/overview).

Keras je neuronové síťové rozhraní API, které se dá používat v horní části dalších oblíbených DNN architektur pro zjednodušení vývoje. Pomocí Azure Machine Learning můžete rychle škálovat školicí úlohy pomocí výpočetních prostředků elastického cloudu. Můžete také sledovat vaše školicí běhy, modely verzí, nasazovat modely a mnohem víc.

Bez ohledu na to, jestli vyvíjíte model Keras z provozu nebo přenášíte existující model do cloudu, vám Azure Machine Learning může pomáhat při sestavování modelů připravených pro produkční prostředí.

> [!NOTE]
> Pokud používáte rozhraní Keras API **TF. Keras** , které je integrované v TensorFlow, a ne na samostatný balíček Keras, přečtěte si místo toho, abyste mohli [naučit TensorFlow modely](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Předpoklady

Spusťte tento kód v jednom z těchto prostředí:

- Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

     - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce Samples na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete na tento adresář: How-to---- **> ml-frameworks > keras > výuka – keras** .

 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` ani `utils.py`

    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experimenty školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením datové sady pro vstupní školicí data, vytvořením cíle služby COMPUTE a definováním školicího prostředí.

### <a name="import-packages"></a>Import balíčků

Nejdřív importujte nezbytné knihovny Pythonu.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Vytvoření datové sady souborů

`FileDataset`Objekt odkazuje na jeden nebo více souborů v úložišti dat pracovního prostoru nebo veřejných adresách URL. Soubory mohou být libovolného formátu a třída poskytuje možnost stahovat nebo připojovat soubory do výpočtů. Vytvořením vytvoříte `FileDataset` odkaz na umístění zdroje dat. Pokud jste v sadě dat použili jakékoli transformace, budou uloženy i v datové sadě. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště. Další informace najdete v průvodci [vytvořením](./how-to-create-register-datasets.md) `Dataset` balíčku.

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

Pomocí `register()` metody můžete zaregistrovat datovou sadu do svého pracovního prostoru, aby bylo možné je sdílet s ostatními, znovu použít v různých experimentech a v rámci školicího skriptu, který se nazývá název.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů

Vytvořte výpočetní cíl pro spuštění úlohy školení. V tomto příkladu vytvoříte výpočetní cluster Azure Machine Learning s podporou GPU.

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

### <a name="define-your-environment"></a>Definice prostředí

Definujte [prostředí](concept-environments.md) Azure ml, které zapouzdřuje závislosti školicího skriptu.

Nejprve v souboru YAML definujte závislosti conda; v tomto příkladu je soubor pojmenován `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Vytvořte prostředí Azure ML z této specifikace prostředí conda. Prostředí se zabalí do kontejneru Docker za běhu.

Ve výchozím nastavení, pokud není zadaná žádná základní image, Azure ML použije image procesoru `azureml.core.environment.DEFAULT_CPU_IMAGE` jako základní image. Vzhledem k tomu, že tento příklad spustí školení v clusteru GPU, budete muset zadat základní image GPU, která má potřebné ovladače a závislosti GPU. Azure ML udržuje sadu základních imagí publikovaných v Microsoft Container Registry (MCR), které můžete použít, další informace najdete v úložišti GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Další informace o vytváření a používání prostředí najdete v tématu [vytváření a používání softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurace a odeslání školicích běhů

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig
Nejprve Získejte data z úložiště dat pracovního prostoru pomocí `Dataset` třídy.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Vytvořte objekt ScriptRunConfig a zadejte podrobnosti o konfiguraci školicí úlohy, včetně vašeho školicího skriptu, prostředí, které se má použít, a výpočetní cíl, který se má spustit.

Jakékoli argumenty školicího skriptu budou předány prostřednictvím příkazového řádku, pokud je zadáno v `arguments` parametru. DatasetConsumptionConfig pro naši datovou sadu je předán jako argument skriptu školení pro `--data-folder` argument. Azure ML tento DatasetConsumptionConfig vyřeší na přípojný bod záložního úložiště dat, ke kterému se pak dostanete ze školicího skriptu.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Další informace o konfiguraci úloh pomocí ScriptRunConfig najdete v tématu [Konfigurace a odeslání školicích běhů](how-to-set-up-training-targets.md).

> [!WARNING]
> Pokud jste dříve používali TensorFlow Estimator ke konfiguraci školicích úloh Keras, Upozorňujeme, že odhady byly zastaralé jako verze sady 1.19.0 SDK. S Azure ML SDK >= 1.15.0, ScriptRunConfig je doporučeným způsobem konfigurace školicích úloh, včetně těch, které používají architekturu pro hloubkové učení. Běžné otázky týkající se migrace najdete v [Průvodci migrací Estimator do ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Odeslat běh

[Objekt Run](/python/api/azureml-core/azureml.core.run%28class%29) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co se stane při spuštění
Po spuštění se spustí v následujících fázích:

- **Příprava**: image Docker se vytvoří podle definovaného prostředí. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu. Pokud je místo toho zadáno spravované prostředí, bude použit obrázek uložený v mezipaměti, který bude toto prostředí obsahovat.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a `script` spustí se. Výstupy z stdout a složky **./logs** se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka **./Outputs** se v běhu kopíruje do historie spuštění.

## <a name="register-the-model"></a>Registrace modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

Můžete si také stáhnout místní kopii modelu. To může být užitečné pro místní práci s ověřováním modelu. Ve školicím skriptu `keras_mnist.py` objekt TensorFlow spořiče uchovává model do místní složky (místní k cíli výpočtů). Pomocí objektu Run můžete stáhnout kopii z historie spuštění.

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

V tomto článku jste si vyškole a zaregistrovali Keras model na Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek nasazení modelu.

* [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
