---
title: Trénování a zaregistrujte Chainer modelů
titleSuffix: Azure Machine Learning service
description: Tento článek popisuje, jak pro trénování a zaregistrujte model Chainer pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 06/15/2019
ms.openlocfilehash: 5057a8cf6c8769761a1a1a9f513ec46d152289ab
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67488661"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning-service"></a>Trénování a registraci Chainer modely ve velkém měřítku ve službě Azure Machine Learning

Tento článek popisuje, jak pro trénování a zaregistrujte model Chainer pomocí služby Azure Machine Learning. Využívá Oblíbené [datovou sadu mnist ručně](http://yann.lecun.com/exdb/mnist/) klasifikovat ručně číslic, pomocí hluboké neuronové sítě (DNN) vytvořené pomocí [knihovna Python pro Chainer](https://Chainer.org) systémem nad [numpy](https://www.numpy.org/).

Chainer je základní rozhraní API fungovat v horní části dalších oblíbených architektur DNN ke zjednodušení vývoje neuronové sítě. Pomocí služby Azure Machine Learning můžete rychle horizontální navýšení kapacity pomocí elastických cloudových výpočetních prostředků úlohy trénování. Můžete také sledovat tréninkových spuštění, verze modely nasazení modelů a mnoho dalšího.

Ať už vyvíjíte modelu Chainer od základů nebo existující model spojili do cloudu, můžete pomocí služby Azure Machine Learning sestavovat modely připravené pro produkční prostředí.

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

Tento kód spusťte v jedné z těchto prostředí:

- Azure Machine Learning poznámkového bloku virtuálního počítače – žádné soubory ke stažení nebo instalace

    - Dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md) vytvořit poznámkový blok vyhrazený server už načtené pomocí sady SDK a ukázkové úložiště.
    - Ve složce samples na serveru Poznámkový blok, najít dokončené Poznámkový blok a soubory v **how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer** složky.  Poznámkový blok obsahuje rozšířené oddíly věnované inteligentní hyperparametrů, model nasazení a widgetů poznámkového bloku.

- Váš vlastní server poznámkového bloku Jupyter

    - [Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python](setup-create-workspace.md#sdk)
    - [Vytvořte konfigurační soubor pracovního prostoru](setup-create-workspace.md#write-a-configuration-file)
    - Stáhněte si ukázkový soubor skriptu [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/chainer_mnist.py)
     - Můžete také vyhledat dokončené [Poznámkový blok Jupyter verze](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) tohoto průvodce na stránku GitHub s ukázkami. Poznámkový blok obsahuje rozšířené oddíly věnované inteligentní hyperparametrů, model nasazení a widgetů poznámkového bloku.

## <a name="set-up-the-experiment"></a>Nastavení testu

Tato část výukového experimentu nastaví načtením balíčky požadované pythonu, inicializuje se pracovní prostor, vytvoření experimentu a nahrávání trénovacích dat a trénovací skripty.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte azureml.core Python knihovny ad zobrazit číslo verze.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Pracovního prostoru služby Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně služby. To vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte. V sadě Python SDK, dostanete artefakty pracovního prostoru tak, že vytvoříte [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvoření objektu z pracovního prostoru `config.json` soubor vytvořený v [části s předpoklady](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Vytvořit adresář projektu
Vytvoření adresáře, který bude obsahovat veškerý nezbytný kód z místního počítače, budete potřebovat přístup k na vzdálený prostředek. To zahrnuje cvičný skript a další soubory cvičný skript, na kterém závisí.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Příprava skriptu školení

V tomto kurzu se skript školení **chainer_mnist.py** je již k dispozici za vás. V praxi by měl být schopen převzít jakékoli vlastní cvičný skript a spustit s použitím Azure ML nemusíte upravovat kód.

Pokud chcete použít sledování Azure ML a možnosti metrik, budete muset přidat malé množství kódu Azure ML uvnitř cvičný skript.  Trénovací skript **chainer_mnist.py** ukazuje, jak se připojit k vaší Azure ML spustit některé metriky. Uděláte to tak, můžete přístup k Azure ML `Run` objekt v rámci skriptu.

Zkopírujte skript školení **chainer_mnist.py** do adresáře vašeho projektu.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvoření experimentu a složku pro uložení trénovací skripty. V tomto příkladu vytvoření experimentu nazývá "chainer mnist ručně".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Vytvořit nebo získat cílové výpočetní prostředí

Budete potřebovat [cílové výpočetní prostředí](concept-compute-target.md) pro trénování modelu. V tomto kurzu použijete Azure ML spravovat výpočetní prostředky (AmlCompute) pro vzdálené školení výpočetní prostředek.

**Vytvoření AmlCompute trvá přibližně 5 minut**. Pokud AmlCompute s tímto názvem již ve vašem pracovním prostoru, přeskočí tento kód v procesu vytváření.  

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

Další informace o cílových výpočetních prostředí najdete v článku [co je cílové výpočetní prostředí](concept-compute-target.md) článku.

## <a name="create-a-chainer-estimator"></a>Vytvoření odhadu Chainer

[Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) poskytuje jednoduchý způsob spuštění trénovací úlohy Chainer na vaše cílové výpočetní prostředí.

Odhad Chainer se implementuje pomocí obecného [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídu, která slouží k podpoře libovolné architektury. Další informace o školení modely s využitím obecných odhad, naleznete v tématu [trénování modelů Azure Machine Learning pomocí odhad](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Odeslat spustit

[Spustit objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historie spuštění, když úloha běží, a po jejím dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jak spustit provádí, prochází následujících fází:

- **Příprava**: Podle Chainer odhaduje se vytvoří image dockeru. Na obrázku je odeslat do registru kontejnerů pracovního prostoru a uložená v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spouštění a lze je zobrazit můžete sledovat průběh.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spustit, než je aktuálně k dispozici více uzlů.

- **Spuštění**: Všechny skripty ve složce script se nahrají do cílové výpočetní prostředí, úložiště dat se připojí, tedy zkopírují a entry_script provádí. Výstupy z výstupu stdout a. / složky protokolů se streamují do historie spouštění a slouží k monitorování spuštění.

- **Následné zpracování**: . / Výstupy, složka spuštění se kopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložit a zaregistrujte model

Když jsme natrénovali model, můžete uložit a zaregistrujte ho do pracovního prostoru. Registrace modelu vám umožní úložiště a verzí vašich modelů v pracovním prostoru pro zjednodušení [model nasazení a správu](concept-model-management-and-deployment.md).

Přidejte následující kód do vašeho skriptu školení **chainer_mnist.py**, chcete-li uložit model. 

``` Python
    serializers.save_npz(os.path.join(args.output_dir, 'model.npz'), model)
```

Zaregistrujte model do pracovního prostoru s následujícím kódem.

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```



## <a name="next-steps"></a>Další postup

V tomto článku se trénuje model Chainer ve službě Azure Machine Learning. 

* Informace o nasazení modelu najdete dál v našich [model nasazení](how-to-deploy-and-where.md) článku.

* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)

* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
