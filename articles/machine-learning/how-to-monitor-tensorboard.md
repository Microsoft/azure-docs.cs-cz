---
title: Vizualizace experimentů s využitím TensorBoardu
titleSuffix: Azure Machine Learning
description: Spusťte TensorBoard k vizualizaci historie spouštění experimentů a Identifikujte potenciální oblasti pro ladění a rekurzi parametrů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 362b44cbed07359635f542e4eb18fc33803f0390
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89649319"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Vizualizace běhů experimentů a metrik pomocí TensorBoard a Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se naučíte, jak zobrazit spuštění experimentů a metriky v TensorBoard pomocí [ `tensorboard` balíčku](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py&preserve-view=true) v hlavní sadě SDK pro Azure Machine Learning. Po kontrole spuštění experimentů můžete lépe vyladit a přeškolovat modely strojového učení.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) je sada webových aplikací pro kontrolu a porozumění vaší struktuře experimentů a výkonu.

Způsob spuštění TensorBoard s Azure Machine Learning experimenty závisí na typu experimentu:
+ Pokud váš experiment nativně Vypisuje soubory protokolů, které jsou spotřební TensorBoard, jako je PyTorch, chainer a TensorFlow experimenty, můžete [Spustit TensorBoard přímo](#direct) z historie spuštění experimentu. 

+ V případě experimentů, které nemají nativně výstupní TensorBoardelné soubory, jako např. například Scikit-učí nebo Azure Machine Learning experimenty, použijte [ `export_to_tensorboard()` metodu](#export) pro export historie spuštění jako protokolů TensorBoard a z ní spusťte TensorBoard. 

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learningu, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

* Chcete-li spustit TensorBoard a zobrazit historie spuštění experimentů, vaše experimenty musí mít dříve povolené protokolování, aby bylo možné sledovat jeho metriky a výkon.  

* Kód v tomto dokumentu může běžet v jednom z následujících prostředí: 

    * Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

        * Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.

        * Ve složce Samples na serveru poznámkového bloku najděte dva dokončené a rozšířené poznámkové bloky tak, že přejdete na tyto adresáře:
            * **How-to-> Training – s hloubkovým učením > export-Run-History to-tensorboard > export-Run-History-to-tensorboard. ipynb**

            * **Postup použití-AzureML > sledování a monitorování – experimenty > tensorboard. ipynb**

    * Váš vlastní server Juptyer notebook
       * [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) s `tensorboard` dalšími
        * [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).  
        * [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Možnost 1: přímo zobrazit historii spuštění v TensorBoard

Tato možnost je vhodná pro experimenty, které nativně výstupují soubory protokolu TensorBoard, jako je PyTorch, chainer a TensorFlow experimenty. Pokud to není případ vašeho experimentu, použijte místo toho [ `export_to_tensorboard()` metodu](#export) .

Následující příklad kódu používá vzorový [experiment mnist ručně zapsaných](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z úložiště TensorFlow ve vzdáleném cíli služby compute, Azure Machine Learning Compute. V dalším kroku provedeme náš model pomocí vlastního [TensorFlow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)sady SDK a potom zahájíte TensorBoard proti tomuto TensorFlow experimentu, tedy experiment, který nativně produkuje soubory událostí TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Nastavit název experimentu a vytvořit složku projektu

Tady pojmenujte experiment a vytvoříte jeho složku. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Stáhnout ukázkový kód experimentu TensorFlow

Úložiště TensorFlow má ukázku MNIST ručně zapsaných s rozsáhlou instrumentací TensorBoard. Pro práci s Azure Machine Learning nepotřebujeme ani změnit žádný z těchto ukázkových kódu. V následujícím kódu stáhneme kód MNIST ručně zapsaných a uložíme ho do naší nově vytvořené složky experimentů.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
V celém souboru kódu mnist ručně zapsaných mnist_with_summaries. py, Všimněte si, že existují řádky, které `tf.summary.scalar()` volají  `tf.summary.histogram()` , `tf.summary.FileWriter()` atd. Tyto metody seskupují, protokolují a klíčová metriky značek svých experimentů do historie spuštění. `tf.summary.FileWriter()`Je obzvláště důležité při serializaci dat z metriky protokolovaných experimentů, které umožňují TensorBoard vygenerovat vizualizace z nich.

 ### <a name="configure-experiment"></a>Konfigurovat experiment

V následujícím příkladu nakonfigurujeme náš experiment a nastavíme adresáře pro protokoly a data. Tyto protokoly se nahrají do služby artefaktu, která TensorBoard přistupuje později.

>[!Note]
> V tomto příkladu TensorFlow budete muset nainstalovat TensorFlow na svůj místní počítač. Kromě toho musí být modul TensorBoard (tj. ten, který je součástí TensorFlow) dostupný pro jádro tohoto poznámkového bloku, protože místní počítač je to, co spouští TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
script_params = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Vytvoření clusteru pro experiment
Pro tento experiment vytvoříme cluster AmlCompute, ale vaše experimenty se dají vytvořit v jakémkoli prostředí a pořád můžete spustit TensorBoard proti historii spuštění experimentu. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="submit-run-with-tensorflow-estimator"></a>Odeslat běh s TensorFlow Estimator

TensorFlow Estimator poskytuje jednoduchý způsob, jak spustit školicí úlohu TensorFlow na cílovém výpočetním cíli. Je implementována prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) třídy, kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

```Python
from azureml.train.dnn import TensorFlow

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Spustit TensorBoard

TensorBoard můžete spustit během běhu nebo po jeho dokončení. V následujícím postupu vytvoříme instanci objektu TensorBoard, `tb` která převezme historii spuštění experimentu, která byla načtena v `run` nástroji, a potom spustí TensorBoard s `start()` metodou. 
  
[Konstruktor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true) přebírá pole spuštění, proto se ujistěte, že je a předáte do jako pole s jedním prvkem.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 I když se tento příklad používá TensorFlow, TensorBoard se dá snadno použít s PyTorch nebo modely zřetězení. TensorFlow musí být dostupná na počítači, na kterém běží TensorBoard, ale není potřeba na počítači, který provádí PyTorch nebo výpočetní modul. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Možnost 2: Export historie jako protokolu pro zobrazení v TensorBoard

Následující kód nastaví vzorový experiment, zahájí proces protokolování pomocí rozhraní API historie spuštění Azure Machine Learning a exportuje historii spuštění experimentu do protokolů, které jsou použitelné TensorBoard pro vizualizaci. 

### <a name="set-up-experiment"></a>Nastavení experimentu

Následující kód nastaví nový experiment a pojmenuje adresář Run `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Tady načteme datovou sadu diabetes – integrovanou malou datovou sadu, která je dodávána s scikit-učí a rozdělená do testovacích a školicích sad.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Spouštění metrik experimentů a protokolů

Pro tento kód vytvoříme v historii spuštění lineární regresní model a metriky klíčů protokolu, koeficient alfa, `alpha` a střední hodnotu chyby `mse` .

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportovat běhy do TensorBoard

Pomocí metody [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py&preserve-view=true) sady SDK můžeme Exportovat historii spuštění našeho experimentu Azure Machine Learning do protokolů tensorboard, takže je můžeme zobrazit přes tensorboard.  

V následujícím kódu vytvoříme složku `logdir` v aktuálním pracovním adresáři. Tato složka je místo, kde budeme exportovat naši historii a protokoly spuštění experimentů z `root_run` a potom tento běh označit jako dokončený. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Můžete také exportovat konkrétní běh do TensorBoard zadáním názvu spuštění.  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Spuštění a zastavení TensorBoard
Jakmile je naše historie spuštění pro tento experiment exportována, můžeme spustit TensorBoard pomocí metody [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#&preserve-view=truestart-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Až budete hotovi, ujistěte se, že zavoláte metodu [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#&preserve-view=truestop--) objektu TensorBoard. V opačném případě bude TensorBoard běžet dál, dokud nevypnete jádro poznámkového bloku. 

```python
tb.stop()
```

## <a name="next-steps"></a>Další kroky

V tomto postupu jste vytvořili dvě experimenty a zjistili jste, jak spustit TensorBoard na svých historiích spuštění, abyste identifikovali oblasti pro možné vyladění a přeškolení. 

* Pokud jste s modelem spokojeni, přejděte na článek [nasazení modelu](how-to-deploy-and-where.md) . 
* Přečtěte si další informace o [ladění parametrů](how-to-tune-hyperparameters.md). 
