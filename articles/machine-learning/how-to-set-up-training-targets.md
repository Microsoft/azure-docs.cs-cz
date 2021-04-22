---
title: Konfigurace spuštění školení
titleSuffix: Azure Machine Learning
description: Naučte svůj model strojového učení v různých školicích prostředích (cíle výpočtů). Mezi školicími prostředími můžete snadno přepínat.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: e044920953d0b635e7fe92f07b2cd69c0d8f5ee7
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888579"
---
# <a name="configure-and-submit-training-runs"></a>Konfigurace a odesílání trénovacích spuštění

V tomto článku se dozvíte, jak nakonfigurovat a odeslat Azure Machine Learning běhy pro výuku vašich modelů. Fragmenty kódu vysvětlují klíčové části Konfigurace a odeslání školicího skriptu.  Pak můžete použít jeden z [ukázkových poznámkových bloků](#notebooks) a najít kompletní pracovní příklady.

Po školení je běžné začít na místním počítači a později škálovat na cloudový cluster. Pomocí Azure Machine Learning můžete skript spustit na různých výpočetních cílech, aniž byste museli měnit školicí skript.

Vše, co potřebujete udělat, je definovat prostředí pro každý cíl výpočtů v rámci **Konfigurace spuštění skriptu**.  Pak, pokud chcete spustit experiment pro školení na jiném cílovém výpočetním prostředí, zadejte konfiguraci spuštění pro výpočetní výkon.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/install) (>= 1.13.0)
* [Azure Machine Learning pracovní prostor](how-to-manage-workspace.md),`ws`
* Cíl výpočtů, `my_compute_target` .  [Vytvořit cíl výpočtů](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Co je konfigurace spuštění skriptu?
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) se používá ke konfiguraci informací nezbytných pro odeslání školicích běhů v rámci experimentu.

Odešlete experiment školení s objektem ScriptRunConfig.  Tento objekt obsahuje:

* **source_directory**: zdrojový adresář, který obsahuje školicí skript.
* **skript**: školicí skript, který se má spustit
* **compute_target**: výpočetní cíl, na kterém se má spustit
* **prostředí**: prostředí, které se má použít při spuštění skriptu.
* a některé další konfigurovatelné možnosti (Další informace najdete v [referenční dokumentaci](/python/api/azureml-core/azureml.core.scriptrunconfig) )

## <a name="train-your-model"></a><a id="submit"></a>Trénování modelu

Vzor kódu pro odeslání školicího běhu je stejný pro všechny typy výpočetních cílů:

1. Vytvoření experimentu ke spuštění
1. Vytvořte prostředí, ve kterém se skript spustí.
1. Vytvoření ScriptRunConfig, které určuje cíl a prostředí výpočtů
1. Odeslat běh
1. Počkat na dokončení běhu

Nebo můžete:

