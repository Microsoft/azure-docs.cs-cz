---
title: Výuková neuronovéá síť pomocí zřetězení
titleSuffix: Azure Machine Learning
description: Naučte se spouštět PyTorch školicí skripty v podnikovém měřítku pomocí třídy Estimator zřetězení pro Azure Machine Learning.  Ukázkový skript klasifikuje ručně psané obrázky číslic, aby se vytvořila neuronové síť s hloubkovým učením pomocí knihovny Pythonu pro zřetězení, která běží nad numpy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: 70d6bd9507670a8846b2a79509b6b6e571f17e37
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710090"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Škálujte a Registrujte modely řetězení ve velkém měřítku pomocí Azure Machine Learning

V tomto článku se dozvíte, jak spustit školicí skripty pro [řetězení](https://chainer.org/) v podnikovém měřítku pomocí třídy [Estimator zřetězení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) pro Azure Machine Learning. Ukázkový skript pro školení v tomto článku používá oblíbenou [datovou sadu mnist ručně zapsaných](http://yann.lecun.com/exdb/mnist/) ke klasifikaci psaných číslic pomocí hluboké neuronové sítě (DNN) vytvořené pomocí knihovny Pythonu v chainu, která běží na [numpy](https://www.numpy.org/).

Bez ohledu na to, jestli provedete výukový model služby hloubkového učení z provozu nebo přenášíte existující model do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh s využitím elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat a monitorovat modely produkčního prostředí. 

Přečtěte si další informace o službě [hloubkového učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:

- Virtuální počítač s poznámkovým blokem Azure Machine Learning – nemusíte stahovat nebo instalovat

    - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce s ukázkami hloubkového učení na serveru poznámkového bloku vyhledejte dokončený Poznámkový blok a soubory ve složce **How-to->-azureml ml-framework > chainer >ho nasazení >** .  Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

- Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Stáhněte si vzorový soubor skriptu [chainer_mnist. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py).
     - Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) tohoto průvodce můžete najít na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním školicích dat a školicích skriptů.

### <a name="import-packages"></a>Importovat balíčky

Nejprve importujte knihovnu aplikace AzureML. Core Python a zobrazte číslo verze.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK můžete získat přístup k artefaktům pracovního prostoru vytvořením objektu [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Vytvořte objekt pracovního prostoru načtením souboru `config.json` vytvořeného v [části požadavky](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Vytvořit adresář projektu
Vytvořte adresář, který bude obsahovat veškerý potřebný kód z místního počítače, ke kterému budete potřebovat přístup ze vzdáleného prostředku. To zahrnuje školicí skript a všechny další soubory, na kterých je váš školicí skript závislý.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Příprava školicího skriptu

V tomto kurzu jste si už pro vás poskytli školicí skript **chainer_mnist. py** . V praxi byste měli být schopni vzít libovolný vlastní školicí skript a spustit ho s Azure ML bez nutnosti upravovat kód.

Pokud chcete používat funkce sledování a metrik Azure ML, přidejte do školicího skriptu malý objem kódu Azure ML.  Školicí skript **chainer_mnist. py** ukazuje, jak zaznamenat některé metriky do běhu Azure ml pomocí objektu `Run` v rámci skriptu.

Zadaný školicí skript používá ukázková data z funkce chainer `datasets.mnist.get_mnist`.  Pro vlastní data možná budete muset použít kroky, jako je například [nahrát datovou sadu a skripty](how-to-train-keras.md#data-upload) k zpřístupnění dat během školení.

Zkopírujte školicí skript **chainer_mnist. py** do adresáře projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hloubkovým učením

Vytvořte experiment. V tomto příkladu vytvořte experiment nazvaný "chainer-mnist ručně zapsaných".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Vytvořit nebo získat cíl výpočtů

Potřebujete [výpočetní cíl](concept-compute-target.md) pro školení modelu. V tomto příkladu použijete Azure ML Managed COMPUTE (AmlCompute) pro výpočetní prostředek vzdáleného školení.

**Vytváření AmlCompute trvá přibližně 5 minut**. Pokud je AmlCompute s tímto názvem již ve vašem pracovním prostoru, tento kód přeskočí proces vytváření.  

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

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Vytvoření Estimator zřetězení

[Estimator zřetězení](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) poskytuje jednoduchý způsob spouštění školicích úloh zřetězení pro výpočetní cíl.

Estimator zřetězení je implementována prostřednictvím obecné třídy [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

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

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Po spuštění se spustí v následujících fázích:

- **Příprava**: obrázek Docker se vytvoří podle Estimator zřetězení. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka./Outputs se v běhu kopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).


Po dokončení školení modelu zaregistrujte model do svého pracovního prostoru pomocí následujícího kódu.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Pokud se zobrazí chyba, že model nebyl nalezen, dejte mu minutu a akci opakujte.  Někdy existuje mírné prodleva mezi koncem školicího běhu a dostupností modelu v adresáři výstupy.

Můžete si také stáhnout místní kopii modelu. To může být užitečné pro místní práci s ověřováním modelu. Ve školicím skriptu `chainer_mnist.py`, objekt spořiče uchovává model do místní složky (místní k cíli výpočtů). Pomocí objektu spustit můžete stáhnout kopii z úložiště dat.

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

V tomto článku jste si naučili a zaregistrovali obsáhlý Learning, neuronové síť pomocí zřetězení na Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek [nasazení modelu](how-to-deploy-and-where.md) .

* [Ladit parametry](how-to-tune-hyperparameters.md)

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)

* [Podívejte se na naši referenční architekturu distribuovaného školení pro hloubkové učení v Azure.](/azure/architecture/reference-architectures/ai/training-deep-learning)
