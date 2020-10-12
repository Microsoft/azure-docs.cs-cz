---
title: 'Kurz k klasifikaci imagí: nasazení modelů'
titleSuffix: Azure Machine Learning
description: Tento kurz, druhý z řad dvou částí, ukazuje, jak použít Azure Machine Learning k nasazení modelu klasifikace Image pomocí scikit-učení v poznámkovém bloku Python Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: f0d78fc55ca60fa883c742885acf2fa98ede61ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896585"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Kurz: nasazení modelu klasifikace imagí v Azure Container Instances


Tento kurz je **druhou částí z dvoudílné série kurzů**. V [předchozím kurzu](tutorial-train-models-with-aml.md) jste trénovali modely strojového učení a pak jste zaregistrovali model ve vašem pracovním prostoru v cloudu.  Nyní jste připraveni nasadit model jako webovou službu. Webová služba je image, v tomto případě image Docker. Zapouzdřuje logiku bodování a model sám sebe. 

V této části kurzu použijete Azure Machine Learning pro následující úlohy:

> [!div class="checklist"]
> * Nastavte testovací prostředí.
> * Načtěte model z pracovního prostoru.
> * Nasaďte model do Container Instances.
> * Otestujte nasazený model.

Container Instances je skvělé řešení pro testování a porozumění pracovnímu postupu. Pro škálovatelná produkční nasazení zvažte použití Azure Kubernetes Service. Další informace naleznete v tématu [Jak nasadit a kde](how-to-deploy-and-where.md).

>[!NOTE]
> Kód v tomto článku byl testován pomocí sady Azure Machine Learning SDK 1.0.83 verze.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit Poznámkový blok, nejdřív dokončete školení modelu v [kurzu (část 1): výuka modelu klasifikace imagí](tutorial-train-models-with-aml.md).   Pak otevřete Poznámkový blok  *img-Classification-Část2-Deploy. ipynb* v naklonovaných *kurzech/složce image-Classification-mnist ručně zapsaných-data* .

Tento kurz je také k dispozici na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , pokud ho chcete použít ve svém vlastním [místním prostředí](how-to-configure-environment.md#local).  Ujistěte se, že máte nainstalované `matplotlib` a `scikit-learn` ve vašem prostředí. 

> [!Important]
> Zbývající část tohoto článku obsahuje stejný obsah, jaký vidíte v poznámkovém bloku.  
>
> Pokud chcete při spuštění kódu číst společně, přepněte do poznámkového bloku Jupyter.
> Pokud chcete na poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **SHIFT + ENTER**. Případně spusťte celý Poznámkový blok výběrem možnosti **Spustit vše** na horním panelu nástrojů.

## <a name="set-up-the-environment"></a><a name="start"></a>Nastavení prostředí

Začněte tím, že nastavíte testovací prostředí.

### <a name="import-packages"></a>Import balíčků

Naimportujte balíčky Pythonu, které jsou pro tento kurz potřeba.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Nasazení jako webová služba

Model nasaďte jako webovou službu hostovanou v ACI. 

K vytvoření správného prostředí pro ACI je potřeba zadat tyto položky:
* Hodnoticí skript, který ukáže, jak se daný model používá
* Konfigurační soubor pro sestavení ACI
* Model, který jste natrénovali

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
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
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
Odhadovaný čas dokončení: **přibližně 2-5 minut**

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Vytvoří objekt prostředí obsahující závislosti, které model potřebuje, pomocí prostředí ( `tutorial-env` ) uloženého během školení.
1. Vytvořte odvozenou konfiguraci nezbytnou k nasazení modelu jako webové služby pomocí:
   * Soubor vyhodnocení (`score.py`)
   * objekt prostředí vytvořený v předchozím kroku
1. Nasaďte model do kontejneru ACI.
1. Získání koncového bodu HTTP webové služby


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod lze sdílet s kýmkoli, kdo chce danou webovou službu otestovat nebo ji integrovat do aplikace.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testování modelu


### <a name="download-test-data"></a>Stáhnout testovací data
Stažení testovacích dat do adresáře **./data/**


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Načtení testovacích dat

Načtěte testovací data z adresáře **./data/**, který se vytvořil během kurzu věnovaného trénování.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Předpovídání testovacích dat

Vložte testovací datovou sadu do modelu, abyste získali předpovědi.


Následující kód provede tyto kroky:
1. Odeslání dat ve formátu pole JSON do webové služby hostované v ACI 

1. Použití rozhraní `run` API sady SDK k vyvolání služby. Základní volání lze uskutečnit také pomocí libovolného nástroje HTTP, jako je například curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
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

```output
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
```

Pomocí knihovny `matplotlib` zobrazíte chybovou matici jako graf. V tomto grafu osa X představuje skutečné hodnoty a osa Y představuje předpovídané hodnoty. Barva v jednotlivých polích mřížky představuje chybovost. Čím světlejší je barva, tím vyšší je chybovost. Například mnoho hodnot 5 je chybně klasifikovaných jako hodnoty 3. Zobrazí se Světlá mřížka na (5, 3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
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

![Graf znázorňující nejasnost matice](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Zobrazit předpovědi

Otestujte nasazený model s náhodným vzorkem 30 imagí z testovacích dat.  


1. Tisk vrácených předpovědí a jejich vykreslení spolu se vstupními obrázky. K označení chybně klasifikovaných vzorků se používají červené písmo a inverzní obraz (bílá na černém pozadí). 

 Přesnost modelu je vysoká, takže než se objeví chybně klasifikovaný vzorek, bude asi potřeba spustit následující kód několikrát.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

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
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Při testování webové služby můžete odeslat také požadavek HTTP v základním formátu.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

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

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro jiné kurzy a průzkum, můžete odstranit jenom nasazení Container Instances pomocí tohoto volání rozhraní API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Další kroky

+ Přečtěte si o všech [možnostech nasazení Azure Machine Learning](how-to-deploy-and-where.md).
+ Naučte se [vytvářet klienty pro webovou službu](how-to-consume-web-service.md).
+  [Provádějte asynchronní předpovědi velké množství dat](how-to-use-parallel-run-step.md) .
+ Monitorujte Azure Machine Learning modely pomocí [Application Insights](how-to-enable-app-insights.md).
+ Vyzkoušejte kurz pro [Automatický výběr algoritmu](tutorial-auto-train-models.md) . 
