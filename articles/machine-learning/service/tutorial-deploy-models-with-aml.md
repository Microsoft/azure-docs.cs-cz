---
title: 'Kurz klasifikace obrázků: nasazujte modely pomocí služby Azure Machine Learning'
description: Tento kurz ukazuje, jak pomocí služby Azure Machine Learning nasadit model klasifikace obrázků s využitím scikit-learn v poznámkovém bloku Python Jupyter.  Tento kurz je druhou částí z dvoudílné série.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
ms.custom: seodec12
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 6e56fe88eec70af313d0b0dd8cf0aaf27b054121
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011534"
---
# <a name="tutorial-part-2--deploy-an-image-classification-model-in-azure-container-instance-aci"></a>Kurz (část 2): nasazení modelu klasifikace image v Azure Container Instance (ACI)

Tento kurz je **druhou částí z dvoudílné série kurzů**. V [předchozím kurzu](tutorial-train-models-with-aml.md) jste trénovali modely strojového učení a pak jste zaregistrovali model ve vašem pracovním prostoru v cloudu.  

Teď jste připravení k tomu, abyste tento model nasadili jako webovou službu v [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Webová služba je image, v tomto případě image Dockeru, která obsahuje logiku hodnocení a samotný model. 

V této části kurzu provedete pomocí služby Azure Machine Learning následující úlohy:

> [!div class="checklist"]
> * Nastavení vašeho testovacího prostředí
> * Načtení modelu z vašeho pracovního prostoru
> * Místní testování modelu
> * Nasazení modelu do služby ACI
> * Testování nasazeného modelu

Služba ACI není pro produkční nasazení ideální, ale hodí se pro účely testování a porozumění pracovnímu postupu. Pro škálovatelná produkční nasazení zvažte použití [Azure Kubernetes Service](how-to-deploy-to-aks.md).

## <a name="get-the-notebook"></a>Získání poznámkového bloku

V zájmu usnadnění práce je tento kurz dostupný jako [poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part2-deploy.ipynb). Spusťte poznámkový blok `tutorials/img-classification-part2-deploy.ipynb` ve službě Azure Notebooks nebo na vlastním serveru poznámkového bloku Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> V tomto článku kódu byl testován s Azure Machine Learning SDK verze 1.0.2

## <a name="prerequisites"></a>Požadavky

Dokončete trénování modelu v poznámkovém bloku [Kurz 1: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).  


## <a name="set-up-the-environment"></a>Nastavení prostředí

Začněte tím, že nastavíte testovací prostředí.

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které jsou pro tento kurz potřeba.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Načtení modelu

V předchozím kurzu jste zaregistrovali model ve vašem pracovním prostoru. Teď načtete tento pracovní prostor a stáhnete model do místního adresáře.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-model-locally"></a>Místní testování modelu

Před nasazením ověřte pomocí těchto postupů, že model funguje místně:
* Načtení testovacích dat
* Předpovídání testovacích dat
* Prozkoumání chybové matice

### <a name="load-test-data"></a>Načtení testovacích dat

Načtěte testovací data z adresáře **./data/**, který se vytvořil během kurzu věnovaného trénování.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>Předpovídání testovacích dat

Vložte testovací datovou sadu do modelu, abyste získali předpovědi.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Prozkoumání chybové matice

Vygenerujte chybovou matici, abyste zjistili, kolik vzorků z testovací sady má správnou klasifikaci. Všimněte si chybně klasifikované hodnoty u nesprávných předpovědí. 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Výstup zobrazuje chybovou matici:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Pomocí knihovny `matplotlib` zobrazíte chybovou matici jako graf. V tomto grafu osa X představuje skutečné hodnoty a osa Y představuje předpovídané hodnoty. Barva v jednotlivých polích mřížky představuje chybovost. Čím světlejší je barva, tím vyšší je chybovost. Například mnoho hodnot 5 je chybně klasifikovaných jako hodnoty 3. Pole mřížky (5;3) je tedy světlé.

```python
# normalize the diagnal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![chybová matice](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-web-service"></a>Nasazení jako webová služba

Pokud jste model otestovali a jste s výsledky spokojení, nasaďte tento model jako webovou službu hostovanou v ACI. 

K vytvoření správného prostředí pro ACI je potřeba zadat tyto položky:
* Hodnoticí skript, který ukáže, jak se daný model používá
* Soubor prostředí, který ukáže balíčky, které je nutné nainstalovat
* Konfigurační soubor pro sestavení ACI
* Model, který jste natrénovali

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Vytvoření hodnoticího skriptu

Vytvořte hodnoticí skript s názvem score.py, pomocí kterého volání webové služby ukáže, jak se model používá.

Do hodnoticího skriptu je nutné zahrnout dvě povinné funkce:
* Funkce `init()`, která obvykle načítá daný model do globálního objektu. Tato funkce se spustí jenom jednou, a to při spuštění kontejneru Dockeru. 

* Funkce `run(input_data)` používá daný model k předpovědi hodnoty na základě vstupních dat. Vstupy a výstupy spuštění obvykle pro serializaci a deserializaci používají JSON, ale podporují se i další formáty.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retreive the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Vytvoření souboru prostředí

Pak vytvořte soubor prostředí s názvem myenv.yml, který určuje všechny závislosti balíčků daného skriptu. Tento soubor slouží k tomu, aby se zajistila instalace všech těchto závislostí do image Dockeru. Tento model vyžaduje `scikit-learn` a `azureml-sdk`.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Zkontrolujte obsah souboru `myenv.yml`.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-configuration-file"></a>Vytvoření konfiguračního souboru

Vytvořte konfigurační soubor nasazení a zadejte počet procesorů a velikost paměti RAM v GB, které vyžaduje kontejner ACI. Tyto hodnoty jsou závislé na příslušném modelu, ale pro mnoho modelů obvykle postačují výchozí hodnoty 1 jádro a 1 GB RAM. Pokud později zjistíte, že je potřeba tyto hodnoty navýšit, bude nutné znovu vytvořit danou image a znovu nasadit danou službu.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Nasazení v ACI
Odhadovaný čas dokončení: **asi 7–8 minut**

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Sestavení image pomocí těchto souborů:
   * Soubor vyhodnocení (`score.py`)
   * Soubor prostředí (`myenv.yml`)
   * Soubor modelu
1. Registrace dané image v pracovním prostoru 
1. Odeslání image do kontejneru ACI
1. Spuštění kontejneru v ACI pomocí dané image
1. Získání koncového bodu HTTP webové služby


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod lze sdílet s kýmkoli, kdo chce danou webovou službu otestovat nebo ji integrovat do aplikace. 

```python
print(service.scoring_uri)
```


## <a name="test-deployed-service"></a>Testování nasazené služby

V dřívější fázi jste hodnotili všechna testovací data pomocí místní verze modelu. Teď můžete otestovat nasazený model pomocí náhodného vzorku 30 obrázků z testovacích dat.  

Následující kód provede tyto kroky:
1. Odeslání dat ve formátu pole JSON do webové služby hostované v ACI 

1. Použití rozhraní `run` API sady SDK k vyvolání služby. Základní volání lze uskutečnit také pomocí libovolného nástroje HTTP, jako je například curl.

1. Tisk vrácených předpovědí a jejich vykreslení spolu se vstupními obrázky. K označení chybně klasifikovaných vzorků se používají červené písmo a inverzní obraz (bílá na černém pozadí). 

 Přesnost modelu je vysoká, takže ke zjištění chybně klasifikovaného vzorku stačí několikrát spustit následující kód.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Tady je výsledek pro jeden náhodný vzorek testovacích obrázků: ![výsledky](./media/tutorial-deploy-models-with-aml/results.png)

Při testování webové služby můžete odeslat také požadavek HTTP v základním formátu.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Aby se skupina prostředků a pracovní prostor zachovaly pro další kurzy a zkoumání, je možné nasazení ACI odstranit jenom pomocí tohoto volání rozhraní API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu ke službě Azure Machine Learning jste pomocí Pythonu provedli následující úlohy:

> [!div class="checklist"]
> * Nastavení vašeho testovacího prostředí
> * Načtení modelu z vašeho pracovního prostoru
> * Místní testování modelu
> * Nasazení modelu do služby ACI
> * Testování nasazeného modelu
 
Můžete taky vyzkoušet kurz [Automatický výběr algoritmu](tutorial-auto-train-models.md), abyste viděli, jak může služba Azure Machine Learning automaticky vybrat a vyladit nejlepší algoritmus pro daný model a tento model vám sestavit.
