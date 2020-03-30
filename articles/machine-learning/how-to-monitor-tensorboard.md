---
title: Vizualizace experimentů s využitím TensorBoardu
titleSuffix: Azure Machine Learning
description: Spusťte TensorBoard pro vizualizaci historie spuštění experimentu a identifikaci potenciálních oblastí pro hyperparametrické ladění a rekvalifikaci.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195375"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Vizualizace experimentů a metrik s TensorBoardem a Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak zobrazit spuštění experimentu a metriky v TensorBoard pomocí [ `tensorboard` balíčku](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) v hlavní Azure Machine Learning SDK. Po kontrole spuštění experimentu můžete lépe naladit a přeškolit modely strojového učení.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) je sada webových aplikací pro kontrolu a pochopení struktury experimentu a výkonu.

Způsob spuštění tensorboardu pomocí experimentů Azure Machine Learning závisí na typu experimentu:
+ Pokud váš experiment nativně vyhotovuje soubory protokolu, které jsou spotřební pomocí TensorBoard, jako jsou experimenty PyTorch, Chainer a TensorFlow, můžete [spustit TensorBoard přímo](#direct) z historie spuštění experimentu. 

+ U experimentů, které nativně nevyprovočitelně nevypracovávají spotřební soubory TensorBoard, jako je například Scikit-learn nebo Experimenty Azure Machine Learning, použijte [metodu `export_to_tensorboard()` ](#export) k exportu historie spuštění jako protokoly TensorBoard a odtud spusťte TensorBoard. 

> [!TIP]
> Informace v tomto dokumentu jsou určeny především pro datové vědce a vývojáře, kteří chtějí sledovat proces školení modelu. Pokud jste správce zájem o monitorování využití prostředků a události z Azure Machine learning, jako jsou kvóty, dokončené školení běží nebo dokončena nasazení modelu, najdete [v tématu monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

* Chcete-li spustit TensorBoard a zobrazit historii spuštění experimentu, musí vaše experimenty dříve povolit protokolování, aby mohly sledovat jeho metriky a výkon.  

* Kód v tomto dokumentu lze spustit v některém z následujících prostředí: 

    * Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

        * Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.

        * Ve složce ukázky na serveru poznámkového bloku vyhledejte dva dokončené a rozbalené poznámkové bloky přejděte do těchto adresářů:
            * **how-to-use-azureml > školení s hlubokým učením > export-run-historie-k-tendenboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experimenty > tensorboard.ipynb**

    * Váš vlastní notebook ový server Juptyer
       * [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) s `tensorboard` extra
        * [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).  
        * [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Možnost 1: Přímé zobrazení historie běhu v TensorBoardu

Tato možnost funguje u experimentů, které nativně výstupy soubory protokolu spotřební tensorboard, jako je například PyTorch, Chainer a TensorFlow experimenty. Pokud tomu tak není v případě experimentu, použijte [metodu `export_to_tensorboard()` ](#export) místo.

Následující ukázkový kód používá [demo experiment MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z úložiště TensorFlow ve vzdáleném výpočetním cíli Azure Machine Learning Compute. Dále trénujeme náš model s vlastním [odhadem Tententrového toku](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)SDK a pak spustíme TensorBoard proti tomuto experimentu TensorFlow, tedy experimentu, který nativně vyhotovuje soubory událostí TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Nastavení názvu experimentu a vytvoření složky projektu

Zde pojmenujeme experiment a vytvoříme jeho složku. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Stáhnout kód demo experimentu TensorFlow

Úložiště TensorFlow má demo MNIST s rozsáhlou instrumentací TensorBoard. Nechceme, ani není nutné měnit žádný kód této ukázky pro práci s Azure Machine Learning. V následujícím kódu stáhneme kód MNIST a uložíme jej do naší nově vytvořené složky experimentu.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
V celém souboru kódu MNIST, mnist_with_summaries.py, `tf.summary.scalar()`všimněte si, že existují řádky, které volají , `tf.summary.histogram()`atd. `tf.summary.FileWriter()` Tyto metody seskupují, protokolují a označují klíčové metriky experimentů do historie spuštění. Je `tf.summary.FileWriter()` obzvláště důležité, protože serializuje data z metrik y protokolovaného experimentu, což umožňuje TensorBoard generovat vizualizace z nich.

 ### <a name="configure-experiment"></a>Konfigurace experimentu

V následujícím textu nakonfigurujeme náš experiment a nastavíme adresáře pro protokoly a data. Tyto protokoly budou odeslány do služby artefaktů, ke které tensorboard přistupuje později.

>[!Note]
> Pro tento příklad TensorFlow budete muset nainstalovat TensorFlow na místním počítači. Dále musí být modul TensorBoard (to znamená ten, který je součástí TensorFlow) přístupný jádru tohoto notebooku, protože místní počítač je to, co běží TensorBoard.

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
Pro tento experiment vytvoříme cluster AmlCompute, ale vaše experimenty mohou být vytvořeny v libovolném prostředí a stále můžete spustit TensorBoard proti historii spuštění experimentu. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Odeslat spuštění s odhadem TensorFlow

Odhad TensorFlow poskytuje jednoduchý způsob spuštění trénovací úlohy TensorFlow na výpočetní cíl. Je implementována prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy, která lze použít pro podporu jakékoli rozhraní. Další informace o trénovacích modelech pomocí obecného odhadu najdete v tématu [trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md)

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

TensorBoard můžete spustit během běhu nebo po jeho dokončení. V následujícím textu vytvoříme instanci `tb`objektu TensorBoard , která `run`přebírá historii spuštění experimentu `start()` načtenou v , a poté spustí TensorBoard s metodou. 
  
[TentenorBoard konstruktor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) trvá pole běží, takže se ujistěte, a předat jako jednoprvkové pole.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Zatímco tento příklad používá TensorFlow, TensorBoard lze použít stejně snadno s modely PyTorch nebo Chainer. TensorFlow musí být k dispozici na stroji se systémem TensorBoard, ale není nutné na stroji, který provádí výpočty PyTorch nebo Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Možnost 2: Export historie jako log pro zobrazení v TensorBoard

Následující kód nastaví ukázkový experiment, zahájí proces protokolování pomocí souborů API historie spuštění Azure Machine Learning a exportuje historii spuštění experimentu do protokolů, které tensorboard potřebuje pro vizualizaci. 

### <a name="set-up-experiment"></a>Nastavit experiment

Následující kód nastaví nový experiment a `root_run`pojmenuje spustit adresář . 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Zde načteme datovou sadu diabetu- vestavěnou malou datovou sadu, která je dodávána s scikit-learn, a rozdělíme ji na testovací a tréninkové sady.

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

### <a name="run-experiment-and-log-metrics"></a>Spuštění metrik experimentu a protokolu

Pro tento kód trénujeme lineární regresní model a klíčové metriky protokolu, `alpha`alfa `mse`koeficient a střední kvadratózní chybu , v historii běhu.

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

### <a name="export-runs-to-tensorboard"></a>Export běží na TensorBoard

Pomocí [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) metody sady SDK můžeme exportovat historii spuštění našeho experimentu azure strojového učení do protokolů TensorBoard, abychom je mohli zobrazit přes TensorBoard.  

V následujícím kódu vytvoříme `logdir` složku v našem aktuálním pracovním adresáři. Tato složka je místo, kde budeme exportovat `root_run` náš experiment spustit historii a protokoly z a pak označit, že spustit jako dokončené. 

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
 Můžete také exportovat konkrétní spuštění na TensorBoard zadáním názvu spustit`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Spuštění a zastavení TensorBoardu
Jakmile je naše historie běhu tohoto experimentu exportována, můžeme spustit TensorBoard metodou [start().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Až budete hotovi, ujistěte se, že volat [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) metoda TensorBoard objektu. V opačném případě bude TensorBoard pokračovat v běhu, dokud nevypnete jádro notebooku. 

```python
tb.stop()
```

## <a name="next-steps"></a>Další kroky

V tomto how-to vás, vytvořil dva experimenty a naučil se, jak spustit TensorBoard proti jejich spuštění historie k identifikaci oblastí pro potenciální ladění a rekvalifikaci. 

* Pokud jste spokojeni s modelem, přejdete na náš článek o [nasazení modelu.](how-to-deploy-and-where.md) 
* Další informace o [hyperparametrické optimalizaci](how-to-tune-hyperparameters.md). 
