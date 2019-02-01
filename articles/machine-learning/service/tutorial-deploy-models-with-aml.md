---
title: 'Kurz klasifikace obrázků: Modely nasazení'
titleSuffix: Azure Machine Learning service
description: Tento kurz ukazuje, jak pomocí služby Azure Machine Learning nasadit model klasifikace obrázků s využitím scikit-learn v poznámkovém bloku Python Jupyter. Tento kurz je druhá dvě série.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 01/29/2019
ms.custom: seodec18
ms.openlocfilehash: 167cc390fb9cc28f4249d168e452825b37902723
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510421"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Kurz: Nasadit model klasifikace obrázků ve službě Azure Container Instances

Tento kurz je **druhou částí z dvoudílné série kurzů**. V [předchozím kurzu](tutorial-train-models-with-aml.md) jste trénovali modely strojového učení a pak jste zaregistrovali model ve vašem pracovním prostoru v cloudu.  

Nyní jste připraveni tento model nasadíme jako webovou službu v [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Webová služba je image, v tomto případě image Dockeru. Zapouzdřuje bodovací logiku a samotného modelu. 

V této části kurzu se pomocí služby Azure Machine Learning pro následující úlohy:

> [!div class="checklist"]
> * Nastavte své testovací prostředí.
> * Načtení z pracovního prostoru modelu.
> * Otestování modelu místně.
> * Nasazení modelu do služby Container Instances.
> * Otestujte nasazeného modelu.

Container Instances je skvělým řešením pro testování a pochopení pracovního postupu. Pro nasazení v produkčním prostředí škálovatelné zvažte použití služby Azure Kubernetes Service. Další informace najdete v tématu [nasazení a kde](how-to-deploy-and-where.md).

>[!NOTE]
> S využitím Azure Machine Learning SDK verze 1.0.8 testovaný kód v tomto článku.

## <a name="prerequisites"></a>Požadavky
Přejděte k [nastavit vývojové prostředí](#start) číst kroky poznámkového bloku.  

Poznámkový blok spustit, nejdřív dokončit cvičení modelu v [kurzu (část 1): Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md).   Spusťte **kurzy/img – klasifikace – část2 deploy.ipynb** poznámkového bloku pomocí stejné server poznámkového bloku.


## <a name="start"></a>Nastavení prostředí

Začněte tím, že nastavíte testovací prostředí.

### <a name="import-packages"></a>Import balíčků

Importujte balíčky Pythonu pro tento kurz potřeba:

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

V předchozím kurzu jste zaregistrovali model ve vašem pracovním prostoru. Teď načíst tento pracovní prostor a stahovat modelu do místního adresáře:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os 
ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Místní testování modelu

Před nasazením, ujistěte se, že váš model funguje místně:
* Data zátěžového testu.
* Předpověď testovací data.
* Zkontrolujte chybovou matici.

### <a name="load-test-data"></a>Načtení testovacích dat

Načíst testovací data z **. /data/** adresář vytvořený během kurzu školení:

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)
```

### <a name="predict-test-data"></a>Předpovídání testovacích dat

Chcete-li získat predikcí, informačního kanálu testovací datové modelu:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>Prozkoumání chybové matice

Vygenerujte chybovou matici, abyste zjistili, kolik vzorků z testovací sady má správnou klasifikaci. Všimněte si, že chybně klasifikované hodnota nesprávné předpovědí: 

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
   

Pomocí knihovny `matplotlib` zobrazíte chybovou matici jako graf. V tomto grafu zobrazuje skutečné hodnoty osy x a osy y zobrazí predikované hodnoty. Zobrazí barva v každé mřížce míra chyb. Čím světlejší je barva, tím vyšší je chybovost. Například mnoho 5 jsou chybně klasifikované jako od 3. Zobrazí se tak jasně mřížky v (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
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

![Diagram znázorňující chybovou matici](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Nasadit jako webovou službu

Po testování modelu a budete spokojeni s výsledky, tento model nasadíme jako webovou službu hostované ve službě Container Instances. 

K vytvoření správné prostředí pro Container Instances, obsahují následující součásti:
* Hodnoticí skript, který ukazují, jak použít model.
* Prostředí soubor, který chcete zobrazit, co musíte balíčky nainstalovat.
* Konfigurační soubor k vytvoření instance kontejneru.
* Model, který jste dříve školení.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Vytvoření hodnoticího skriptu

Vytváření hodnoticí skript volá **score.py**. Volání webové služby používá tento skript k ukazují, jak použít model.

Zahrnout tyto dvě požadované funkce hodnoticí skript:
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

Dále vytvořte souboru prostředí s názvem **myenv.yml**, všechny závislé součásti balíčku skript, který určuje. Tento soubor se používá, abyste měli jistotu, že všechny tyto závislosti nainstalují do image Dockeru. Tento model musí `scikit-learn` a `azureml-sdk`:

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
Zkontrolujte obsah `myenv.yml` souboru:

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Vytvoření konfiguračního souboru

Vytvořte konfigurační soubor nasazení. Zadejte počet procesorů a GB paměti RAM potřebné pro váš kontejner Container Instances. Přestože je závislé na váš model, výchozí jedno jádro a 1 GB paměti RAM je dostačující pro mnoho modelů. Pokud potřebujete další rozšíření později, budete muset znovu vytvořte image a službu znovu nasaďte.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Nasazení ve službě Container Instances
Odhadovaný čas na dokončení nasazení je **asi sedm až osm minut**.

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Sestavte image pomocí těchto souborů:
   * Soubor vyhodnocení `score.py`.
   * Soubor prostředí `myenv.yml`.
   * Soubor modelu.
1. Registrace na obrázku v části pracovní prostor. 
1. Odeslat image do kontejneru Container Instances.
1. Spuštění kontejneru ve službě Container Instances pomocí bitové kopie.
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

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod můžete sdílet s kýmkoli, kdo chce testovat webovou službu nebo ji integrovat do aplikace: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>Testování nasazené službě

Dříve skóre všechna data testu v místní verzi modelu. Nyní můžete otestovat nasazeného modelu pomocí náhodného vzorku 30 imagí v testovacích datech.  

Následující kód provede tyto kroky:
1. Odesílání dat jako JSON pole k webové službě hostované ve službě Container Instances. 

1. Použití rozhraní `run` API sady SDK k vyvolání služby. Můžete také provést nezpracovaná volání pomocí libovolného nástroje protokolu HTTP, jako **curl**.

1. Tisk vrácených předpovědí a jejich vykreslení spolu se vstupními obrázky. Abyste měli na očích chybně klasifikované ukázky se používá červeně a inverzní obrázku na černou, bílou. 

Protože je vysoká přesnost modelu, budete muset několikrát spusťte následující kód, než uvidíte chybně klasifikované ukázku:

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
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Tento výsledek je z jednoho náhodného vzorku imagím pro testování:

![Obrázek znázorňující výsledky](./media/tutorial-deploy-models-with-aml/results.png)

Můžete také odeslat nezpracovaná požadavku HTTP k otestování webové služby:

```python
import requests

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

Pro skupinu prostředků a pracovní prostor pro další kurzy a zkoumání, můžete odstranit pouze nasazení Container Instances pomocí toto volání rozhraní API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Další postup

+ Přečtěte si o všech [možnosti nasazení pro službu Azure Machine Learning](how-to-deploy-and-where.md).
+ Zjistěte, jak [vytvoření klientů pro webovou službu](how-to-consume-web-service.md).
+  [Vytvoření predikcí velkým objemům dat.](how-to-run-batch-predictions.md) asynchronně.
+ Monitorování vašich modelů Azure Machine Learning s [Application Insights](how-to-enable-app-insights.md).
+ Vyzkoušejte si [automatické algoritmus výběru](tutorial-auto-train-models.md) kurzu. 