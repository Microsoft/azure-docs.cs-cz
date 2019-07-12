---
title: Trénování a zaregistrujte modely PyTorch
titleSuffix: Azure Machine Learning service
description: Tento článek popisuje, jak pro trénování a zaregistrujte model PyTorch pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: d9c953eeecedf14a8f3fae43c5d4713252d58b4c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840089"
---
# <a name="train-and-register-pytorch-models-at-scale-with-azure-machine-learning-service"></a>Trénování a registraci PyTorch modely ve velkém měřítku ve službě Azure Machine Learning

Tento článek popisuje, jak pro trénování a zaregistrujte model PyTorch pomocí služby Azure Machine Learning. Je založen na [přenos PyTorch společnosti learning kurzu](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) , která sestavení klasifikátoru hluboké neuronové sítě (DNN) pro Image kuřat a krůty.

[PyTorch](https://pytorch.org/) je open source výpočetní platforma běžně používá k vytvoření neuronových sítí (DNN). Pomocí služby Azure Machine Learning můžete rychle škálovat úlohy trénování open source pomocí elastických cloudových výpočetních prostředků. Můžete také sledovat tréninkových spuštění, verze modely nasazení modelů a mnoho dalšího.

Ať už vyvíjíte modelu PyTorch od základů nebo existující model spojili do cloudu, můžete pomocí služby Azure Machine Learning sestavovat modely připravené pro produkční prostředí.

## <a name="prerequisites"></a>Požadavky

Tento kód spusťte v jedné z těchto prostředí:

 - Azure Machine Learning poznámkového bloku virtuálního počítače – žádné soubory ke stažení nebo instalace

    - Dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md) vytvořit poznámkový blok vyhrazený server už načtené pomocí sady SDK a ukázkové úložiště.
    - Ve složce samples na serveru Poznámkový blok, najít poznámkový blok dokončené a rozšířená tak, že přejdete do tohoto adresáře: **postupy-k-použití azureml > školení s obsáhlého learningu > train-hyperparameter-tune-deploy-with-pytorch** složka. 
 
 - Váš vlastní server poznámkového bloku Jupyter

    - [Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python](setup-create-workspace.md#sdk)
    - [Vytvořte konfigurační soubor pracovního prostoru](setup-create-workspace.md#write-a-configuration-file)
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) `pytorch_train.py`
     
    Můžete také vyhledat dokončené [Poznámkový blok Jupyter verze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) tohoto průvodce na stránce ukázky na Githubu. Poznámkový blok obsahuje rozšířené oddíly věnované inteligentní hyperparametrů, model nasazení a widgetů poznámkového bloku.

## <a name="set-up-the-experiment"></a>Nastavení testu

Tato část výukového experimentu nastaví načtením balíčky požadované pythonu, inicializuje se pracovní prostor, vytvoření experimentu a nahrávání trénovacích dat a trénovací skripty.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte nezbytné knihovny jazyka Python.

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

[Pracovního prostoru služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně služby. To vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte. V sadě Python SDK, dostanete artefakty pracovního prostoru tak, že vytvoříte [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvoření objektu z pracovního prostoru `config.json` soubor vytvořený v [části s předpoklady](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvoření experimentu a složku pro uložení trénovací skripty. V tomto příkladu vytvoření experimentu nazývá "pytorch ptáci".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Získání dat

Datová sada se skládá z přibližně 120 trénovacích obrázků každý krůty a kuřat, s 100 ověřování imagí pro každou třídu. Budeme stáhnout a extrahovat datovou sadu jako součást skriptu školení `pytorch_train.py`. Bitové kopie jsou podmnožinou [datovou sadu otevřít Imagí v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Příprava trénovací skripty

V tomto kurzu se skript školení `pytorch_train.py`, je již k dispozici. V praxi můžete provést všechny vlastní cvičný skript, protože je a spuštění pomocí služby Azure Machine Learning.

Uložení skriptu školení Pytorch, `pytorch_train.py`.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Ale pokud byste chtěli použít sledování služby Azure Machine Learning a možnosti metrik, budete muset přidat malé množství kódu uvnitř cvičný skript. Příklady sledování metrik najdete v `pytorch_train.py`.

## <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Vytvořte cílové výpočetní prostředí pro vaši úlohu PyTorch ke spuštění na. V tomto příkladu vytvořte výpočetní cluster s podporou grafického procesoru Azure Machine Learning.

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

## <a name="create-a-pytorch-estimator"></a>Vytvoření odhadu PyTorch

[PyTorch estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) poskytuje jednoduchý způsob spuštění trénovací úlohy PyTorch na cílové výpočetní prostředí.

Odhad PyTorch se implementuje pomocí obecného [ `estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídu, která slouží k podpoře libovolné architektury. Další informace o školení modely s využitím obecných odhad, naleznete v tématu [trénování modelů Azure Machine Learning pomocí odhad](how-to-train-ml-models.md)

Pokud váš skript školení potřebuje další pip nebo conda balíčky ke spuštění, můžete mít balíčky, které jsou nainstalovány na výsledný image dockeru předáním názvů prostřednictvím `pip_packages` a `conda_packages` argumenty.

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

## <a name="submit-a-run"></a>Odeslat spustit

[Spustit objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historie spuštění, když úloha běží, a po jejím dokončení.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Jak spustit provádí, prochází následujících fází:

- **Příprava**: Podle PyTorch odhaduje se vytvoří image dockeru. Na obrázku je odeslat do registru kontejnerů pracovního prostoru a uložená v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spouštění a lze je zobrazit můžete sledovat průběh.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spustit, než je aktuálně k dispozici více uzlů.

- **Spuštění**: Všechny skripty ve složce script se nahrají do cílové výpočetní prostředí, úložiště dat se připojí, tedy zkopírují a entry_script provádí. Výstupy z výstupu stdout a. / složky protokolů se streamují do historie spouštění a slouží k monitorování spuštění.

- **Následné zpracování**: . / Výstupy, složka spuštění se kopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Zaregistrovat nebo stáhnout modelu

Když jsme natrénovali model, můžete ho zaregistrovat do pracovního prostoru. Registrace modelu vám umožní úložiště a verzí vašich modelů v pracovním prostoru pro zjednodušení [model nasazení a správu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

Můžete také stáhnout místní kopii modelu s použitím objektu spuštění. Ve skriptu školení `pytorch_train.py`, PyTorch uložit objekt nevyřeší modelu do místní složky (místní cílové výpočetní prostředí). Můžete stáhnout kopii objektu spuštění.

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

[ `PyTorch` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Estimator také podporuje distribuované trénování napříč clustery CPU a GPU. Můžete snadno spouštět distribuované PyTorch úlohy a služby Azure Machine Learning spravovat orchestraci za vás.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je open source, všechny snížit framework pro distribuované trénování vypracovanou organizací cccppf Uber. Nabízí Snadná cesta k distribuované úlohy GPU PyTorch.

Chcete-li použít Horovod, zadejte [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt pro `distributed_training` parametr v konstruktoru PyTorch. Tento parametr zajišťuje, že je nainstalován Horovod knihovny můžete použít ve skriptu školení.


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
Horovod a jeho závislosti se nainstaluje za vás, tak importujte ji do vašeho skriptu školení `train.py` následujícím způsobem:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportovat do ONNX

K optimalizaci odvození se [ONNX Runtime](concept-onnx.md), trénovaného modelu PyTorch převést do formátu ONNX. Odvození nebo vyhodnocení modelu je fáze použití nasazený model pro predikci, obvykle na produkční data. Zobrazit [kurzu](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) příklad.

## <a name="next-steps"></a>Další postup

V tomto článku školení a zaregistrován PyTorch modelu ve službě Azure Machine Learning. Informace o nasazení modelu najdete dál náš model nasazení článkem.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
