---
title: 'Kurz klasifikace obrázků: Modely vlaků'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning slouží k trénování modelu klasifikace bitových obrázků pomocí scikit-learn v poznámkovém bloku Pythonjupyter. Tento výukový program je první ze dvou částí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 8cf46db06a4a2f8fa86f97dab5a8477cf427c999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159064"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Kurz: Vlak modely klasifikace obrázků s daty MNIST a scikit-learn 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu trénujete model strojového učení na vzdálených výpočetních prostředcích. Pracovní postup školení a nasazení pro Azure Machine Learning použijete v poznámkovém bloku Pythonu Jupyter.  Poznámkový blok poté můžete použít jako šablonu k trénování vlastního modelu strojového učení s vlastními daty. Tento kurz je **první částí z dvoudílné série kurzů**.  

Tento kurz trénuje jednoduchou logistickou regresi pomocí datové sady [MNIST](http://yann.lecun.com/exdb/mnist/) a [scikit-learn](https://scikit-learn.org) s Azure Machine Learning. MNIST je oblíbená datová sada obsahující 70 000 obrázků ve stupních šedi. Každý obrázek je ručně psaná číslice 28 x 28 pixelů, představující číslo od nuly do devíti. Cílem je vytvoření klasifikátoru s více třídami pro identifikaci číslice, kterou představuje daný obrázek.

Přečtěte si, jak provést následující akce:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Přístup k datům a jejich prozkoumání.
> * Trénování jednoduchého logistického regresního modelu ve vzdáleném clusteru.
> * Zkontrolujte výsledky školení a zaregistrujte nejlepší model.

Dozvíte se, jak vybrat model a nasadit jej v [druhé části tohoto kurzu](tutorial-deploy-models-with-aml.md).

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

>[!NOTE]
> Kód v tomto článku byl testován pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) verze 1.0.65.

## <a name="prerequisites"></a>Požadavky

* Dokončete [kurz: Začínáme vytvářet první experiment Azure ML,](tutorial-1st-experiment-sdk-setup.md) abyste mohli:
    * Vytvoření pracovního prostoru
    * Naklonujte poznámkový blok kurzů do složky v pracovním prostoru.
    * Vytvořte cloudovou výpočetní instanci.

* Ve složce klonovaných *kurzů/image-classification-mnist-data* otevřete poznámkový blok *img-classification-part1-training.ipynb.* 


