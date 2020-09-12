---
title: Výuka a nasazení výukového modelu pro posílení (Preview).
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning posílení učení (Preview) k výuce agenta RL k přehrání pong.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 97119f3ec370894183c4aee44ff8a5bd6e5ea93b
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647136"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Posílení učení (Preview) s Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Azure Machine Learning posílení učení je aktuálně funkcí verze Preview. V současné době jsou podporovány pouze architektury ray a RLlib.

V tomto článku se naučíte, jak pomocí agenta pro výuku RL (výztuže Learning) hrát pong hry pro video. Pro správu složitosti distribuovaných úloh RL budete používat open source [RLlib](https://ray.readthedocs.io/en/master/rllib.html) knihovny Pythonu s Azure Machine Learning.

V tomto článku se dozvíte, jak:
> [!div class="checklist"]
> * Nastavení experimentu
> * Definování hlav a pracovních uzlů
> * Vytvoření estimatoru RL
> * Odeslat experiment ke spuštění běhu
> * Zobrazení výsledků

Tento článek je založený na [příkladu RLlib pong](https://aka.ms/azureml-rl-pong) , který najdete v [úložišti GitHubu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)poznámkového bloku Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

Tento kód spusťte v jednom z následujících prostředí. Doporučujeme, abyste si vyzkoušeli Azure Machine Learning výpočetní instance pro nejrychlejší úvodní prostředí. K dispozici jsou ukázkové poznámkové bloky pro rychlé klonování a spouštění Azure Machine Learning výpočetní instance.

 - Výpočetní instance Azure Machine Learningu

     - Naučte se klonovat ukázkové poznámkové bloky v [kurzu: nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md).
         - Klonování složky s **postupy použití-AzureML** místo **kurzů**
     - Spusťte Poznámkový blok instalace virtuální sítě umístěný na adrese `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` a otevřete tak síťové porty používané pro distribuované výztuže.
     - Spuštění ukázkového poznámkového bloku `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Váš vlastní server Jupyter Notebook

    - Nainstalujte [sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
    - Nainstalujte [sadu Azure Machine Learning RL SDK](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/?view=azure-ml-py&preserve-view=true): `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Vytvořte [konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - Spusťte [Poznámkový blok instalace](https://aka.ms/azure-rl-env-setup) virtuální sítě a otevřete tak síťové porty, které se používají pro distribuované výztuže při výuce.


## <a name="how-to-train-a-pong-playing-agent"></a>Postup výuky pong agenta

Posílení učení (RL) je přístup ke strojovém učení, který se učí pomocí tohoto postupu. I když se jiné techniky strojového učení učí pomocí průchozího zadávání vstupních dat a hledáním vzorů v ní, RL používá **školicí agenty** k tomu, aby aktivně rozhodovat a dozvěděli se od jejich výsledků.

Vaše školicí agenti se naučí, aby se pong ve **simulovaném prostředí**. Školení agenti zavedou rozhodnutí, že každý rámec hry bude paddle nahoru, dolů nebo zůstat na pracovišti. Při rozhodování se vyhledá stav hry (obrázek RGB obrazovky).

RL využívá **k oznámení agenta, pokud** je jeho rozhodnutí úspěšné. V tomto prostředí získá agent pozitivní nepracovní hodnotu, pokud je vyhodnocen jako bod a záporná nepracovní hodnota, když se na něj vrátí bod. V rámci řady iterací se agent školení učí vybrat akci na základě aktuálního stavu, který se optimalizuje na součet očekávaných budoucích neprospěchů.

K provedení této optimalizace v RL je běžné použít model DNN ( **rozsáhlá neuronové síť** ). Na začátku bude mít agent učení nesplatně, ale každá hra bude vygenerovat další vzorky k dalšímu zlepšení modelu.

Školení skončí, když agent dosáhne průměrného skóre pro 18 v epocha školení. To znamená, že agent beaten svého protihráče na průměr nejméně 18 bodů v porovnání až 21.

Proces iterace díky simulaci a rekurzování DNN je výpočetně nákladný a vyžaduje velké objemy dat. Jedním ze způsobů, jak zlepšit výkon úloh RL, je **virtuálního práce** , aby několik školicích agentů mohli současně působit a učit se. Správa distribuovaného prostředí RL ale může být složitým podnikem.

Azure Machine Learning poskytuje rozhraní pro správu těchto složitých procesů pro horizontální navýšení kapacity úloh RL.

## <a name="set-up-the-environment"></a>Nastavení prostředí

Nastavte místní prostředí RL načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a zadáním nakonfigurované virtuální sítě.

### <a name="import-libraries"></a>Import knihoven

Naimportujte potřebné balíčky Pythonu pro spuštění zbytku tohoto příkladu.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro Azure Machine Learning. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte.

Inicializujte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites). Pokud tento kód spouštíte ve Azure Machine Learning výpočetní instanci, konfigurační soubor již byl vytvořen za vás.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Vytvořit výztužný experiment při výuce

Vytvořte [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) , který sleduje školicí běh. V Azure Machine Learning experimenty jsou logické kolekce souvisejících zkušebních verzí, které slouží k uspořádání protokolů spuštění, historie, výstupů a dalších.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Zadat virtuální síť

U úloh RL, které používají více výpočetních cílů, je nutné zadat virtuální síť s otevřenými porty, které umožňují vzájemnou komunikaci uzlů pracovních procesů a hlavních uzlů. Virtuální síť se může nacházet v jakékoli skupině prostředků, ale měla by být ve stejné oblasti jako váš pracovní prostor. Další informace o nastavení virtuální sítě najdete v [poznámkovém bloku instalace pracovního prostoru](https://aka.ms/azure-rl-env-setup) , který najdete v části požadavky. Tady zadáte název virtuální sítě ve vaší skupině prostředků.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definování cílových výpočetních hodnot hlav a pracovních procesů

V tomto příkladu se používají samostatné výpočetní cíle pro uzly paprskových hlav a pracovníků. Tato nastavení umožňují škálovat výpočetní prostředky nahoru a dolů v závislosti na očekávaném zatížení. Nastavte počet uzlů a velikost jednotlivých uzlů v závislosti na potřebách experimentů.

### <a name="head-computing-target"></a>Cíl hlavního výpočetního prostředí

V tomto příkladu se používá hlavní cluster s podporou GPU k optimalizaci výkonu hloubkového učení. Hlavní uzel nakládá neuronové síť, kterou agent používá k rozhodování. Hlavní uzel také shromažďuje datové body z pracovních uzlů pro další vzdělávání sítě neuronové.

Hlavní výpočetní prostředí používá jeden [ `STANDARD_NC6` virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/nc-series) (VM). Má 6 virtuálních procesorů, což znamená, že může distribuovat práci mezi 6 funkčních procesorů.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Cluster Computing Worker

Tento příklad používá pro výpočetní cíl pro pracovní proces čtyři [ `STANDARD_D2_V2` virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/nc-series) . Každý pracovní uzel má 2 dostupné procesory, celkem 8 dostupných procesorů, které paralelizovat práci.

GPU nejsou pro pracovní uzly nutné, protože neprovádějí důkladné učení. Pracovníci spouštějí simulace her a shromažďují data.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Vytvoření posílení výukového Estimator

V této části se dozvíte, jak pomocí [ReinforcementLearningEstimator](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?view=azure-ml-py&preserve-view=true) odeslat školicí úlohu Azure Machine Learning.

Azure Machine Learning používá třídy Estimator k zapouzdření informací o konfiguraci spuštění. To vám umožní snadno určit, jak se má nakonfigurovat provádění skriptu. Další informace o modelu Azure Machine Learning Estimator najdete v tématu [výuka modelů pomocí odhady](how-to-train-ml-models.md).

### <a name="define-a-worker-configuration"></a>Definování konfigurace pracovního procesu

Objekt WorkerConfiguration oznamuje Azure Machine Learning, jak inicializovat pracovní cluster, který spustí vstupní skript.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definovat parametry skriptu

Skript vstupu `pong_rllib.py` přijímá seznam parametrů, které definují, jak spustit úlohu školení. Předání těchto parametrů prostřednictvím Estimator jako vrstvy zapouzdření usnadňuje změnu parametrů skriptu a konfigurací spouštění nezávisle na sobě.

Určením správného nastavení `num_workers` bude vaše úsilí na maximum na maximum. Nastavte počet pracovních procesů na stejný jako počet dostupných procesorů. V tomto příkladu to můžete vypočítat následujícím způsobem:

Hlavní uzel je [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) s 6 vCPU. Pracovní cluster má 4 [Standard_D2_V2 virtuálních počítačů](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs#dv2-series) se dvěma procesory (celkem 8 procesorů). Je ale potřeba odečíst 1 procesor od počtu pracovních procesů, protože 1 se musí vyhradit roli hlavního uzlu. 6 procesorů + 8 procesorů – 1 hlavní procesor = 13 současných pracovních procesů. Azure Machine Learning využívá hlavní a pracovní clustery k odlišení výpočetních prostředků. Ray ale nerozlišuje mezi vedoucími a pracovními procesy a všechny procesory jsou dostupné CPU pro provádění pracovních vláken.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definování Estimator výukového kurzu

K vytvoření Estimator použijte seznam parametrů a objekt konfigurace pracovního procesu.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Vstupní skript

[Vstupní skript](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` navlacích neuronové síť pomocí [prostředí OpenAI posilovně](https://github.com/openai/gym/) `PongNoFrameSkip-v4` . OpenAI Gyms jsou standardizovaná rozhraní pro testování posílení výukových algoritmů v klasických Atari hrách.

V tomto příkladu se používá školicí algoritmus, který se označuje jako [Impala](https://arxiv.org/abs/1802.01561) (vážený význam architektury actor-učí). IMPALA parallelizes každý jednotlivý aktér výukového objektu pro škálování napříč mnoha výpočetními uzly, aniž by došlo ke ztrátě rychlosti nebo stability.

[Ray Intune](https://ray.readthedocs.io/en/latest/tune.html) orchestruje pracovní úkoly Impala.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Funkce zpětného volání protokolování


Skript vstupu používá funkci nástrojů k definování [vlastní funkce zpětného volání RLlib](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) pro protokolování metrik do vašeho pracovního prostoru Azure Machine Learning. Naučte se, jak tyto metriky zobrazit v části [monitorování a zobrazení výsledků](#monitor-and-view-results) .

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Odeslat běh

[Spustit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) zpracovává historii spuštění probíhajících nebo dokončených úloh. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Dokončení běhu může trvat až 30 až 45 minut.

## <a name="monitor-and-view-results"></a>Monitorování a zobrazení výsledků

Pomocí widgetu Azure Machine Learning Jupyter můžete zobrazit stav spuštění v reálném čase. V tomto příkladu Widget zobrazuje dvě podřízené běhy: jeden pro hlavní a jeden pro pracovní procesy. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Počkejte, než se widget načte.
1. V seznamu spuštění vyberte hlavní spuštění.

Vyberte **kliknutím sem zobrazíte informace o spuštění v Azure Machine Learning Studiu** , kde najdete další informace o spuštění v studiu. K těmto informacím můžete přistupovat v průběhu probíhajícího běhu nebo po jeho dokončení.

![Spojnicový graf znázorňující pomůcku podrobností o spuštění](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

**Episode_reward_mean** vykreslení znázorňuje průměrný počet bodů, které jsou vyhodnoceny na epocha školení. Vidíte, že se agent pro školení zpočátku vykonal, ztratí své shody bez bodování jednoho bodu (na reward_mean-21). V rámci 100 iterací se agent školení dozvěděl o tom, že má na začátku průměr 18 bodů.

Pokud procházíte protokoly podřízeného spuštění, můžete zobrazit výsledky vyhodnocení zaznamenané v souboru driver_log.txt. Než budou tyto metriky k dispozici na stránce spuštění, možná budete muset počkat několik minut.

V krátké práci jste se naučili nakonfigurovat více výpočetních prostředků, aby se mohl naučit posílit výukový Agent na hraní pong.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí agenta učení IMPALA vytvořit výukového agenta pro posílení. Pokud se chcete podívat na další příklady, přejděte [Azure Machine Learning na úložiště GitHub learningu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).
