---
title: Neuronové síť s hloubkovým učením pomocí PyTorch
titleSuffix: Azure Machine Learning service
description: Naučte se spouštět školicí skripty PyTorch v podnikovém měřítku pomocí třídy PyTorch Estimator v Azure Machine Learning.  Ukázkové skripty klasifikují obrázky od kuřecích a Turecka a vytvářejí neuronovéou síť s hloubkovým učením na základě kurzu přenosu PyTorch.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: d7ac3675ec9d90fc51bc9e3c72b76d8fb80312a8
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966788"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Naučte se škálovat modely Pytorch hloubkového učení s využitím Azure Machine Learning

V tomto článku se dozvíte, jak spustit školicí skripty [PyTorch](https://pytorch.org/) v podnikovém měřítku pomocí třídy [PyTorch Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) v Azure Machine Learning.  

Ukázkové skripty v tomto článku se používají ke klasifikaci kuřecích a Turecko imagí a k vytvoření neuronové sítě s hloubkovým učením založeného na [kurzu](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)přenosu PyTorch. 

Bez ohledu na to, jestli školicíte model PyTorch pro obsáhlý Learning od základu nebo přenášíte stávající model do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat a monitorovat modely produkčního prostředí. 

Přečtěte si další informace o službě [hloubkového učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:

 - Virtuální počítač s poznámkovým blokem Azure Machine Learning – nemusíte stahovat nebo instalovat

    - Dokončete [kurz: Nastavte prostředí a pracovní](tutorial-1st-experiment-sdk-setup.md) prostor pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného pomocí sady SDK a ukázkového úložiště.
    - Ve složce s přehledem hloubkového učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete na tento adresář: **postupy-použití-azureml > školení – with-learning > výuka – pytorch – parametr-Tune-Deploy-with-** . 
 
 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním školicích dat a školicích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejdřív importujte nezbytné knihovny Pythonu.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Pracovní prostor služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hloubkovým učením

Vytvořte experiment a složku, do které se budou ukládat skripty pro školení. V tomto příkladu vytvořte experiment nazvaný "pytorch-ptáci".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Získání dat

Datová sada se skládá z přibližně 120 školicích snímků každého pro krůty a kuřat a s imagemi na 100 ověření pro každou třídu. Datovou sadu budeme stahovat a extrahovat jako součást našeho školicího skriptu `pytorch_train.py`. Bitové kopie jsou podmnožinou [datové sady otevřených imagí 5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Příprava školicích skriptů

V tomto kurzu už je k dispozici `pytorch_train.py`školicí skript. V praxi můžete použít libovolný vlastní školicí skript, jak je, a spustit ho pomocí Azure Machine Learning služby.

Nahrajte školicí skript Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Pokud ale chcete použít možnosti sledování a metriky služby Azure Machine Learning, budete muset do školicího skriptu přidat malý kód. Příklady sledování metrik najdete v `pytorch_train.py`části.

## <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Vytvořte výpočetní cíl pro úlohu PyTorch, na které se má spustit. V tomto příkladu vytvoříte výpočetní cluster Azure Machine Learning s podporou GPU.

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

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

## <a name="create-a-pytorch-estimator"></a>Vytvoření PyTorch Estimator

[PyTorch Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) poskytuje jednoduchý způsob, jak spustit školicí úlohu PyTorch na cílovém výpočetním cíli.

PyTorch Estimator je implementován prostřednictvím obecné [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy, kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete mít balíčky nainstalované ve výsledné imagi Docker tím, že předáte jejich názvy pomocí `pip_packages` argumentů a. `conda_packages`

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

## <a name="submit-a-run"></a>Odeslat běh

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Po spuštění se spustí v následujících fázích:

- Připravuje se: Obrázek Docker se vytvoří podle PyTorch Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování:** Cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: Všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: Složka s příponou./Outputs se zkopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Místní kopii modelu můžete také stáhnout pomocí objektu run. Ve školicím skriptu `pytorch_train.py`objekt PyTorch Save uchovává model do místní složky (místní do výpočetního cíle). Kopii můžete stáhnout pomocí objektu spustit.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuované trénování

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Estimator také podporuje distribuované školení v rámci clusterů procesoru a GPU. Můžete snadno spouštět distribuované úlohy PyTorch a služba Azure Machine Learning bude orchestrace spravovat za vás.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je open source, což snižuje rámec pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k distribuovaným úlohám PyTorch GPU.

Chcete-li použít Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) zadejte objekt `distributed_training` pro parametr v konstruktoru PyTorch. Tento parametr zajišťuje, že se knihovna Horovod nainstaluje pro použití ve školicím skriptu.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod a jeho závislosti se budou instalovat za vás, takže je můžete importovat do skriptu `train.py` školení následujícím způsobem:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportovat do ONNX

Chcete-li optimalizovat odvození s [modulem runtime ONNX](concept-onnx.md), převeďte vyškolený model PyTorch na formát ONNX. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. Příklad najdete v tomto [kurzu](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Další postup

V tomto článku jste si naučili a zaregistrovali neuronové síť s hloubkovým učením pomocí PyTorch ve službě Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek nasazení modelu.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
