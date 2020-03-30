---
title: Trénujte modely chainerů s hlubokým učením
titleSuffix: Azure Machine Learning
description: Zjistěte, jak spustit školicí skripty PyTorch v podnikovém měřítku pomocí třídy odhadu Azure Machine Learning Chainer.  Ukázkový skript klasifikuje ručně psané číslice obrázky k vytvoření neural sítě s hlubokým učením pomocí knihovny Chainer Python, která běží nad numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536613"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Trénování a registrace modelů Chainer ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit skripty školení [Chainer](https://chainer.org/) v podnikovém měřítku pomocí třídy odhadu Azure Machine Learning [Chainer.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) Ukázkový trénovací skript v tomto článku používá oblíbenou [datovou sadu MNIST](http://yann.lecun.com/exdb/mnist/) ke klasifikaci ručně psaných číslic pomocí hluboké neuronové sítě (DNN) vytvořené pomocí knihovny Chainer Python spuštěné nad [numpy](https://www.numpy.org/).

Ať už trénujete model chaineru s hlubokým učením od základu nebo přinášíte do cloudu existující model, můžete pomocí Azure Machine Learning škálovat úlohy školení s otevřeným zdrojovým kódem pomocí elastických cloudových výpočetních prostředků. Pomocí Azure Machine Learning můžete vytvářet, nasazovat, předvádět a monitorovat produkční modely. 

Další informace o [hloubkovém učení vs. strojové učení](concept-deep-learning-vs-machine-learning.md).

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v některém z těchto prostředí:

- Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

    - Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.
    - Ve složce ukázky hluboké učení na serveru notebooku najděte dokončený poznámkový blok a soubory ve složce **> ml-frameworks s použitím jak to použít, > chainer > nasazení > složky train-hyperparameter-tune-deploy-with-chainer.**  Poznámkový blok obsahuje rozšířené sekce, které zahrnují inteligentní ladění hyperparametrů, nasazení modelu a widgety poznámkových bloků.

- Váš vlastní Jupyter Notebook server

    - [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Stáhněte si ukázkový soubor skriptu [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Můžete také najít vyplněnou [verzi jupyterského notebooku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) této příručky na stránce ukázek GitHub. Poznámkový blok obsahuje rozšířené sekce, které zahrnují inteligentní ladění hyperparametrů, nasazení modelu a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastaví trénovací experiment načtením požadovaných balíčků pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním trénovacích dat a trénovacích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte knihovnu pythonu azureml.core a zobrazte číslo verze.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializaci pracovního prostoru

[Pracovní prostor Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje vám centralizované místo pro práci se všemi artefakty, které vytvoříte. V pythonu SDK můžete přistupovat k artefaktům [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) pracovního prostoru vytvořením objektu.

Vytvořte objekt pracovního `config.json` prostoru čtením souboru vytvořeného v [části požadavky](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Vytvoření adresáře projektu
Vytvořte adresář, který bude obsahovat veškerý potřebný kód z místního počítače, ke kterému budete potřebovat přístup ke vzdálenému prostředku. To zahrnuje školicí skript a všechny další soubory, na kterých závisí školicí skript.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Příprava školicího skriptu

V tomto kurzu je pro vás již k dispozici školicí skript **chainer_mnist.py.** V praxi byste měli být schopni přijmout všechny vlastní trénovací skript, jak je a spustit jej s Azure ML bez nutnosti měnit kód.

Pokud chcete používat možnosti sledování a metrik Azure ML, přidejte do školicího skriptu malé množství kódu Azure ML.  Trénovací skript **chainer_mnist.py** ukazuje, jak protokolovat některé `Run` metriky do vašeho Azure ML spustit pomocí objektu ve skriptu.

Poskytnutý trénovací skript používá ukázková data z funkce chainer. `datasets.mnist.get_mnist`  Pro vlastní data může být nutné použít kroky, jako je [například nahrát datovou sadu a skripty,](how-to-train-keras.md#data-upload) aby byla data k dispozici během školení.

Zkopírujte školicí skript **chainer_mnist.py** do adresáře projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hlubokým učením

Vytvoření experimentu V tomto příkladu vytvořte experiment s názvem "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Vytvoření nebo získání výpočetního cíle

Potřebujete [výpočetní cíl](concept-compute-target.md) pro trénování modelu. V tomto příkladu použijete spravované výpočetní prostředky Azure ML (AmlCompute) pro váš výpočetní prostředek pro vzdálené školení.

**Vytvoření AmlCompute trvá přibližně 5 minut**. Pokud AmlCompute s tímto názvem je již ve vašem pracovním prostoru, tento kód přeskočí proces vytváření.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Další informace o výpočetních cílech najdete v článku, [co je cíl výpočetního cíle.](concept-compute-target.md)

## <a name="create-a-chainer-estimator"></a>Vytvoření chainerového odhadu

Odhadl [chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) poskytuje jednoduchý způsob spuštění úlohy školení Chainer na váš výpočetní cíl.

Chainer odhad je implementována [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) prostřednictvím obecné třídy, které lze použít pro podporu jakékoli rámce. Další informace o trénovacích modelech pomocí obecného odhadu najdete v tématu [trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Odeslat běh

Run [Objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historii spuštění, zatímco úloha je spuštěna a po dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jako run je proveden, prochází následujícími fázemi:

- **Příprava**: Obrázek dockeru je vytvořen podle odhadu chaineru. Bitová kopie se nahraje do registru kontejnerů pracovního prostoru a uloží do mezipaměti pro pozdější spuštění. Protokoly jsou také přenášeny do historie spuštění a lze je zobrazit za účelem sledování průběhu.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spuštění více uzlů, než je aktuálně k dispozici.

- **Spuštění**: Všechny skripty ve složce skriptu jsou odeslány do výpočetního cíle, úložiště dat jsou připojena nebo zkopírována a entry_script se spustí. Výstupy ze složky stdout a ./logs jsou přenášeny do historie spuštění a lze je použít ke sledování běhu.

- **Post-Processing**: Složka ./výstupy spuštění je zkopírována do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po vyškolení modelu jej můžete uložit a zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md).


Po dokončení trénování modelu zaregistrujte model do pracovního prostoru pomocí následujícího kódu.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Model, který jste právě zaregistrovali, se nasadí přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který odhad jste použili pro školení. Postup nasazení obsahuje oddíl o registraci modelů, ale můžete přeskočit přímo k [vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

Můžete si také stáhnout místní kopii modelu. To může být užitečné pro provedení další ověření modelu pracovat místně. V trénovacím skriptu `chainer_mnist.py`objekt uchovává objekt spořiče model do místní složky (místní podle cíle výpočetního prostředí). Objekt Spustit můžete použít ke stažení kopie z úložiště dat.

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

V tomto článku jste vyškolili a zaregistrovali neural síť s hloubkovým učením pomocí Chaineru v Azure Machine Learning. Chcete-li se dozvědět, jak nasadit model, pokračujte v našem článku [nasazení modelu.](how-to-deploy-and-where.md)

* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)

* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)

* [Prohlédněte si naši referenční architekturu pro distribuované školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