Výukový program a doprovodný **soubor utils.py** je také k dispozici na [GitHubu,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) pokud jej chcete použít ve svém vlastním [místním prostředí](how-to-configure-environment.md#local). Spusťte `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` instalaci závislostí pro tento kurz.

> [!Important]
> Zbytek tohoto článku obsahuje stejný obsah, který vidíte v poznámkovém bloku.  
>
> Přepněte do poznámkového bloku Jupyter nyní, pokud si chcete přečíst spolu při spuštění kódu. 
> Pokud chcete v poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **Shift+Enter**. Nebo spusťte celý poznámkový blok tak, že zvolíte **Spustit vše** z horního panelu nástrojů.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Nastavení vývojového prostředí

Veškeré nastavení pro vaši vývojovou práci se dá provést v poznámkovém bloku Pythonu. Instalační program zahrnuje následující akce:

* Importujte balíčky Pythonu.
* Připojte se k pracovnímu prostoru, aby místní počítač mohl komunikovat se vzdálenými prostředky.
* Vytvořte experiment pro sledování všech vašich běhů.
* Vytvořte vzdálený výpočetní cíl, který se použije pro školení.

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které potřebujete v této relaci. Zobrazte také verzi sady Azure Machine Learning SDK:

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

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. `Workspace.from_config()`Přečte soubor **config.json** a načte `ws`podrobnosti do objektu s názvem :

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

### <a name="create-or-attach-an-existing-compute-target"></a>Vytvoření nebo připojení existujícího výpočetního cíle

Pomocí Azure Machine Learning Compute, spravované služby, mohou datoví vědci trénovat modely strojového učení v clusterech virtuálních počítačů Azure. Mezi příklady patří virtuální zařízení s podporou GPU. V tomto kurzu vytvoříte Azure Machine Learning Compute jako tréninkové prostředí. Kód Pythonu, který se spustí na tomto virtuálním počítači, se spustí později v kurzu. 

Níže uvedený kód vytvoří výpočetní clustery pro vás, pokud ještě neexistují ve vašem pracovním prostoru.

 **Vytvoření výpočetního cíle trvá asi pět minut.** Pokud výpočetní prostředek je již v pracovním prostoru, kód používá a přeskočí proces vytváření.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

Před trénováním modelu je třeba porozumět datům, která používáte k jeho trénování. V této části získáte informace o následujících postupech:

* Stáhněte si datovou sadu MNIST.
* Zobrazí některé ukázkové obrázky.

### <a name="download-the-mnist-dataset"></a>Stáhnutí datové sady MNIST

Pomocí otevřených datových sad Azure získáte nezpracované datové soubory MNIST. [Azure Open Datasets](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) jsou sestavené veřejné datové sady, které můžete použít k přidání funkcí specifických pro scénář do řešení strojového učení pro přesnější modely. Každá datová sada má `MNIST` odpovídající třídu, v tomto případě načíst data různými způsoby.

Tento kód načte data `FileDataset` jako objekt, který `Dataset`je podtřídou . Odkazuje `FileDataset` na jednotlivé nebo více souborů libovolného formátu ve vašem úložišti dat nebo veřejných adres URL. Třída poskytuje možnost stáhnout nebo připojit soubory k výpočtu vytvořením odkazu na umístění zdroje dat. Kromě toho zaregistrujete datovou sadu do pracovního prostoru pro snadné načítání během školení.

Postupujte [podle návodu, jak](how-to-create-register-datasets.md) se dozvědět více o datových sadách a jejich použití v sadách SDK.

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

Načtěte komprimované soubory do pole `numpy`. Pak pomocí `matplotlib` vykreslete 30 náhodných obrázků z datové sady s jejich popisky nad nimi. Tento krok `load_data` vyžaduje funkci, která `util.py` je součástí souboru. Tento soubor je umístěný ve složce s ukázkou. Ujistěte se, že je umístěn ve stejné složce jako tento poznámkový blok. Funkce `load_data` jednoduše analyzuje komprimované soubory do numpy polí.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, "train-images-idx3-ubyte.gz"), False) / 255.0
X_test = load_data(os.path.join(data_folder, "t10k-images-idx3-ubyte.gz"), False) / 255.0
y_train = load_data(os.path.join(data_folder, "train-labels-idx1-ubyte.gz"), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, "t10k-labels-idx1-ubyte.gz"), True).reshape(-1)

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

![Náhodný vzorek obrázků](./media/tutorial-train-models-with-aml/digits.png)

Nyní máte představu o tom, jak tyto obrázky vypadají, a o očekávaném výstupu predikce.

## <a name="train-on-a-remote-cluster"></a>Trénování na vzdáleném clusteru

Pro tuto úlohu odešlete úlohu ke spuštění ve vzdáleném trénovacím clusteru, který jste nastavili dříve.  K odeslání úlohy je potřeba provést:
* Vytvoření adresáře
* Vytvoření trénovacího skriptu
* Vytvoření objektu odhadu
* Odeslání úlohy

### <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte adresář, ze kterého bude dodán potřebný kód z počítače do vzdáleného prostředku.

