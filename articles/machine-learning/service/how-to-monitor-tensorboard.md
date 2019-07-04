---
title: Vizualizujte experimenty se službou Azure Machine Learning a TensorBoard
description: Spusťte TensorBoard k vizualizaci historie spuštění experimentu a identifikovat potenciální oblasti pro ladění a přetrénování hyperparameter.
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560856"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Vizualizujte dostupných spuštění experimentu a metriky ve službě Azure Machine Learning a TensorBoard

V tomto článku se dozvíte, jak k zobrazení dostupných spuštění experimentu a metriky pomocí TensorBoard [ `tensorboard` balíčku](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) v hlavní Azure Machine Learning service SDK. Po spuštění experimentu si jste ho zkontrolovat, můžete lépe optimalizovat a přeučování modelů strojového učení.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) je sada webových aplikací pro kontrolu a vysvětlení struktury experiment a výkonu.

Jak spustit TensorBoard s experimenty Azure Machine Learning závisí na typu testu:
+ Pokud experimentu nativně Vypíše soubory protokolů, které jsou použitelné podle TensorBoard, jako je například experimenty PyTorch, Chainer a TensorFlow, pak můžete [TensorBoard spouštět přímo](#direct) z experiment historie spuštění. 

+ Experimenty, které není výstupní nativně TensorBoard použitelné soubory, jako třeba Scikit informace nebo Azure Machine Learning, experimenty, použijte [ `export_to_tensorboard()` metoda](#export) exportovat jako TensorBoard protokoly historie spuštění a spuštění TensorBoard z něj. 

## <a name="prerequisites"></a>Požadavky

* Pokud chcete spustit TensorBoard a zobrazení historií spuštění experimentu, vaše experimenty muset dříve jste povolili protokolování můžete sledovat jeho metriky a výkonu.  

* Kód v tomto postupu můžete spustit v některém z následujících prostředích: 

    * Azure Machine Learning poznámkového bloku virtuálního počítače – žádné soubory ke stažení nebo instalace

        * Dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md#create-notebook) vytvořit poznámkový blok vyhrazený server už načtené pomocí sady SDK a ukázkové úložiště.

        * Ve složce samples na serveru Poznámkový blok, najít dvě dokončit a poznámkové bloky rozšířit tak, že přejdete do tohoto adresáře: **postupy-k-použití azureml > školení s obsáhlého learningu**.
        * Export-Run-history-to-Run-history.ipynb
        * tensorboard.ipynb

    * Váš vlastní server Juptyer poznámkového bloku
      * Použití [vytvořit pracovní prostor článek](setup-create-workspace.md) do
          * [Nainstalujte sadu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) s `tensorboard` navíc
          * Vytvoření pracovního prostoru a jeho konfigurační soubor (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Option 1: Přímo zobrazení historie spuštění v sadě TensorBoard

Tato možnost funguje pro experimenty, že nativně výstupy protokolu souborů použitelné TensorBoard, jako je například PyTorch, Chainer a TensorFlow experimenty. Pokud to není případ experimentu, použijte [ `export_to_tensorboard()` metoda](#export) místo.

Následující příklad kódu používá [mnist ručně ukázku experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z vaší TensorFlow úložiště v cílové vzdálené výpočetní prostředí, Azure Machine Learning Compute. V dalším kroku trénujeme náš model s vlastní sadou SDK [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), a pak spusťte TensorBoard pro tento experiment TensorFlow, to znamená, že experiment, který nativně Vypíše soubory TensorBoard událostí.

### <a name="set-experiment-name-and-create-project-folder"></a>Nastavte název experimentu a vytvořte složku projektu

Tady používáme název experimentu a vytvoření jeho složky. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Stáhněte si kód TensorFlow ukázku experimentu

Společnosti TensorFlow úložiště obsahuje ukázkový mnist ručně s rozsáhlou TensorBoard instrumentace. Jsme nepodporují ani zapotřebí změnit kód v této ukázce, aby fungovaly se službou Azure Machine Learning. V následujícím kódu jsme kód mnist ručně stáhněte a uložte ho ve složce náš nově vytvořený experiment.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
V celém souboru kódu mnist ručně mnist_with_summaries.py, Všimněte si, že existují řádky toto volání `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` atd. Tyto skupiny metod, log a značky klíčové metriky své experimenty do historie spuštění. `tf.summary.FileWriter()` Je obzvláště důležité, protože dojde k serializaci dat ze zaznamenané experiment metrik, která umožňuje TensorBoard můžete generovat vizualizace z nich.

 ### <a name="configure-experiment"></a>Konfigurace testu

V následujícím příkladu jsme naše experiment konfigurace a nastavení adresáře pro protokoly a data. Tyto protokoly se nahraje do služby artefaktu, která má přístup k TensorBoard později.

>[!Note]
> V tomto příkladu TensorFlow, musíte nainstalovat TensorFlow na místním počítači. Modul TensorBoard (to znamená, ten je součástí TensorFlow) dál, musí být přístupná pro jádra tento poznámkový blok, je v místním počítači, cokoli běží TensorBoard.

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
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Vytvoření clusteru pro experiment
Vytvoříme cluster AmlCompute pro tento experiment, ale možné vytvářet své experimenty v jakémkoli prostředí a budete stále moct TensorBoard spouštět proti experiment historie spuštění. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Odeslat spuštění s TensorFlow odhad

Odhad TensorFlow poskytuje jednoduchý způsob spouštění úlohy trénování TensorFlow na cílové výpočetní prostředí. Se implementuje pomocí obecného [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídu, která slouží k podpoře libovolné architektury. Další informace o školení modely s využitím obecných odhad, naleznete v tématu [trénování modelů Azure Machine Learning pomocí odhad](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Spuštění TensorBoard

Při spuštění nebo po jeho dokončení, můžete spustit TensorBoard. V následujícím příkladu vytvoříme instanci objektu TensorBoard `tb`, že trvá spuštění experimentu načetla v `run`a pak spustí TensorBoard s `start()` metody. 
  
[TensorBoard konstruktor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) přijímá pole spuštění, proto ujistěte se a předat ji jako pole s jedním prvkem.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Option 2: Export historie protokolu zobrazíte v sadě TensorBoard

Následující kód nastaví ukázkový experiment, zahájí proces přihlášení pomocí Azure Machine Learning API historie spuštění a exportuje experiment spustit historie do protokolů, které jsou použitelné TensorBoard pro vizualizaci. 

### <a name="set-up-experiment"></a>Nastavení testu

Následující kód nastaví nový experiment a názvy běhového adresáře `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Tady se nám načíst datovou sadu diabetem – integrované malou datovou sadu, která se dodává s scikit-informace a rozdělit na test a trénovací sady.

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

### <a name="run-experiment-and-log-metrics"></a>Spusťte experiment a protokolování metrik

Pro tento kód jsme trénování modelu lineární regrese a protokolu klíčové metriky, použije alfa `alpha` a rozumí kvadratická chyba `mse`, v historii spuštění.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Probíhá export TensorBoard

Pomocí sady SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metody jsme to taky historii spuštění tohoto naše experimentu služby Azure machine learning do protokolů TensorBoard, abychom mohli zobrazit přes TensorBoard.  

V následujícím kódu, můžeme vytvořit složku `logdir` v naše aktuální pracovní adresář. Tato složka je, kde vyexportujeme naše experiment historie spuštění a protokoly z `root_run` a poté označit, která spustí jako dokončené. 

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
 Můžete také exportovat konkrétního spuštění do TensorBoard tak, že zadáte název běhu  `export_to_tensorboard(run_name, logdir)`

Spouštění a zastavování TensorBoard jednou naší historii spuštění tohoto experimentu je exportovat, můžeme spustit TensorBoard s [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) metody. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Jakmile budete hotovi, nezapomeňte volat [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metodu objektu TensorBoard. V opačném případě bude pokračovat TensorBoard spustit až po vypnutí poznámkového bloku jádro. 

```python
tb.stop()
```

## <a name="next-steps"></a>Další postup

V tomto s postupy vám, vytvořili dva pokusy a zjistili, jak spustit TensorBoard proti jejich historie spuštění k odhalení oblastí pro potenciální ladění a přetrénování. 

* Pokud jste se svým modelem spokojeni, přejděte na naše [nasazení modelu](how-to-deploy-and-where.md) článku. 
* Další informace o [hyperparametrů](how-to-tune-hyperparameters.md). 