* Odešlete HyperDrive spuštění pro [ladění pomocí parametrů](how-to-tune-hyperparameters.md).
* Odešlete experiment prostřednictvím [rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvořte experiment v pracovním prostoru.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Vybrat cíl výpočtů

Vyberte výpočetní cíl, na kterém se má školicí skript spustit. Pokud se v ScriptRunConfig nezadá žádný cíl výpočtů, nebo pokud `compute_target='local'` Azure ml provede místní spuštění skriptu. 

Vzorový kód v tomto článku předpokládá, že jste již vytvořili výpočetní cíl v `my_compute_target` části požadavky.

>[!Note]
>Azure Databricks se pro školení modelů nepodporují jako výpočetní cíl. Pro úlohy přípravy a nasazování dat můžete použít Azure Databricks. 

## <a name="create-an-environment"></a>Vytvoření prostředí
Azure Machine Learning [prostředí](concept-environments.md) jsou zapouzdření prostředí, ve kterém se vaše školení ve službě Machine Learning stane. Určují balíčky Pythonu, image Docker, proměnné prostředí a nastavení softwaru v rámci vašich školicích a vyhodnocovacích skriptů. Určují také moduly runtime (Python, Spark nebo Docker).

Můžete buď definovat vlastní prostředí, nebo použít známé prostředí Azure ML. Sestupná [prostředí](./how-to-use-environments.md#use-a-curated-environment) jsou předdefinovaná prostředí, která jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Tato prostředí se zálohují pomocí imagí Docker uložených v mezipaměti, což snižuje náklady na přípravu na spuštění. Úplný seznam dostupných prostředí, která jsou k dispozici, najdete v tématu [Azure Machine Learning uspořádaná prostředí](./resource-curated-environments.md) .

Pro vzdálený cíl výpočetní služby můžete použít některé z těchto oblíbených prostředí, která zahájí:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Další informace a podrobnosti o prostředích najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Místní cíl výpočtů

Pokud je váš cíl výpočetní služby vaším **místním počítačem**, zodpovídáte za zajištění dostupnosti všech potřebných balíčků v prostředí Pythonu, ve kterém se skript spouští.  Použijte `python.user_managed_dependencies` k použití aktuálního prostředí Python (nebo Pythonu na cestě, kterou zadáte).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Vytvoření konfigurace spuštění skriptu

Teď, když máte výpočetní cíl ( `my_compute_target` ) a prostředí ( `myenv` ), vytvořte konfiguraci spuštění skriptu, která spustí školicí skript (), který `train.py` se nachází ve vašem `project_folder` adresáři:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Pokud nezadáte prostředí, vytvoří se pro vás výchozí prostředí.

Pokud máte argumenty příkazového řádku, které chcete předat skriptu pro školení, můžete je zadat prostřednictvím **`arguments`** parametru konstruktoru ScriptRunConfig, např. `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Pokud chcete přepsat výchozí maximální dobu povolenou pro spuštění, můžete to provést prostřednictvím **`max_run_duration_seconds`** parametru. Systém se pokusí spuštění automaticky zrušit, pokud trvá déle než tato hodnota.

### <a name="specify-a-distributed-job-configuration"></a>Zadat konfiguraci distribuované úlohy
Pokud chcete spustit úlohu distribuovaného školení, zadejte pro parametr konfiguraci specifickou pro distribuovanou úlohu **`distributed_job_config`** . Podporované typy konfigurace zahrnují [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)a [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration). 

Další informace a příklady spouštění distribuovaných úloh Horovod, TensorFlow a PyTorch najdete v těchto tématech:

* [Trénování modelů TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Trénování modelů PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Odeslání experimentu

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Po odeslání školicího cvičení se vytvoří snímek adresáře, který obsahuje vaše školicí skripty, a pošle se do cíle služby Compute. Je také uložen jako součást experimentu v pracovním prostoru. Pokud změníte soubory a znovu odešlete spuštění, nahrají se jenom změněné soubory.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Další informace o snímcích najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Speciální složky** Dvě složky, *výstupy* a *protokoly* dostanou zvláštní zacházení Azure Machine Learning. Když při výuce zapisujete soubory do složek s názvem *výstupy* a *protokoly* , které jsou relativní vzhledem ke kořenovému adresáři ( `./outputs` a v `./logs` uvedeném pořadí), soubory se automaticky nahrají do historie spuštění, abyste k nim měli přístup, až se vaše spuštění dokončí.
>
> Chcete-li během školení vytvořit artefakty (například soubory modelů, kontrolní body, datové soubory nebo vykreslené bitové kopie), zapište je do `./outputs` složky.
>
> Podobně můžete do složky napsat jakékoli protokoly z školicích běhů `./logs` . Aby bylo možné využívat Azure Machine Learning [integrace TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) , nezapomeňte zapisovat do této složky protokoly TensorBoard. Během probíhajícího běhu budete moct spouštět TensorBoard a streamovat tyto protokoly.  Později také budete moci obnovit protokoly z libovolného z předchozích spuštění.
>
> Například pro stažení souboru zapsaného do složky *výstupy* do místního počítače po spuštění vzdáleného školení: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Příklady poznámkových bloků

Příklady konfigurace spuštění různých školicích scénářů najdete v těchto poznámkových blocích:
* [Školení pro různé cíle výpočtů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Školení s použitím architektury ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [kurzy/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Řešení potíží

* **Spuštění selhalo `jwt.exceptions.DecodeError` s**: přesná chybová zpráva: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Zvažte upgrade na nejnovější verzi nástroje AzureML-Core: `pip install -U azureml-core` .
    
    Pokud k tomuto problému dochází pro místní spuštění, podívejte se na verzi PyJWT nainstalovanou ve vašem prostředí, kde spouštíte spouštění. Podporované verze PyJWT jsou < 2.0.0. Pokud je verze >= 2.0.0, odinstalujte PyJWT z prostředí. Můžete si ověřit verzi PyJWT, odinstalovat a nainstalovat správnou verzi následujícím způsobem:
    1. Spusťte příkazové prostředí, aktivujte prostředí Conda, ve kterém je nainstalovaná operace AzureML-Core.
    2. Zadejte `pip freeze` a vyhledejte `PyJWT` , pokud se nachází, uvedená verze by měla být < 2.0.0
    3. Pokud uvedená verze není podporovanou verzí, `pip uninstall PyJWT` v příkazovém prostředí a zadejte y pro potvrzení.
    4. Dá se nainstalovat pomocí `pip install 'PyJWT<2.0.0'`.
    
    Pokud odesíláte uživatelem vytvořené prostředí s vaším spuštěním, zvažte použití nejnovější verze programu AzureML-Core v tomto prostředí. Verze >= 1.18.0 z AzureML-Core už připnutí PyJWT < 2.0.0. Pokud v prostředí, které odesíláte, potřebujete použít verzi < AzureML-Core 1.18.0, nezapomeňte ve svých závislostech PIP zadat < PyJWT 2.0.0.


 * **ModuleErrors (žádný modul s názvem)**: Pokud při odesílání experimentů v Azure ml pracujete do ModuleErrors, předpokládá se, že skript pro školení očekává instalaci balíčku, ale nepřidá se. Po zadání názvu balíčku nainstaluje Azure ML balíček do prostředí používaného pro váš školicí běh.

    Pokud používáte odhady k odesílání experimentů, můžete zadat název balíčku prostřednictvím `pip_packages` nebo `conda_packages` parametr v Estimator na základě toho, ze kterého zdroje chcete balíček nainstalovat. Můžete také zadat soubor YML se všemi vašimi závislostmi pomocí `conda_dependencies_file` nebo vypsat všechny požadavky PIP v souboru txt pomocí `pip_requirements_file` parametru. Pokud máte vlastní objekt prostředí Azure ML, který chcete přepsat výchozí image, kterou používá Estimator, můžete toto prostředí zadat prostřednictvím `environment` parametru konstruktoru Estimator.
    
    Azure ML zachovává image Docker a jejich obsah se může zobrazit v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers).
    Závislosti specifické pro rozhraní jsou uvedené v dokumentaci k příslušnému rozhraní:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [Skriptu sklearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Pokud si myslíte, že konkrétní balíček je dostatečně společný, aby ho bylo možné přidat do spravovaných imagí a prostředí Azure ML, vyřešte v [kontejnerech AzureML](https://github.com/Azure/AzureML-Containers)problém GitHubu. 
 
* **NameError (název není definován), AttributeError (objekt nemá žádný atribut)**: Tato výjimka by měla pocházet z vašich školicích skriptů. Můžete si prohlédnout soubory protokolu z Azure Portal a získat další informace o konkrétním názvu, který není definován nebo chyba atributu. V sadě SDK se můžete podívat na `run.get_details()` chybovou zprávu. Zobrazí se také seznam všech souborů protokolu generovaných pro váš běh. Ujistěte se prosím, že se podíváte na školicí skript a opravte chybu před opětovným odesláním běhu. 


* **Spuštění nebo experimentování při odstraňování**: experimenty se dají archivovat pomocí metody [experiment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) nebo na kartě experiment v Azure Machine Learning klientovi studia pomocí tlačítka "archivní experiment". Tato akce skryje experiment ze seznamu dotazy a zobrazení, ale neodstraní ho.

    Trvalé odstranění jednotlivých experimentů nebo spuštění se v současné době nepodporuje. Další informace o odstraňování prostředků pracovního prostoru najdete v tématu [Export nebo odstranění dat pracovního prostoru služby Machine Learning](how-to-export-delete-data.md).

* **Dokument metriky je příliš velký**: Azure Machine Learning má interní omezení velikosti objektů metriky, které je možné v jednom z školicích běhů přihlásit. Pokud při protokolování metriky hodnot seznamu dojde k chybě Dokument metriky je příliš velký, zkuste seznam rozdělit na menší části, například:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Azure ML interně zřetězí bloky se stejným názvem metriky do souvislého seznamu.

* **Spuštění cíle služby COMPUTE trvá dlouhou dobu**: image Docker pro cíle výpočtů se načítají z Azure Container Registry (ACR). Ve výchozím nastavení Azure Machine Learning vytvoří ACR, který používá *základní* úroveň služby. Změna ACR pro váš pracovní prostor na úroveň Standard nebo Premium může zkrátit dobu potřebnou k sestavování a načítání imagí. Další informace najdete v tématu [Azure Container Registry úrovně služeb](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Další kroky

* [Kurz: výuka modelu](tutorial-train-models-with-aml.md) používá ke školení modelu spravovaný výpočetní cíl.
* Podívejte se, jak naučit modely s konkrétními rozhraními ML, jako jsou [Scikit-učení](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)a [PyTorch](how-to-train-pytorch.md).
* Naučte se [efektivně ladit parametry](how-to-tune-hyperparameters.md) pro vytváření lepších modelů.
* Jakmile budete mít školený model, zjistěte, [jak a kde nasadit modely](how-to-deploy-and-where.md).
* Zobrazit odkaz na sadu SDK [třídy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)
* [Použití Azure Machine Learning s Azure Virtual Networks](./how-to-network-security-overview.md)
