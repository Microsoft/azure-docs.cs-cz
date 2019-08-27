---
title: 'Kurz k klasifikaci obrázků: Modely nasazení'
titleSuffix: Azure Machine Learning service
description: Tento kurz ukazuje, jak pomocí služby Azure Machine Learning nasadit model klasifikace obrázků s využitím scikit-learn v poznámkovém bloku Python Jupyter. Tento kurz je druhým z řad se dvěma částmi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 57d34bb170c0ff86f3d3c42a25184d8af71c0270
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036206"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Kurz: Nasazení modelu klasifikace imagí v Azure Container Instances

Tento kurz je **druhou částí z dvoudílné série kurzů**. V [předchozím kurzu](tutorial-train-models-with-aml.md) jste trénovali modely strojového učení a pak jste zaregistrovali model ve vašem pracovním prostoru v cloudu.  

Nyní jste připraveni model nasadit jako webovou službu v [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Webová služba je image, v tomto případě image Docker. Zapouzdřuje logiku bodování a model sám sebe. 

V této části kurzu používáte službu Azure Machine Learning pro následující úlohy:

> [!div class="checklist"]
> * Nastavte testovací prostředí.
> * Načtěte model z pracovního prostoru.
> * Otestujte model místně.
> * Nasaďte model do Container Instances.
> * Otestujte nasazený model.

Container Instances je skvělé řešení pro testování a porozumění pracovnímu postupu. Pro nasazení v produkčním prostředí škálovatelné zvažte použití služby Azure Kubernetes Service. Další informace naleznete v tématu [Jak nasadit a kde](how-to-deploy-and-where.md).

>[!NOTE]
> Kód v tomto článku byl testován pomocí sady Azure Machine Learning SDK 1.0.41 verze.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit Poznámkový blok, nejdřív dokončete [výuku modelu v kurzu (část 1): Výukový model](tutorial-train-models-with-aml.md)klasifikace image   Pak otevřete Poznámkový blok **kurzy/img-Classification-part2-Deploy. ipynb** pomocí stejného serveru poznámkového bloku.

Tento kurz je také k dispozici na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , pokud ho chcete použít ve svém vlastním [místním prostředí](how-to-configure-environment.md#local).  Ujistěte se, že máte `matplotlib` nainstalované `scikit-learn` a ve vašem prostředí. 

## <a name="start"></a>Nastavení prostředí

Začněte tím, že nastavíte testovací prostředí.

### <a name="import-packages"></a>Import balíčků

Importujte balíčky Pythonu potřebné pro tento kurz:

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

V předchozím kurzu jste zaregistrovali model ve vašem pracovním prostoru. Nyní načtěte tento pracovní prostor a Stáhněte model do svého místního adresáře:


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

Před nasazením se ujistěte, že model pracuje místně:
* Data zátěžového testu.
* Předpověď dat testu.
* Projděte si matrici zmatení.

### <a name="load-test-data"></a>Načtení testovacích dat

Načtěte testovací data z adresáře **./data/** vytvořeného během výukového kurzu:

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

Pokud chcete získat předpovědi, pofeedte testovací datovou sadu do modelu:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Prozkoumání chybové matice

Vygenerujte chybovou matici, abyste zjistili, kolik vzorků z testovací sady má správnou klasifikaci. Všimněte si chybné klasifikované hodnoty pro nesprávné předpovědi: 

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
   

Pomocí knihovny `matplotlib` zobrazíte chybovou matici jako graf. V tomto grafu zobrazuje osa x skutečné hodnoty a osa y zobrazuje předpovězené hodnoty. Barva v každé mřížce zobrazuje míru chyb. Čím světlejší je barva, tím vyšší je chybovost. Například mnoho 5 je nesprávně klasifikované jako 3. Zobrazí se Světlá mřížka na (5, 3):

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

## <a name="deploy-as-a-web-service"></a>Nasazení jako webové služby

Po otestování modelu a nespokojenosti s výsledky nasaďte model jako webovou službu hostovanou v Container Instances. 

Chcete-li vytvořit správné prostředí pro Container Instances, zadejte následující komponenty:
* Hodnoticí skript pro zobrazení způsobu použití modelu.
* Soubor prostředí, ve kterém se zobrazují balíčky, které je potřeba nainstalovat
* Konfigurační soubor pro sestavení instance kontejneru.
* Model, který jste si už v minulosti promluvili.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Vytvoření hodnoticího skriptu

Vytvořte skript bodování nazvaný **Score.py**. Volání webové služby používá tento skript k zobrazení způsobu použití modelu.

Zahrňte tyto dvě požadované funkce do skriptu bodování:
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
    model_path = Model.get_model_path('sklearn_mnist')
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

Dále vytvořte soubor prostředí s názvem **MyENV. yml**, který určuje všechny závislosti balíčků daného skriptu. Tento soubor se používá k tomu, aby se zajistilo, že jsou všechny tyto závislosti nainstalované v imagi Docker. Tento model potřebuje `scikit-learn` a `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Zkontrolujte obsah `myenv.yml` souboru:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Vytvoření konfiguračního souboru

Vytvořte konfigurační soubor nasazení. Zadejte počet procesorů a gigabajtů paměti RAM potřebných pro váš Container Instances kontejner. I když to závisí na modelu, je pro mnoho modelů dostačující výchozí hodnota jednoho jádra a 1 GB paměti RAM. Pokud budete potřebovat později, budete muset image znovu vytvořit a službu znovu nasadit.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Nasadit v Container Instances
Odhadovaná doba k dokončení nasazení je **přibližně sedm až osm minut**.

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Sestavte Image pomocí těchto souborů:
   * Soubor bodování, `score.py`.
   * Soubor prostředí, `myenv.yml`.
   * Soubor modelu.
1. Zaregistrujte image v pracovním prostoru. 
1. Odeslat obrázek do kontejneru Container Instances.
1. Spusťte kontejner v Container Instances pomocí Image.
1. Získání koncového bodu HTTP webové služby


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod můžete sdílet s kýmkoli, kdo chce Testovat webovou službu nebo ji integrovat do aplikace: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Otestování nasazené služby

Dříve jste ve výsledku nastavili všechna testovací data pomocí místní verze modelu. Nyní můžete otestovat nasazený model s náhodným vzorkem 30 imagí z testovacích dat.  

Následující kód provede tyto kroky:
1. Odešlete data do webové služby hostované v Container Instances jako pole JSON. 

1. Použití rozhraní `run` API sady SDK k vyvolání služby. Nezpracované volání můžete také provádět pomocí libovolného nástroje HTTP, jako je například **kudrlinkou**.

1. Tisk vrácených předpovědí a jejich vykreslení spolu se vstupními obrázky. Červené písmo a inverzní obrázek, bílá na černé, slouží k zvýraznění chybně klasifikovaných vzorků. 

Vzhledem k tomu, že je přesnost modelu vysoká, může být nutné spustit následující kód několikrát, než uvidíte nechybně klasifikovanou ukázku:

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

Tento výsledek je z jedné náhodné ukázky testovacích imagí:

![Obrázek znázorňující výsledky](./media/tutorial-deploy-models-with-aml/results.png)

Můžete také odeslat nezpracovaný požadavek HTTP na testování webové služby:

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

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Další kroky

+ Přečtěte si o všech [možnostech nasazení Azure Machine Learning služby](how-to-deploy-and-where.md).
+ Naučte se [vytvářet klienty pro webovou službu](how-to-consume-web-service.md).
+  [Provádějte asynchronní předpovědi velké množství dat](how-to-run-batch-predictions.md) .
+ Monitorujte Azure Machine Learning modely pomocí [Application Insights](how-to-enable-app-insights.md).
+ Vyzkoušejte kurz pro [Automatický výběr algoritmu](tutorial-auto-train-models.md) . 
