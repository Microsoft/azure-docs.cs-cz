---
title: Azure Machine Learning nasazení modelu správy webové služby | Dokumentace Microsoftu
description: Tento dokument popisuje kroky při nasazení modelu strojového učení pomocí Azure Machine Learning model správy.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2a486da73d00ddaebee5c1fe32fc6112c177f01e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992333"
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Nasadit Model strojového učení jako webové služby

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Správa modelů Azure Machine Learning poskytuje rozhraní pro nasazení modelů jako kontejnerizovaných webových služeb využívajících Docker. Můžete nasadit modely vytvořené pomocí architektury, jako jsou Spark, Microsoft Cognitive Toolkit (CNTK), Keras, Tensorflow a Python. 

Tento dokument popisuje kroky pro nasazení modelů jako webové služby pomocí rozhraní příkazového řádku správy modelů Azure Machine Learning (rozhraní příkazového řádku).

## <a name="what-you-need-to-get-started"></a>Co potřebujete, abyste mohli začít

Pokud chcete naplno využít této příručce, byste měli mít přístup přispěvatele k předplatnému Azure nebo skupinu prostředků, kterou můžete nasadit vlastní modely a.
Rozhraní příkazového řádku je předem nainstalovaný na aplikaci Azure Machine Learning Workbench a v [Azure datové](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview).  Můžete ho taky nainstalovat jako samostatný balíček.

Kromě toho prostředí účet a nasazení modelu správy musí již být nastavení.  Další informace o nastavení vašeho účtu služby Správa modelů a prostředí pro místní a nasazení clusteru najdete v tématu [konfigurace správy modelů](deployment-setup-configuration.md).

## <a name="deploying-web-services"></a>Nasazování webových služeb
Rozhraní příkazového řádku můžete nasadit webové služby ke spuštění v místním počítači nebo v clusteru.

Doporučujeme začít se místní nasazení. Nejprve ověřte, že váš model a kód fungovat, pak nasadit také webovou službu do clusteru pro použití v produkčním měřítku.

Tady jsou kroky nasazení:
1. Použít uložený, trénované, model Machine Learning
2. Vytvořte schéma pro webovou službu vstupní a výstupní data
3. Vytvoření image kontejneru dockeru
4. Vytvoření a nasazení webové služby

### <a name="1-save-your-model"></a>1. Uložit model
Začněte s souboru uloženého trénovaného modelu, například mymodel.pkl. Přípona souboru se liší podle platformy, na kterou použijete k natrénování a uložit model. 

Jako příklad slouží Pickle knihovna Python k uložení naučeného modelu do souboru. Tady je [příklad](http://scikit-learn.org/stable/modules/model_persistence.html) datovou sadu Iris pomocí:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Vytvořte soubor schema.json

Při generování schématu je volitelný, důrazně doporučujeme k definování proměnné formát požadavku a vstup pro lepší zpracování.

Vytvořte schéma automaticky ověřit vstup a výstup webové služby. Rozhraní příkazového řádku také použití schématu pro generování dokumentu Swagger pro webovou službu.

Pokud chcete vytvořit schéma, importujte následující knihovny:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Dále definujte vstupních proměnných, například Spark dataframe, Pandas dataframe nebo NumPy pole. Následující příklad používá Numpy pole:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```
Následující příklad používá Spark dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

Následující příklad používá PANDAS dataframe:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

Obecný formát JSON v následujícím příkladu:

```python
inputs = {"input_json": SampleDefinition(DataTypes.STANDARD, yourinputjson)}
generate_schema(run_func=run, inputs=inputs, filepath='./outputs/service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Vytvořte soubor score.py
Zadáte-score.py soubor, který načte modelu a vrátí výsledky předpovědí pomocí modelu.

Soubor musí obsahovat dvě funkce: init a spustit.

Přidejte následující kód v horní části souboru score.py povolit funkci shromažďování dat, která pomáhá shromažďovat data vstupu a předpovědí modelu

```python
from azureml.datacollector import ModelDataCollector
```

Zkontrolujte [shromažďování dat modelů](how-to-use-model-data-collection.md) části Podrobné informace o tom, jak tuto funkci používat.

#### <a name="init-function"></a>Init – funkce
Pomocí funkce init můžete načíst uložené modelu.

Příklad jednoduché init funkce načítání modelu:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Run – funkce
Spuštění funkce používá k vrácení předpověď modelu a vstupní data.

Příklad jednoduchou funkci zpracování vstupu a vrátit výsledek předpovědi spustit:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Zaregistrujte model
Následující příkaz se použije k registraci modelu vytvořili v kroku 1 výše

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Vytvoření manifestu
Následující příkaz vám pomůže vytvořit manifest pro model

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Dříve registrovaného modelu můžete přidat do manifestu pomocí argumentu `--model-id` nebo `-i` ve výše uvedeném příkazu. Více modelů je možné zadat při další -i argumenty.

### <a name="6-create-an-image"></a>6. Vytvoření image 
Můžete vytvořit bitovou kopii s možností máte vytvořený manifestu před. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

>[!NOTE] 
>Můžete také jediný příkaz k provedení registrace, manifest a model vytváření modelu. Použití -h ve službě vytvořit příkaz pro další podrobnosti.

Jako alternativu, je jediným příkazem zaregistrujte model, vytvoření manifestu a vytvořit bitovou kopii (ale není vytvořit a nasadit také webovou službu ještě) jako jeden krok následujícím způsobem.

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime e.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Podrobné informace o parametrech příkazového typ -h na konci příkazu například az ml image vytvořte -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Vytvoření a nasazení webové služby
Nasazení služby pomocí následujícího příkazu:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Jediný příkaz můžete také provádět všechny předchozí kroky 4. Použití -h ve službě vytvořit příkaz pro další podrobnosti.

Jako alternativu je jediným příkazem zaregistrujte model, vytvoření manifestu, vytvoření bitové kopie, a také vytvoření a nasazení webové služby, jako jeden krok následujícím způsobem.

```azurecli
az ml service create realtime --model-file [model file/folder path] -f [scoring file e.g. score.py] -n [your service name] -s [schema file e.g. service_schema.json] -r [runtime for the Docker container e.g. spark-py or python] -c [conda dependencies file for additional python packages]
```


### <a name="8-test-the-service"></a>8. Pokud chcete službu otestovat
Chcete-li získat informace o tom, jak volat služby použijte následující příkaz:

```
az ml service usage realtime -i <service id>
```

Volání služby pomocí funkce spuštění z příkazového řádku:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

Následující příklad volá ukázku Iris webové služby:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Další postup
Teď, když jste otestovali webovou službu spustit místně, můžete ho nasadíte na clusteru pro použití ve velkém měřítku. Podrobnosti o nastavení clusteru pro nasazení webové služby najdete v tématu [konfigurace správy modelu](deployment-setup-configuration.md). 
