---
title: PyTorch modely výukového vzdělávání
titleSuffix: Azure Machine Learning
description: Naučte se spouštět PyTorch školicí skripty v podnikovém měřítku pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b1cb14e07f6c0e402510abad6f1cb160f5215c63
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518377"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>PyTorch se škálováním modelů pomocí Azure Machine Learning

V tomto článku se dozvíte, jak spustit [PyTorch](https://pytorch.org/) školicí skripty v podnikovém měřítku pomocí Azure Machine Learning.

Ukázkové skripty v tomto článku se používají ke klasifikaci kuřecích a tureckých imagí k vytvoření neuronové sítě s hloubkovým učením (DNN) na základě [kurzu](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)přenosu PyTorch. Učení přenosu je technika, která používá znalostní bázi získanou z řešení jednoho problému na jiný, ale související problém. Tento postup je pomocný při školicím procesu, protože vyžaduje méně dat, čas a výpočetní prostředky než od nuly. Další informace o učení pro přenos najdete v článku o [podrobném učení vs Machine Learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) .

Bez ohledu na to, jestli školicíte model PyTorch pro obsáhlý Learning od základu nebo přenášíte stávající model do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat a monitorovat modely produkčního prostředí. 

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:

- Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

    - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce s ukázkami hloubkového učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok přechodem do tohoto adresáře: How-to-pytorch------ **> ml-framework > > výuka – pytorch** . 
 
 - Váš vlastní server Jupyter Notebook
    - [Nainstalujte sadu Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experimenty školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením cíle výpočtů a definováním školicího prostředí.

### <a name="import-packages"></a>Import balíčků

Nejdřív importujte nezbytné knihovny Pythonu.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Získání dat

Datová sada se skládá z přibližně 120 školicích snímků každého pro krůty a kuřat a s imagemi na 100 ověření pro každou třídu. Datovou sadu budeme stahovat a extrahovat jako součást našeho školicího skriptu `pytorch_train.py` . Bitové kopie jsou podmnožinou [datové sady otevřených imagí 5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Příprava školicího skriptu

V tomto kurzu `pytorch_train.py` už je k dispozici školicí skript. V praxi můžete použít libovolný vlastní školicí skript, jak je, a spustit ho pomocí Azure Machine Learning.

Vytvořte složku pro školicí skripty.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů

Vytvořte výpočetní cíl pro úlohu PyTorch, na které se má spustit. V tomto příkladu vytvoříte výpočetní cluster Azure Machine Learning s podporou GPU.

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

Pokud chcete definovat [prostředí](concept-environments.md) Azure ml, které zapouzdřuje závislosti školicího skriptu, můžete buď definovat vlastní prostředí, nebo použít prostředí Azure ml s využitím.

#### <a name="use-a-curated-environment"></a>Použití spravovaného prostředí
Azure ML poskytuje předem vytvořená a poduspořádaná prostředí, pokud nechcete definovat vlastní prostředí. Azure ML obsahuje několik PROCESORových a procesorových prostředí GPU pro PyTorch, která odpovídají různým verzím PyTorch. Další informace najdete [tady](resource-curated-environments.md).

Pokud chcete použít konkrétní prostředí, můžete místo toho spustit následující příkaz:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Pokud chcete zobrazit balíčky zahrnuté ve spravovaném prostředí, můžete na disk zapsat závislosti conda:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Ujistěte se, že ve spravovaném prostředí jsou všechny závislosti vyžadované školicím skriptem. V takovém případě budete muset upravit prostředí tak, aby obsahovalo chybějící závislosti. Všimněte si, že pokud je prostředí upraveno, budete ho muset dát novému názvu, protože předpona ' AzureML ' je vyhrazena pro prostředí, která jsou v něm uspořádaná. Pokud jste změnili soubor conda závislosti YAML, můžete z něj vytvořit nové prostředí s novým názvem, třeba:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Pokud jste místo toho změnili objekt s podmnožinou prostředí přímo, můžete toto prostředí klonovat pomocí nového názvu:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

#### <a name="create-a-custom-environment"></a>Vytvoření vlastního prostředí

Můžete také vytvořit vlastní prostředí Azure ML, které zapouzdřuje závislosti školicího skriptu.

Nejprve v souboru YAML definujte závislosti conda; v tomto příkladu je soubor pojmenován `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Vytvořte prostředí Azure ML z této specifikace prostředí conda. Prostředí se zabalí do kontejneru Docker za běhu.

Ve výchozím nastavení, pokud není zadaná žádná základní image, Azure ML použije image procesoru `azureml.core.environment.DEFAULT_CPU_IMAGE` jako základní image. Vzhledem k tomu, že tento příklad spustí školení v clusteru GPU, budete muset zadat základní image GPU, která má potřebné ovladače a závislosti GPU. Azure ML udržuje sadu základních imagí publikovaných v Microsoft Container Registry (MCR), které můžete použít, další informace najdete v úložišti GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Volitelně můžete zachytit všechny závislosti přímo ve vlastní imagi Docker nebo v souboru Dockerfile a vytvořit prostředí z něj. Další informace najdete v tématu [výuka s vlastní imagí](how-to-train-with-custom-image.md).

Další informace o vytváření a používání prostředí najdete v tématu [vytváření a používání softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurace a odeslání školicích běhů

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig

Vytvořte objekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) a zadejte podrobnosti o konfiguraci školicí úlohy, včetně vašeho školicího skriptu, prostředí, které se má použít, a výpočetní cíl, který se má spustit. Jakékoli argumenty školicího skriptu budou předány prostřednictvím příkazového řádku, pokud je zadáno v `arguments` parametru. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho ke svým datům dostanete přístup pomocí [datové sady](how-to-train-with-datasets.md)Azure ml.

Další informace o konfiguraci úloh pomocí ScriptRunConfig najdete v tématu [Konfigurace a odeslání školicích běhů](how-to-set-up-training-targets.md).

> [!WARNING]
> Pokud jste dříve používali PyTorch Estimator ke konfiguraci školicích úloh PyTorch, Upozorňujeme, že odhady byly zastaralé jako verze sady 1.19.0 SDK. S Azure ML SDK >= 1.15.0, ScriptRunConfig je doporučeným způsobem konfigurace školicích úloh, včetně těch, které používají architekturu pro hloubkové učení. Běžné otázky týkající se migrace najdete v [Průvodci migrací Estimator do ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Odeslat běh

[Objekt Run](/python/api/azureml-core/azureml.core.run%28class%29) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co se stane při spuštění
Po spuštění se spustí v následujících fázích:

- **Příprava**: image Docker se vytvoří podle definovaného prostředí. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu. Pokud je místo toho zadáno spravované prostředí, bude použit obrázek uložený v mezipaměti, který bude toto prostředí obsahovat.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a `script` spustí se. Výstupy z stdout a složky **./logs** se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka **./Outputs** se v běhu kopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

Místní kopii modelu můžete také stáhnout pomocí objektu run. Ve školicím skriptu `pytorch_train.py` objekt PyTorch Save uchovává model do místní složky (místní do výpočetního cíle). Kopii můžete stáhnout pomocí objektu spustit.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Distribuované trénování

Azure Machine Learning podporuje také distribuované úlohy PyTorch s více uzly, abyste mohli škálovat školicí úlohy. Můžete snadno spouštět distribuované úlohy PyTorch a Azure ML bude spravovat orchestraci za vás.

Azure ML podporuje spouštění distribuovaných úloh PyTorch s integrovaným modulem DistributedDataParallel Horovod i PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je open source, což snižuje rámec pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k psaní distribuovaného PyTorch kódu pro školení.

Školicí kód bude nutné instrumentovat s Horovod pro distribuované školení. Další informace o použití Horovod s PyTorch najdete v [dokumentaci k Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Navíc se ujistěte, že vaše školicí prostředí obsahuje balíček **horovod** . Pokud používáte PyTorché prostředí, horovod už je součástí jedné ze závislostí. Pokud používáte vlastní prostředí, ujistěte se, že je zahrnutá závislost horovod, například:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Aby bylo možné spustit distribuovanou úlohu pomocí MPI/Horovod v Azure ML, je nutné zadat [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) do `distributed_job_config` parametru konstruktoru ScriptRunConfig. Níže uvedený kód nakonfiguruje distribuovanou úlohu se dvěma uzly se spuštěným jedním procesem na jeden uzel. Pokud chcete spustit více procesů na jeden uzel (tj. Pokud má SKU clusteru více GPU), zadejte také `process_count_per_node` parametr v MpiConfiguration (výchozí nastavení je `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Úplný kurz pro spouštění distribuovaných PyTorch s Horovod v Azure ML najdete v tématu [Distributed PyTorch with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Pokud používáte vestavěný [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) modul PyTorch, který je vytvořen pomocí balíčku **Torch. Distributed** v kódu školení, můžete také spustit distribuovanou úlohu prostřednictvím Azure ml.

Pokud chcete spustit distribuovanou úlohu PyTorch v Azure ML, máte dvě možnosti:
1. Spuštění pro jednotlivé procesy: Určete celkový počet pracovních procesů, které chcete spustit, a Azure ML zpracuje každý proces.
2. Spuštění pro jednotlivé uzly pomocí `torch.distributed.launch` : zadejte `torch.distributed.launch` příkaz, který chcete spustit na každém uzlu. Nástroj pro spuštění Torch bude zpracovávat spuštěné pracovní procesy na jednotlivých uzlech.

Mezi možnostmi spuštění neexistují žádné zásadní rozdíly. je v podstatě až do preference uživatele nebo konvence rozhraní nebo knihoven postavených na Vanilla PyTorch (například blesk nebo Hugging obličej).

#### <a name="per-process-launch"></a>Spuštění pro jednotlivé procesy
Pokud chcete tuto možnost použít ke spuštění distribuované úlohy PyTorch, postupujte takto:
1. Zadat školicí skript a argumenty
2. Vytvořte [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) a zadejte a `process_count` také `node_count` . `process_count`Odpovídá celkovému počtu procesů, které chcete pro vaši úlohu spustit. Hodnota by se obvykle rovnala počtu GPU na uzel vynásobeným počtem uzlů. Pokud `process_count` parametr není zadán, bude služba Azure ml ve výchozím nastavení spouštět jeden proces na uzel.

V Azure ML se nastaví následující proměnné prostředí:
* `MASTER_ADDR` -IP adresa počítače, který bude hostitelem procesu s pořadím 0.
* `MASTER_PORT` – Bezplatný port na počítači, který bude hostitelem procesu s pořadím 0.
* `NODE_RANK` – Pořadí uzlu pro školení s více uzly. Možné hodnoty jsou 0 až (celkový počet uzlů-1).
* `WORLD_SIZE` – Celkový počet procesů. Hodnota by měla být stejná jako celková hodnota zařízení (GPU) použitá pro distribuované školení.
* `RANK` – Pořadí (globální) aktuálního procesu. Možné hodnoty jsou 0 – (World Size-1).
* `LOCAL_RANK` – Místní (relativní) pořadí procesu v rámci uzlu. Možné hodnoty jsou 0 – (počet procesů na uzlu-1).

Vzhledem k tomu, že požadované proměnné prostředí vám pro vás nastaví Azure ML, můžete k inicializaci skupiny procesů ve školicím kódu použít [výchozí inicializační proměnnou prostředí](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) .

Následující fragment kódu konfiguruje úlohu PyTorch se dvěma uzly na jednom uzlu:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Aby bylo možné použít tuto možnost pro školení zaměřené na více procesů, budete muset použít Azure ML Python SDK >= 1.22.0, jak `process_count` bylo zavedeno v 1.22.0.

> [!TIP]
> Pokud váš školicí skript předává informace, jako je místní pořadí nebo pořadí jako argumenty skriptu, můžete odkazovat na proměnné prostředí v argumentech: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Spuštění pro jednotlivé uzly s `torch.distributed.launch`
PyTorch poskytuje nástroj pro spuštění v [Torch. Distributed. Launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) , který mohou uživatelé použít ke spuštění více procesů na jeden uzel. `torch.distributed.launch`Modul v každém z uzlů vytvoří více školicích procesů.

Následující kroky demonstrují, jak nakonfigurovat úlohu PyTorch pomocí spouštěče uzlů na Azure ML, která bude mít za chodu následující příkaz:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Zadejte `torch.distributed.launch` příkaz do `command` parametru `ScriptRunConfig` konstruktoru. Azure ML spustí tento příkaz na všech uzlech školicího clusteru. `--nproc_per_node` hodnota by měla být menší nebo rovna počtu GPU dostupných na jednotlivých uzlech. `MASTER_ADDR`, `MASTER_PORT` a `NODE_RANK` jsou všechny nastavené pomocí Azure ml, takže můžete v příkazu odkazovat jenom na proměnné prostředí. Azure ML nastaví `MASTER_PORT` na 6105, ale pokud chcete, můžete předat jinou hodnotu `--master_port` argumentu `torch.distributed.launch` příkazu. (Spouštěcí nástroj obnoví proměnné prostředí.)
2. Vytvořte `PyTorchConfiguration` a zadejte `node_count` . Nemusíte nastavovat `process_count` , protože Azure ml se použije jako výchozí pro spuštění jednoho procesu na jeden uzel, který spustí zadaný příkaz pro spuštění.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Úplný kurz pro spouštění distribuovaných PyTorch v Azure ML najdete v tématu [Distributed PyTorch with DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel).

### <a name="troubleshooting"></a>Řešení potíží

* **Horovod byla vypnuta**: ve většině případů, pokud narazíte na "AbortedError: Horovod byl vypnut", existovala základní výjimka v jednom z procesů, které způsobily vypnutí Horovod. V Azure ML má každá vrstva v úloze MPI vlastní vyhrazený soubor protokolu. Tyto protokoly mají název `70_driver_logs`. V případě distribuovaného trénování se k názvům těchto protokolů přidává přípona `_rank`, aby bylo snadnější tyto protokoly odlišit. Pokud chcete najít přesnou chybu, která způsobila vypnutí Horovod, Projděte všechny soubory protokolů a hledejte na `Traceback` konci driver_log souborů. Jeden z těchto souborů vám poskytne vlastní podkladovou výjimku. 

## <a name="export-to-onnx"></a>Exportovat do ONNX

Chcete-li optimalizovat odvození s [modulem runtime ONNX](concept-onnx.md), převeďte vyškolený model PyTorch na formát ONNX. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. Příklad najdete v tomto [kurzu](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Další kroky

V tomto článku jste si naučili a zaregistrovali obsáhlý Learning, neuronové síť pomocí PyTorch na Azure Machine Learning. Pokud se chcete dozvědět, jak model nasadit, pokračujte na náš článek nasazení modelu.

* [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
