---
title: 'Kurz klasifikace obrázků: Nasazení modelů'
titleSuffix: Azure Machine Learning
description: Tento kurz, druhý ze dvou částí řady, ukazuje, jak pomocí Azure Machine Learning nasadit model klasifikace bitových obrázků s scikit-learn v poznámkovém bloku Python Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 81e02492f7e79b87e1513a910afe4719908adbbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80159063"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Kurz: Nasazení modelu klasifikace bitových obrázků v instanci kontejneru Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento kurz je **druhou částí z dvoudílné série kurzů**. V [předchozím kurzu](tutorial-train-models-with-aml.md) jste trénovali modely strojového učení a pak jste zaregistrovali model ve vašem pracovním prostoru v cloudu.  Nyní jste připraveni nasadit model jako webovou službu. Webová služba je bitová kopie, v tomto případě image Dockeru. Zapouzdřuje logiku bodování a samotný model. 

V této části kurzu použijete Azure Machine Learning pro následující úkoly:

> [!div class="checklist"]
> * Nastavte testovací prostředí.
> * Načíst model z pracovního prostoru.
> * Otestujte model místně.
> * Nasazení modelu do instancí kontejneru.
> * Otestujte nasazený model.

Instance kontejneru je skvělé řešení pro testování a pochopení pracovního postupu. Pro škálovatelná produkční nasazení zvažte použití Azure Kubernetes Service. Další informace naleznete v tématu [jak nasadit a kde](how-to-deploy-and-where.md).

>[!NOTE]
> Kód v tomto článku byl testován pomocí sady Azure Machine Learning SDK verze 1.0.41.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit poznámkový blok, nejprve dokončete školení modelu v [kurzu (část 1): Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md).   Pak otevřete poznámkový blok *img-classification-part2-deploy.ipynb* ve složce klonovaných *kurzů/image-classification-mnist-data.*

