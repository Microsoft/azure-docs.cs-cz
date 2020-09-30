---
title: 'Kurz pro klasifikaci imagí: modely vlaků'
titleSuffix: Azure Machine Learning
description: Pomocí Azure Machine Learning můžete v poznámkovém bloku Python Jupyter vytvořit výuku k modelu klasifikace obrázků s scikit. Tento kurz je první částí ze dvou.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 40ee7ad74d1a1daaf6df5e76b5e51db52feea304
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535065"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Kurz: analýza modelů klasifikace obrázků pomocí MNIST ručně zapsaných dat a scikit – učení 


V tomto kurzu se naučíte model strojového učení ve vzdálených výpočetních prostředcích. Pracovní postup školení a nasazení budete používat pro Azure Machine Learning v Jupyter poznámkovém bloku Pythonu.  Poznámkový blok poté můžete použít jako šablonu k trénování vlastního modelu strojového učení s vlastními daty. Tento kurz je **první částí z dvoudílné série kurzů**.  

V tomto kurzu se učí jednoduchá Logistická regrese s využitím datové sady [mnist ručně zapsaných](http://yann.lecun.com/exdb/mnist/) a [scikit-učení](https://scikit-learn.org) s Azure Machine Learning. MNIST je oblíbená datová sada obsahující 70 000 obrázků ve stupních šedi. Každý obrázek je ručně psaná číslice o 28 × 28 pixelech, která představuje číslo od 0 do 9. Cílem je vytvoření klasifikátoru s více třídami pro identifikaci číslice, kterou představuje daný obrázek.

Přečtěte si, jak provést následující akce:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Přístup k datům a jejich kontrola
> * Výukové jednoduché modely logistické regrese na vzdáleném clusteru.
> * Zkontrolujte výsledky školení a zaregistrujte nejlepší model.

Naučíte se, jak vybrat model a nasadit ho v [části 2 tohoto kurzu](tutorial-deploy-models-with-aml.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

>[!NOTE]
> Kód v tomto článku byl testován pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 1.13.0 verze.

## <a name="prerequisites"></a>Předpoklady

* Dokončete [kurz: Začínáme s vytvořením prvního experimentu Azure ml](tutorial-1st-experiment-sdk-setup.md) :
    * Vytvoření pracovního prostoru
    * Naklonujte Poznámkový blok kurzů do složky v pracovním prostoru.
    * Vytvořte cloudovou instanci Compute.

* Ve složce s naklonovánými *kurzy/imagí-mnist ručně zapsaných-data* otevřete Poznámkový blok *img-Classification-part1-Training. ipynb* . 


Kurz a doprovodný soubor **utils.py** je také k dispozici na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , pokud ho chcete použít ve svém vlastním [místním prostředí](how-to-configure-environment.md#local). Spusťte `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` pro instalaci závislostí pro tento kurz.

> [!Important]
> Zbývající část tohoto článku obsahuje stejný obsah, jaký vidíte v poznámkovém bloku.  
>
> Pokud chcete při spuštění kódu číst společně, přepněte do poznámkového bloku Jupyter. 
> Pokud chcete na poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **SHIFT + ENTER**. Případně spusťte celý Poznámkový blok výběrem možnosti **Spustit vše** na horním panelu nástrojů.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Nastavení vývojového prostředí

Veškeré nastavení pro vaši vývojovou práci se dá provést v poznámkovém bloku Pythonu. Instalační program zahrnuje následující akce:

* Importujte balíčky Pythonu.
* Připojte se k pracovnímu prostoru, aby váš místní počítač mohl komunikovat se vzdálenými prostředky.
* Vytvořte experiment pro sledování všech vašich spuštění.
* Vytvořte vzdálený výpočetní cíl, který se bude používat pro školení.

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které potřebujete v této relaci. Zobrazit také Azure Machine Learning verzi sady SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Připojení k pracovnímu prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. `Workspace.from_config()` přečte soubor **config.jsv** a načte podrobnosti do objektu s názvem `ws` :

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvořte experiment pro sledování spuštění ve vašem pracovním prostoru. Pracovní prostor může mít více experimentů:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Vytvořit nebo připojit existující cíl služby COMPUTE

Pomocí Azure Machine Learning výpočetní služby, spravované služby, mohou odborníci na data poučení modely strojového učení v clusterech virtuálních počítačů Azure. Mezi příklady patří virtuální počítače s podporou GPU. V tomto kurzu vytvoříte Azure Machine Learning COMPUTE jako školicí prostředí. Později v tomto kurzu odešlete kód Pythonu, který se bude spouštět na tomto virtuálním počítači. 

Následující kód vytvoří výpočetní clustery za vás, pokud už ve vašem pracovním prostoru neexistují. Nastaví cluster, který se bude škálovat dolů na 0, pokud se nepoužívá, a může škálovat až na 4 uzly. 

 **Vytvoření cíle výpočtů trvá asi pět minut.** Pokud je výpočetní prostředek již v pracovním prostoru, kód ho použije a přeskočí proces vytváření.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Nyní máte k dispozici potřebné balíčky a výpočetní prostředky pro trénink modelu v cloudu. 

## <a name="explore-data"></a>Zkoumání dat

Než začnete pracovat s modelem, potřebujete pochopit data, která používáte k jeho učení. V této části získáte informace o následujících postupech:

* Stáhněte si datovou sadu MNIST ručně zapsaných.
* Zobrazí některé ukázkové obrázky.

### <a name="download-the-mnist-dataset"></a>Stáhnutí datové sady MNIST

K získání nezpracovaných datových souborů MNIST ručně zapsaných použijte Azure Open DataSets. [Otevřené datové sady v Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Každá datová sada má odpovídající třídu, `MNIST` v tomto případě pro načtení dat různými způsoby.

Tento kód načte data jako `FileDataset` objekt, který je podtřídou třídy `Dataset` . `FileDataset`Odkazuje na jeden nebo více souborů libovolného formátu v úložišti dat nebo veřejných adresách URL. Třída poskytuje možnost stahovat nebo připojovat soubory do výpočetních prostředků tím, že vytvoří odkaz na umístění zdroje dat. Navíc zaregistrujete datovou sadu do svého pracovního prostoru pro snadné načtení během školení.

Pokud chcete získat další informace o datových sadách a jejich využití v sadě [SDK, postupujte](how-to-create-register-datasets.md) podle pokynů.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Zobrazení některých ukázkových obrázků

Načtěte komprimované soubory do pole `numpy`. Pak pomocí `matplotlib` vykreslete 30 náhodných obrázků z datové sady s jejich popisky nad nimi. Tento krok vyžaduje `load_data` funkci, která je součástí `utils.py` souboru. Tento soubor je umístěný ve složce s ukázkou. Ujistěte se, že je umístěn ve stejné složce jako tento poznámkový blok. `load_data`Funkce jednoduše analyzuje komprimované soubory do polí numpy.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Náhodná ukázka obrázků:

![Náhodný vzorek imagí](./media/tutorial-train-models-with-aml/digits.png)

Nyní máte představu o tom, jak tyto obrázky vypadají, a o očekávaném výstupu predikce.

## <a name="train-on-a-remote-cluster"></a>Trénování na vzdáleném clusteru

Pro tuto úlohu odešlete úlohu, která se má spustit v clusteru vzdáleného školení, který jste nastavili dříve.  K odeslání úlohy je potřeba provést:
* Vytvoření adresáře
* Vytvoření trénovacího skriptu
* Vytvoření konfigurace spuštění skriptu
* Odeslání úlohy

### <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte adresář, ze kterého bude dodán potřebný kód z počítače do vzdáleného prostředku.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Vytvoření trénovacího skriptu

Pokud chcete odeslat úlohu do clusteru, vytvořte nejprve trénovací skript. Spuštěním následujícího kódu vytvořte trénovací skript s názvem `train.py` v adresáři, který jste právě vytvořili.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Všimněte si, jak skript získává data a ukládá modely:

+ Školicí skript čte argument pro vyhledání adresáře, který obsahuje data. Když později odešlete úlohu, bude odkázána na úložiště dat pro tento argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Školicí skript uloží model do adresáře s názvem **výstupy**. Vše, co je v tomto adresáři zapsáno, se automaticky nahraje do vašeho pracovního prostoru. K vašemu modelu přistupujete z tohoto adresáře později v tomto kurzu. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Školicí skript vyžaduje, aby soubor `utils.py` správně načetl datovou sadu. Následující kód zkopíruje `utils.py` do `script_folder` , aby k souboru bylo možné přihlédnout společně se školicím skriptem na vzdáleném prostředku.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Konfigurace školicí úlohy

Vytvořte objekt [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) a zadejte podrobnosti o konfiguraci školicí úlohy, včetně vašeho školicího skriptu, prostředí, které se má použít, a výpočetní cíl, který se má spustit. Nakonfigurujte ScriptRunConfig zadáním:

* Adresář, který obsahuje vaše skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Cílové výpočetní prostředí. V tomto případě používáte cluster pro výpočty služby Azure Machine Learning, který jste vytvořili.
* Název školicího skriptu, **Train.py**.
* Prostředí obsahující knihovny potřebné ke spuštění skriptu.
* Argumenty požadované ze školicího skriptu.

V tomto kurzu je tento cíl AmlCompute. Všechny soubory ve složce skriptu se nahrají do uzlů clusteru pro spuštění. **Data_folder--** je nastaveno na použití datové sady.

Nejprve vytvořte prostředí, které obsahuje: knihovna scikit-učení, AzureML-DataSet-runtime požadovaná pro přístup k datové sadě a AzureML – výchozí, které obsahují závislosti pro metriky protokolování. Služba AzureML-výchozí také obsahuje závislosti potřebné pro nasazení modelu jako webové služby později v části 2 tohoto kurzu.

Jakmile je prostředí definované, zaregistrujte ho v pracovním prostoru a znovu ho použijte v části 2 tohoto kurzu.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Pak vytvořte ScriptRunConfig zadáním školicího skriptu, cíle výpočtů a prostředí.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Odeslání úlohy do clusteru

Spusťte experiment odesláním objektu ScriptRunConfig:

```python
run = exp.submit(config=src)
run
```

Vzhledem k tomu, že volání je asynchronní, vrátí stav **Příprava** nebo **spuštění** ihned po spuštění úlohy.

## <a name="monitor-a-remote-run"></a>Monitorování vzdáleného spuštění

V celkovém případě trvá první spuštění **přibližně 10 minut**. U následných spuštění, pokud se závislosti skriptů nezmění, je stejný obrázek znovu použit. Proto je čas spuštění kontejneru mnohem rychlejší.

Co se stane, když počkáte:

- **Vytvoření bitové kopie**: vytvoří se image Docker, která odpovídá prostředí Pythonu určenému prostředím Azure ml. Image se nahraje do pracovního prostoru. Vytvoření a nahrání obrázku trvá **přibližně pět minut**.

  Tato fáze se u každého prostředí Pythonu provede jednou, protože kontejner je uložený v mezipaměti pro další spuštění. Při vytváření image se streamují protokoly do historie spuštění. Pomocí těchto protokolů můžete monitorovat průběh vytváření imagí.

- **Škálování**: Pokud vzdálený cluster vyžaduje více uzlů, než kolik je aktuálně k dispozici, přidají se další uzly automaticky. Škálování obvykle trvá **přibližně pět minut.**

- **Spuštěno**: v této fázi jsou nezbytné skripty a soubory odeslány do cíle výpočtů. Pak jsou úložiště dat připojená nebo zkopírovaná. A pak se spustí **entry_script** . Když je úloha spuštěná, **stdout** a adresář **./logs** se streamují do historie spuštění. Průběh běhu můžete monitorovat pomocí těchto protokolů.

- **Následné zpracování**: adresář **./Outputs** tohoto spuštění se kopíruje do historie spuštění ve vašem pracovním prostoru, takže můžete získat přístup k těmto výsledkům.

Průběh spuštěné úlohy můžete sledovat několika způsoby. V tomto kurzu se používá widget Jupyter a `wait_for_completion` metoda.

### <a name="jupyter-widget"></a>Widget Jupyter

Sledujte průběh běhu pomocí [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Podobně jako odeslání běhu je pomůcka asynchronní a poskytuje aktualizace za provozu každých 10 až 15 sekund, dokud se nedokončí úloha:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Pomůcka bude vypadat jako na konci školení jako na následujícím místě:

![Widget Poznámkový blok](./media/tutorial-train-models-with-aml/widget.png)

Pokud potřebujete běh zrušit, můžete postupovat podle [těchto pokynů](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Trénování modelu a monitorování probíhají na pozadí. Počkejte, dokud model nedokončí školení, než spustíte více kódu. Použijte `wait_for_completion` k zobrazení po dokončení školení modelu:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Zobrazení výsledků spuštění

Teď je model vytrénovaný na vzdáleném clusteru. Načtěte přesnost modelu:

```python
print(run.get_metrics())
```

Výstup ukazuje, že vzdálený model má přesnost 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

V dalším kurzu si tento model podrobněji prozkoumáte podrobněji.

## <a name="register-model"></a>Registrace modelu

Poslední krok v školicím skriptu vypsal soubor `outputs/sklearn_mnist_model.pkl` v adresáři s názvem `outputs` ve virtuálním počítači clusteru, ve kterém je úloha spuštěná. `outputs` je speciální adresář v tom, že se veškerý obsah v tomto adresáři automaticky nahraje do vašeho pracovního prostoru. Tento obsah se objeví v záznamu spuštění v experimentu pod vaším pracovním prostorem. Proto je soubor modelu teď dostupný i ve vašem pracovním prostoru.

Můžete zobrazit soubory spojené s tímto spuštěním:

```python
print(run.get_file_names())
```

Zaregistrujte model v pracovním prostoru, abyste vy nebo jiní spolupracovníci mohli později dotazovat, prozkoumávat a nasazovat tento model:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Můžete také odstranit pouze výpočetní cluster Azure Machine Learning. Automatické škálování je ale zapnuté a minimum clusteru je nula. Takže se tomuto konkrétnímu prostředku neúčtují další poplatky za výpočetní prostředky, pokud se nepoužívá:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Další kroky

V tomto Azure Machine Learning kurzu jste použili Python pro následující úlohy:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Přístup k datům a jejich kontrola
> * Výuka více modelů na vzdáleném clusteru s využitím oblíbené knihovny Machine Learningu v scikit
> * Zkontrolujte podrobnosti školení a zaregistrujte nejlepší model.

Jste připraveni nasadit tento registrovaný model pomocí pokynů v další části série kurzů:

> [!div class="nextstepaction"]
> [Kurz 2: Nasazení modelů](tutorial-deploy-models-with-aml.md)
