---
title: 'Kurz: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning'
description: Tento kurz ukazuje, jak pomocí služby Azure Machine Learning trénovat model klasifikace obrázků s využitím knihovny scikit-learn v poznámkovém bloku Python Jupyter. Tento kurz je první částí z dvoudílné série.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e6e49a03ee76c50cb2fff492bfd50b2820abafe4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343754"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning-service"></a>Kurz 1: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning

V tomto kurzu budete trénovat model strojového učení místně i pomocí vzdálených výpočetních prostředků. Budete používat pracovní postup pro trénování a nasazení pro službu Azure Machine Learning (Preview) v poznámkovém bloku Python Jupyter.  Poznámkový blok poté můžete použít jako šablonu k trénování vlastního modelu strojového učení s vlastními daty. Tento kurz je **první částí z dvoudílné série kurzů**.  

Tento kurz je zaměřený na trénování jednoduché logistické regrese pomocí datové sady [MNIST](http://yann.lecun.com/exdb/mnist/) a knihovny [scikit-learn](http://scikit-learn.org) pomocí služby Azure Machine Learning.  MNIST je oblíbená datová sada obsahující 70 000 obrázků ve stupních šedi. Každý obrázek je ručně psaná číslice o velikosti 28x28 pixelů představující číslo od 0 do 9. Cílem je vytvoření klasifikátoru s více třídami pro identifikaci číslice, kterou představuje daný obrázek. 

Naučte se:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Získání přístupu k datům a zkoumání dat
> * Místní trénování jednoduché logistické regrese pomocí scikit-learn, oblíbené knihovny pro strojové učení 
> * Trénování více modelů na vzdáleném clusteru
> * Ověření výsledků tréninku a registrace nejlepšího modelu

Postup pro výběr modelu a jeho nasazení se dozvíte v [druhé části tohoto kurzu](tutorial-deploy-models-with-aml.md) později. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="get-the-notebook"></a>Získání poznámkového bloku

V zájmu usnadnění práce je tento kurz dostupný jako [poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb). Spusťte poznámkový blok `01.train-models.ipynb` ve službě Azure Notebooks nebo na vlastním serveru poznámkového bloku Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> Tento kurz byl testován se sadou SDK služby Azure Machine Learning verze 0.168. 

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Veškeré nastavení pro vaši vývojovou práci se dá provést v poznámkovém bloku Pythonu.  Nastavení zahrnuje:

* Import balíčků Pythonu
* Připojení k pracovnímu prostoru, aby umožňoval komunikaci mezi místním počítačem a vzdálenými prostředky
* Vytvoření experimentu ke sledování všech spuštění
* Vytvoření cílového vzdáleného výpočetního prostředí pro trénování

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které potřebujete v této relaci. Zobrazte také verzi sady Azure Machine Learning SDK.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>Připojení k pracovnímu prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. `Workspace.from_config()` přečte soubor **config.json** a načte podrobnosti do objektu s názvem `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Vytvoření experimentu

Vytvořte experiment pro sledování spuštění ve vašem pracovním prostoru. Pracovní prostor může mít více experimentů. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Vytvoření cílového výpočetního prostředí

Azure Batch AI je spravovaná služba, která umožňuje odborníkům na zpracování dat trénovat modely strojového učení na clusterech virtuálních počítačů Azure, včetně virtuálních počítačů s podporou GPU.  V tomto kurzu vytvoříte cluster služby Azure Batch AI jako prostředí pro trénování. Tento kód pro vás vytvoří cluster, pokud ještě ve vašem pracovním prostoru neexistuje. 

 **Vytvoření clusteru trvá přibližně 5 minut.** Pokud cluster v pracovním prostoru již existuje, tento kód ho použije a přeskočí proces vytváření.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if type(compute_target) is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Nyní máte k dispozici potřebné balíčky a výpočetní prostředky pro trénink modelu v cloudu. 

## <a name="explore-data"></a>Zkoumání dat

Před zahájením trénování modelu musíte rozumět datům, která použijete k trénování.  Také je nutné zkopírovat data do cloudu, aby byla přístupná ve vašem cloudovém prostředí pro trénování.  V této části získáte informace o následujících postupech:

* Stáhnutí datové sady MNIST
* Zobrazení některých ukázkových obrázků
* Nahrání dat do cloudu

### <a name="download-the-mnist-dataset"></a>Stáhnutí datové sady MNIST

Stáhněte datovou sadu MNIST a uložte soubory do místního adresáře `data`.  Stáhnou se obrázky a popisky pro trénování i testování.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Zobrazení některých ukázkových obrázků

Načtěte komprimované soubory do pole `numpy`. Pak pomocí `matplotlib` vykreslete 30 náhodných obrázků z datové sady s jejich popisky nad nimi. Poznámka: Tento krok vyžaduje funkci `load_data`, která je součástí souboru `util.py`. Tento soubor je umístěný ve složce s ukázkou. Ujistěte se prosím, že je umístěný ve stejné složce jako tento poznámkový blok. Funkce `load_data` parsuje komprimované soubory do polí numpy.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
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

![náhodná ukázka obrázků](./media/tutorial-train-models-with-aml/digits.png)

Nyní máte představu o tom, jak tyto obrázky vypadají, a o očekávaném výstupu predikce.

### <a name="upload-data-to-the-cloud"></a>Nahrání dat do cloudu

Nyní zpřístupněte data vzdáleně tak, že je nahrajete z místního počítače do Azure, aby byla přístupná pro vzdálené trénování. Úložiště dat je vhodné zařízení přidružené k vašemu pracovnímu prostoru pro nahrávání a stahování dat, s nímž interaguje z vašich vzdálených výpočetních prostředí. Je podporováno účtem Azure Blob Storage.

Soubory MNIST se nahrají do adresáře `mnist` v kořenové složce úložiště dat.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
Teď máte všechno, co potřebujete k zahájení trénování modelu. 

## <a name="train-a-model-locally"></a>Místní trénování modelu

Trénujte místně jednoduchý model logistické regrese pomocí knihovny scikit-learn.

**Místní trénování může trvat minutu nebo dvě** v závislosti na konfiguraci počítače.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

V dalším kroku vytvořte predikce pomocí testovací sady a vypočítejte přesnost. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

Pro přesnost místního modelu se zobrazí:

`0.9202`

Pomocí pouhých několika řádků kódu jste dosáhli přesnost 92 %.

## <a name="train-on-a-remote-cluster"></a>Trénování na vzdáleném clusteru

Teď můžete tento jednoduchý model rozšířit vytvořením modelu s jinou mírou regulace. Tentokrát budete model trénovat na vzdáleném prostředku.  

Odešlete úlohu do clusteru pro vzdálené trénování, který jste nastavili dříve.  K odeslání úlohy je potřeba provést:
* Vytvoření adresáře
* Vytvoření trénovacího skriptu
* Vytvoření estimátoru
* Odeslání úlohy 

### <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte adresář, ze kterého bude dodán potřebný kód z počítače do vzdáleného prostředku.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Vytvoření trénovacího skriptu

Pokud chcete odeslat úlohu do clusteru, vytvořte nejprve trénovací skript. Spuštěním následujícího kódu vytvořte trénovací skript s názvem `train.py` v adresáři, který jste právě vytvořili. Tento trénink přidá míru regulace k trénovacímu algoritmu, což vytvoří mírně odlišný model než místní verze.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
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

+ Trénovací skript načte argument pro nalezení adresáře, který obsahuje data.  Když později odešlete úlohu, bude odkázána na úložiště dat pro tento argument: `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`

    
+ Trénovací skript uloží model do adresáře s názvem outputs. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Vše, co je v tomto adresáři zapsáno, se automaticky nahraje do vašeho pracovního prostoru. Později v tomto kurzu budete z tohoto adresáře přistupovat k vašemu modelu.

Soubor `utils.py` je odkazován z trénovacího skriptu pro správné načtení datové sady.  Zkopírujte tento skript do složky skriptu, aby byl přístupný spolu s trénovacím skriptem na vzdáleném prostředku.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Vytvoření estimátoru

Objekt estimátoru se používá k odeslání spuštění.  Vytvořte objekt estimátoru spuštěním následujícího kódu, který definuje:

* Název objektu estimátoru: `est`
* Adresář, který obsahuje vaše skripty. Všechny soubory v tomto adresáři se nahrají do uzlů clusteru ke spuštění. 
* Cílové výpočetní prostředí.  V tomto případě použijete cluster služby Batch AI, který jste vytvořili.
* Název trénovacího skriptu: train.py
* Parametry požadované z trénovacího skriptu. 
* Balíčky Pythonu potřebné pro trénování.

V tomto kurzu je cíl cluster služby Batch AI. Všechny soubory v tomto adresáři projektu se nahrají do uzlů clusteru ke spuštění. Parametr data_folder je nastavený pro použití úložiště dat (`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Odeslání úlohy do clusteru

Spusťte experiment odesláním objektu estimátoru.

```python
run = exp.submit(config=est)
run
```

Vzhledem k tomu, že volání je asynchronní, vrátí stav **Příprava** nebo **Spuštění** ihned po spuštění úlohy.

## <a name="monitor-a-remote-run"></a>Monitorování vzdáleného spuštění

První spuštění trvá **přibližně 10 minut**. Ale pro následná spouštění, dokud se nezmění závislosti skriptů, se znovu použije stejná image, a proto se kontejner spouští mnohem rychlejší.

Zde je uvedeno, co se děje, zatímco čekáte:

- **Vytvoření image**: Vytvoří se image Dockeru odpovídající prostředí Pythonu specifikovaném estimátorem. Image se nahraje do pracovního prostoru. Vytvoření a nahrání image trvá **asi 5 minut**. 

  Tato fáze nastane jednou pro každé prostředí Pythonu, protože kontejner je uložen v mezipaměti pro následná spouštění.  Při vytváření image se streamují protokoly do historie spuštění. Pomocí těchto protokolů můžete sledovat průběh vytváření image.

- **Škálování**: Pokud vzdálený cluster vyžaduje k provedení spuštění více uzlů, než je aktuálně k dispozici, přidají se automaticky další uzly. Škálování obvykle trvá **asi 5 minut**.

- **Spuštění**: V této fázi se do cílového výpočetního prostředí odešlou nezbytné skripty a soubory, následně jsou připojena/zkopírována úložiště dat a potom se spustí parametr entry_script. Když úloha běží, adresáře stdout a /logs se streamují do historie spuštění. Pomocí těchto protokolů můžete sledovat průběh spuštění.

- **Následné zpracování**: Adresář /outputs spuštění se zkopíruje do historie spuštění ve vašem pracovním prostoru, abyste získali přístup k těmto výsledkům.


Průběh spuštěné úlohy můžete kontrolovat několika různými způsoby. Tento kurz používá widget Jupyter a také metodu `wait_for_completion`. 

### <a name="jupyter-widget"></a>Widget Jupyter

Sledujte průběh spuštění pomocí widgetu Jupyter.  Podobně jako odeslání spuštění je tento widget asynchronní a poskytuje průběžné aktualizace každých 10 až 15 sekund, dokud se úloha nedokončí.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Zde je snímek widgetu zobrazeného na konci tréninku:

![widget poznámkového bloku](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Získání protokolu výsledků při dokončení

Trénování modelu a monitorování probíhají na pozadí. Před spuštěním dalšího kódu počkejte, než model dokončí trénink. Pomocí `wait_for_completion` zobrazte, kdy je trénování modelu dokončeno. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Zobrazení výsledků spuštění

Teď je model vytrénovaný na vzdáleném clusteru.  Načtěte přesnost modelu:

```python
print(run.get_metrics())
```
Výstup ukazuje, že vzdálený model má přesnost mírně vyšší než místní model, a to díky přidání míry regulace během tréninku.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

V kurzu nasazení tento model podrobněji prozkoumáte.

## <a name="register-model"></a>Registrace modelu

Poslední krok v trénovacím skriptu zapsal soubor `outputs/sklearn_mnist_model.pkl` do adresáře s názvem `outputs` ve virtuálním počítači clusteru, ve kterém se spouští úloha. `outputs` je speciální adresář v tom, že veškerý obsah v tomto adresáři se automaticky nahraje do vašeho pracovního prostoru.  Tento obsah se objeví v záznamu spuštění v experimentu pod vaším pracovním prostorem. Soubor modelu je tedy nyní k dispozici také ve vašem pracovním prostoru.

Můžete si zobrazit soubory související s tímto spuštěním.

```python
print(run.get_file_names())
```

Zaregistrujte model v pracovním prostoru, abyste mohli vy (nebo další spolupracovníci) později dotazovat, prozkoumat a nasadit tento model.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také odstranit pouze cluster spravovaného výpočetního prostředí Azure. Protože je však zapnuto automatické škálování a minimální hodnota clusteru je 0, tento konkrétní prostředek nevyvolá další poplatky za výpočty, pokud se nepoužívá.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu ke službě Azure Machine Learning jste pomocí Pythonu provedli následující úlohy:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Získání přístupu k datům a zkoumání dat
> * Místní trénování jednoduché logistické regrese pomocí scikit-learn, oblíbené knihovny pro strojové učení
> * Trénování více modelů na vzdáleném clusteru
> * Ověření podrobností o tréninku a registraci nejlepšího modelu

Jste připravení nasadit tento registrovaný model podle pokynů v další části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz 2: Nasazení modelů](tutorial-deploy-models-with-aml.md)