Tento výukový program je také k dispozici na [GitHubu,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) pokud jej chcete použít ve svém vlastním [místním prostředí](how-to-configure-environment.md#local).  Ujistěte se, `matplotlib` `scikit-learn` že jste nainstalovali a ve vašem prostředí. 

> [!Important]
> Zbytek tohoto článku obsahuje stejný obsah, který vidíte v poznámkovém bloku.  
>
> Přepněte do poznámkového bloku Jupyter nyní, pokud si chcete přečíst spolu při spuštění kódu.
> Pokud chcete v poznámkovém bloku spustit jednu buňku kódu, klikněte na buňku kódu a stiskněte **Shift+Enter**. Nebo spusťte celý poznámkový blok tak, že zvolíte **Spustit vše** z horního panelu nástrojů.

## <a name="set-up-the-environment"></a><a name="start"></a>Nastavení prostředí

Začněte tím, že nastavíte testovací prostředí.

### <a name="import-packages"></a>Import balíčků

Import balíčků Pythonu potřebných pro tento kurz:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Načtení modelu

V předchozím kurzu jste zaregistrovali model ve vašem pracovním prostoru. Nyní načtěte tento pracovní prostor a stáhněte model do místního adresáře:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Místní testování modelu

Před nasazením se ujistěte, že váš model funguje místně:
* Načíst testovací data.
* Předpovědět testovací data.
* Prozkoumejte matici záměny.

### <a name="load-test-data"></a>Načtení testovacích dat

Načtěte testovací data z adresáře **./data/** vytvořeného během školení:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Předpovídání testovacích dat

Chcete-li získat předpovědi, krmit testovací datové sady na model:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Prozkoumání chybové matice

Vygenerujte chybovou matici, abyste zjistili, kolik vzorků z testovací sady má správnou klasifikaci. Všimněte si chybně klasifikované hodnoty pro nesprávné předpovědi: 

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
   

Pomocí knihovny `matplotlib` zobrazíte chybovou matici jako graf. V tomto grafu osa x zobrazuje skutečné hodnoty a osa y zobrazuje předpokládané hodnoty. Barva v každé mřížce zobrazuje chybovost. Čím světlejší je barva, tím vyšší je chybovost. Například mnoho 5 je nesprávně klasifikovány jako 3. Takže vidíte jasnou mřížku na (5,3):

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

![Graf znázorňující matici záměny](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Nasazení jako webové služby

Po otestování modelu a jste spokojeni s výsledky, nasadit model jako webovou službu hostované v kontejneru instance. 

Chcete-li vytvořit správné prostředí pro instance kontejneru, zadejte následující součásti:
* Bodovací skript, který ukazuje, jak používat model.
* Soubor prostředí, který ukazuje, jaké balíčky je třeba nainstalovat.
* Konfigurační soubor k vytvoření instance kontejneru.
* Model, který jste trénovali dříve.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Vytvoření hodnoticího skriptu

Vytvořte bodovací skript s názvem **score.py**. Volání webové služby používá tento skript k zobrazení způsobu použití modelu.

Do bodovacího skriptu zahrňte tyto dvě požadované funkce:
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
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Vytvoření souboru prostředí

Dále vytvořte soubor prostředí, nazvaný **myenv.yml**, který určuje všechny závislosti balíčku skriptu. Tento soubor se používá k ujistěte se, že všechny tyto závislosti jsou nainstalovány v image Dockeru. Tento model vyžaduje `scikit-learn` a `azureml-sdk`. Všechny soubory vlastního prostředí je třeba seznam azureml-defaults s verion >= 1.0.45 jako pip závislost. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby.

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Zkontrolujte obsah `myenv.yml` souboru:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Vytvoření konfiguračního souboru

Vytvořte konfigurační soubor nasazení. Zadejte počet procesorů a gigabajtů paměti RAM potřebných pro kontejner Instance kontejneru. I když to závisí na modelu, výchozí jedno jádro a 1 gigabajt paměti RAM je dostatečná pro mnoho modelů. Pokud budete později potřebovat více, budete muset znovu vytvořit bitovou kopii a znovu nasadit službu.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Nasazení v instancích kontejnerů
Předpokládaná doba do dokončení nasazení je **asi sedm až osm minut**.

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Vytvoření bitové kopie pomocí těchto souborů:
   * Bodovací soubor `score.py`, .
   * Soubor prostředí, `myenv.yml`.
   * Soubor modelu.
1. Zaregistrujte obrázek pod pracovním prostorem. 
1. Odešlete bitovou kopii do kontejneru Instance kontejneru.
1. Spusťte kontejner v instancích kontejneru pomocí bitové kopie.
1. Získání koncového bodu HTTP webové služby

Vezměte prosím na vědomí, že pokud definujete vlastní soubor prostředí, musíte uvést azureml-defaults s verzí >= 1.0.45 jako závislost pip. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod můžete sdílet s kýmkoli, kdo chce webovou službu otestovat nebo integrovat do aplikace: 

```python
print(service.scoring_uri)
```

## <a name="test-the-deployed-service"></a>Testování nasazené služby

Dříve jste získali všechna testovací data s místní verzí modelu. Nyní můžete otestovat nasazený model s náhodným vzorkem 30 bitových kopií z testovacích dat.  

Následující kód provede tyto kroky:
1. Odešlete data jako pole JSON do webové služby hostované v instancích kontejneru. 

1. Použití rozhraní `run` API sady SDK k vyvolání služby. Můžete také provádět nezpracovaná volání pomocí libovolného nástroje HTTP, například **curl**.

1. Tisk vrácených předpovědí a jejich vykreslení spolu se vstupními obrázky. Červené písmo a inverzní obrázek, bílá na černé, se používá ke zvýraznění chybně klasifikovaných vzorků. 

Vzhledem k tomu, že přesnost modelu je vysoká, bude pravděpodobně muset spustit následující kód několikrát, než uvidíte chybně klasifikovaný vzorek:

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
plt.figure(figsize=(20, 1))

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

Tento výsledek je z jednoho náhodného vzorku testovacích obrazů:

![Obrázek znázorňující výsledky](./media/tutorial-deploy-models-with-aml/results.png)

Můžete také odeslat nezpracovaný požadavek HTTP k testování webové služby:

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

Chcete-li zachovat skupinu prostředků a pracovní prostor pro další kurzy a zkoumání, můžete odstranit pouze nasazení instance kontejneru pomocí tohoto volání rozhraní API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Další kroky

+ Přečtěte si o všech [možnostech nasazení pro Azure Machine Learning](how-to-deploy-and-where.md).
+ Přečtěte si, jak [vytvořit klienty pro webovou službu](how-to-consume-web-service.md).
+  [Proveďte předpovědi na velké množství dat](how-to-use-parallel-run-step.md) asynchronně.
+ Monitorujte své modely Azure Machine Learning pomocí [Application Insights](how-to-enable-app-insights.md).
+ Vyzkoušejte kurz [automatického výběru algoritmů.](tutorial-auto-train-models.md) 