```python
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
from sklearn.externals import joblib

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

+ Trénovací skript přečte argument najít adresář, který obsahuje data. Když později odešlete úlohu, bude odkázána na úložiště dat pro tento argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Školicí skript uloží model do adresáře s názvem **výstupy**. Vše, co je v tomto adresáři zapsáno, se automaticky nahraje do vašeho pracovního prostoru. Přístup k modelu z tohoto adresáře dále v kurzu. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Trénovací skript `utils.py` vyžaduje, aby soubor správně načetl datovou sadu. Následující kód `utils.py` se `script_folder` zkopíruje do tak, aby soubor přístupný spolu s trénovací skript na vzdáleném prostředku.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Vytvoření estimátoru

[SKLearn odhad](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) objektse používá k odeslání spustit. Vytvořte estimátor spuštěním následujícího kódu, který definuje tyto položky:

* Název objektu estimátoru je `est`.
* Adresář, který obsahuje vaše skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění.
* Cílové výpočetní prostředí. V tomto případě používáte cluster pro výpočty služby Azure Machine Learning, který jste vytvořili.
* Název školicího skriptu **train.py**.
* Parametry požadované z trénovacího skriptu.

V tomto kurzu je tento cíl AmlCompute. Všechny soubory ve složce skriptů jsou odeslány do uzlů clusteru pro spuštění. **Data_folder** je nastavena na použití datové sady. Nejprve vytvořte objekt prostředí, který určuje závislosti potřebné pro školení. 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

Potom vytvořte odhad s následujícím kódem.

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env, 
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Odeslání úlohy do clusteru

Spusťte experiment odesláním objektu odhadu:

```python
run = exp.submit(config=est)
run
```

Vzhledem k tomu, že volání je asynchronní, vrátí **stav Příprava** nebo **Spuštění,** jakmile je úloha spuštěna.

## <a name="monitor-a-remote-run"></a>Monitorování vzdáleného spuštění

Celkově první běh trvá **asi 10 minut**. Ale pro následné spuštění, tak dlouho, dokud se nezmění závislosti skriptu, stejný obrázek je znovu použit. Takže doba spuštění kontejneru je mnohem rychlejší.

Co se stane, když čekáte:

- **Vytvoření obrázku**: Vytvoří se obrázek Dockeru, který odpovídá prostředí Pythonu určenému odhadcem. Image se nahraje do pracovního prostoru. Vytváření a nahrávání obrázků trvá **asi pět minut**.

  Tato fáze se stane jednou pro každé prostředí Pythonu, protože kontejner je uložen do mezipaměti pro následné spuštění. Při vytváření image se streamují protokoly do historie spuštění. Průběh vytváření obrazu můžete sledovat pomocí těchto protokolů.

- **Změna měřítka**: Pokud vzdálený cluster vyžaduje ke spuštění více uzlů, než je aktuálně k dispozici, budou automaticky přidány další uzly. Škálování obvykle trvá **asi pět minut.**

- **Spuštění**: V této fázi jsou potřebné skripty a soubory odeslány do výpočetního cíle. Potom datastores jsou připojeny nebo zkopírovány. A pak je **entry_script** spuštěn. Při spuštění úlohy jsou **stdout** a adresář **./logs** přenášeny datovým proudem do historie spuštění. Průběh spuštění můžete sledovat pomocí těchto protokolů.

- **Post-processing**: Adresář **./výstupy** spuštění je zkopírován do historie spuštění v pracovním prostoru, takže k těmto výsledkům máte přístup.

Průběh spuštěné úlohy můžete zkontrolovat několika způsoby. Tento kurz používá widget Jupyter a metodu. `wait_for_completion`

### <a name="jupyter-widget"></a>Widget Jupyter

Sledujte průběh běhu s [widgetem Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Stejně jako spuštění podání, widget je asynchronní a poskytuje živé aktualizace každých 10 až 15 sekund, dokud úloha neskončí:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Widget bude vypadat takto na konci tréninku:

![Widget poznámkový blok](./media/tutorial-train-models-with-aml/widget.png)

Pokud potřebujete zrušit spuštění, můžete postupovat podle [těchto pokynů](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Trénování modelu a monitorování probíhají na pozadí. Počkejte, až model dokončil školení před spuštěním další kód. Slouží `wait_for_completion` k zobrazení po dokončení trénování modelu:

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

V dalším kurzu prozkoumat tento model podrobněji.

## <a name="register-model"></a>Registrace modelu

Poslední krok v trénovacím `outputs/sklearn_mnist_model.pkl` skriptu `outputs` napsal soubor do adresáře pojmenovaného ve virtuálním provozu clusteru, kde je úloha spuštěna. `outputs`je zvláštní adresář v tom, že veškerý obsah v tomto adresáři je automaticky odeslán do pracovního prostoru. Tento obsah se objeví v záznamu spuštění v experimentu pod vaším pracovním prostorem. Soubor modelu je tedy nyní k dispozici také ve vašem pracovním prostoru.

Můžete zobrazit soubory přidružené k tomuto spuštění:

```python
print(run.get_file_names())
```

Zaregistrujte model v pracovním prostoru, abyste vy nebo jiní spolupracovníci mohli později dotazovat, zkoumat a nasazovat tento model:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Můžete také odstranit jenom cluster Azure Machine Learning Compute. Automatické škálování je však zapnuto a minimální hodnota clusteru je nula. Takže tento konkrétní prostředek nebude účtovány další výpočetní poplatky, pokud se nepoužívá:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu Azure Machine Learning jste použili Python pro následující úkoly:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Přístup k datům a jejich prozkoumání.
> * Trénování více modelů ve vzdáleném clusteru pomocí oblíbené knihovny strojového učení scikit-learn
> * Zkontrolujte podrobnosti školení a zaregistrujte nejlepší model.

Jste připraveni nasadit tento registrovaný model pomocí pokynů v další části série kurzů:

> [!div class="nextstepaction"]
> [Kurz 2: Nasazení modelů](tutorial-deploy-models-with-aml.md)
