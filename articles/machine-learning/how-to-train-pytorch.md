---
title: Školení modelů PyTorch s hlubokým učením
titleSuffix: Azure Machine Learning
description: Naučte se, jak spustit školicí skripty PyTorch v podnikovém měřítku pomocí třídy odhadu PyTorch azure machine learningu.  Příklad skripty klasifikovat kuřecí a krůtí obrázky vybudovat hluboké učení neuronové sítě založené na přenosu PyTorch učení tutorial.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834862"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Vyškolte modely hlubokého učení Pytorch ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit vaše [školicí skripty PyTorch](https://pytorch.org/) v podnikovém měřítku pomocí [třídy odhadu PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) azure machine learningu.  

Příklad skripty v tomto článku se používají ke klasifikaci kuřecí a krůtí obrázky vybudovat hluboké učení neuronové sítě založené na přenosu PyTorch učení [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html). 

Ať už trénujete model PyTorch s hlubokým učením od základů nebo přinášíte do cloudu existující model, můžete pomocí Azure Machine Learning škálovat úlohy s otevřeným zdrojovým kódem pomocí elastických cloudových výpočetních prostředků. Pomocí Azure Machine Learning můžete vytvářet, nasazovat, předvádět a monitorovat produkční modely. 

Další informace o [hloubkovém učení vs. strojové učení](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v některém z těchto prostředí:

- Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

    - Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.
    - Ve složce ukázky hlubokévýuky na serveru notebooku najděte vyplněný a rozšířený poznámkový blok tak, že přejdete do tohoto adresáře: **> s použitím azureml, který se učí s podrobným učením > vlak-hyperparameter-tune-deploy-with-pytorch.** 
 
 - Váš vlastní Jupyter Notebook server

    - [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptů](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Můžete také najít vyplněnou [verzi jupyterského poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) této příručky na stránce ukázek GitHub. Poznámkový blok obsahuje rozšířené sekce, které zahrnují inteligentní ladění hyperparametrů, nasazení modelu a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastaví trénovací experiment načtením požadovaných balíčků pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním trénovacích dat a trénovacích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte potřebné knihovny Pythonu.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Inicializaci pracovního prostoru

[Pracovní prostor Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje vám centralizované místo pro práci se všemi artefakty, které vytvoříte. V pythonu SDK můžete přistupovat k artefaktům [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) pracovního prostoru vytvořením objektu.

Vytvořte objekt pracovního `config.json` prostoru ze souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hlubokým učením

Vytvořte experiment a složku pro uložení školicích skriptů. V tomto příkladu vytvořte experiment s názvem "pytorch-birds".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Získání dat

Datová sada se skládá z asi 120 trénovacích obrázků pro krůty a kuřata, se 100 ověřovacími obrázky pro každou třídu. Budeme stahovat a extrahovat datovou sadu `pytorch_train.py`jako součást našeho trénovacího skriptu . Obrazy jsou podmnožinou [datové sady Open Images v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Příprava školicích skriptů

V tomto kurzu je `pytorch_train.py`již k dispozici školicí skript , . V praxi můžete vzít libovolný vlastní školicí skript, jak je, a spustit jej s Azure Machine Learning.

Nahrajte tréninkový skript Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Pokud však chcete použít azure machine learning sledování a metriky možnosti, budete muset přidat malé množství kódu uvnitř školicího skriptu. Příklady měření metrik lze nalézt `pytorch_train.py`v .

## <a name="create-a-compute-target"></a>Vytvoření výpočetního cíle

Vytvořte výpočetní cíl, na který se má vaše úloha PyTorch spustit. V tomto příkladu vytvořte výpočetní cluster Azure Machine Learning s podporou GPU.

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

## <a name="create-a-pytorch-estimator"></a>Vytvoření odhadu PyTorch

[PyTorch odhad poskytuje](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) jednoduchý způsob, jak zahájit pytorch školení práci na výpočetní cíl.

Odhad PyTorch je implementován [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) prostřednictvím obecné třídy, kterou lze použít pro podporu libovolného rámce. Další informace o trénovacích modelech pomocí obecného odhadu najdete v tématu [trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md)

Pokud váš školicí skript potřebuje další pip nebo conda balíčky ke spuštění, můžete mít `pip_packages` `conda_packages` balíčky nainstalované na výsledné image docker ujeho předávání jejich názvy prostřednictvím a argumenty.

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

Další informace o přizpůsobení prostředí Pythonu najdete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md).

## <a name="submit-a-run"></a>Odeslat běh

Run [Objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historii spuštění, zatímco úloha je spuštěna a po dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Jako run je proveden, prochází následujícími fázemi:

- **Příprava**: Obrázek dockeru je vytvořen podle odhadu PyTorch. Bitová kopie se nahraje do registru kontejnerů pracovního prostoru a uloží do mezipaměti pro pozdější spuštění. Protokoly jsou také přenášeny do historie spuštění a lze je zobrazit za účelem sledování průběhu.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spuštění více uzlů, než je aktuálně k dispozici.

- **Spuštění**: Všechny skripty ve složce skriptu jsou odeslány do výpočetního cíle, úložiště dat jsou připojena nebo zkopírována a entry_script se spustí. Výstupy ze složky stdout a ./logs jsou přenášeny do historie spuštění a lze je použít ke sledování běhu.

- **Post-Processing**: Složka ./výstupy spuštění je zkopírována do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Jakmile model vycvičíte, můžete jej zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Model, který jste právě zaregistrovali, se nasadí přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který odhad jste použili pro školení. Postup nasazení obsahuje oddíl o registraci modelů, ale můžete přeskočit přímo k [vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

Můžete také stáhnout místní kopii modelu pomocí Spustit objekt. V trénovací skript `pytorch_train.py`, PyTorch uložit objekt uchovává model do místní složky (místní výpočetní cíl). Ke stažení kopie můžete použít objekt Spustit.

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

Odhad [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) také podporuje distribuované školení napříč clustery CPU a GPU. Můžete snadno spouštět distribuované úlohy PyTorch a Azure Machine Learning bude spravovat orchestraci za vás.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je open-source, to vše snižuje rámec pro distribuované školení vyvinuté společností Uber. Nabízí snadnou cestu k distribuovaným úlohám GPU PyTorch.

Chcete-li použít Horovod, zadejte [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt pro `distributed_training` parametr v konstruktoru PyTorch. Tento parametr zajišťuje, že horovodská knihovna je nainstalována pro použití v tréninkovém skriptu.


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
Horovod a jeho závislosti budou nainstalovány pro vás, takže `train.py` jej můžete importovat do školicího skriptu takto:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Export ovat do ONNX

Chcete-li optimalizovat odvození pomocí [zařízení ONNX Runtime](concept-onnx.md), převeďte trénovaný model PyTorch do formátu ONNX. Odvození nebo vyhodnocování modelu je fáze, kde se nasazený model používá pro předpověď, nejčastěji na produkčních datech. Podívejte se na [příklad tutoriálu.](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)

## <a name="next-steps"></a>Další kroky

V tomto článku jste vyškolili a zaregistrovali neural síť s hloubkovým učením pomocí PyTorch ve službě Azure Machine Learning. Chcete-li se dozvědět, jak nasadit model, pokračujte v našem článku nasazení modelu.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Referenční architektura pro distribuované školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

