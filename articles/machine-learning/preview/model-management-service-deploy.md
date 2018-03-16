---
title: "Azure Machine Learning nasazení modelu správy webové služby | Microsoft Docs"
description: "Tento dokument popisuje kroky k nasazení modelu strojového učení pomocí Azure Machine Learning modelu správy."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 3d9446eaad4aaeb9de9600642d88a1f8179c4255
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Nasazení modelu strojového učení jako webovou službu

Azure Machine Learning modelu Management poskytuje rozhraní pro nasazení modely jako kontejnerizované na základě Docker webové služby. Můžete nasadit modely, které vytvoříte pomocí rozhraní, jako je například Spark, Microsoft kognitivní Toolkit (CNTK), Keras, Tensorflow a Python. 

Tento dokument popisuje postup nasazení modely jako webové služby pomocí rozhraní příkazového řádku (CLI) Azure Machine Learning modelu správy.

## <a name="deploying-web-services"></a>Nasazení webové služby
Pomocí CLIs, můžete nasadit webové služby pro spuštění v místním počítači nebo na clusteru.

Doporučujeme začít s místní nasazení. Nejprve ověřit, že model a kód fungovat, pak nasazení webové služby do clusteru s podporou pro produkční škálování použití. Další informace o nastavení prostředí pro nasazení clusteru najdete v tématu [Model správy konfigurace](deployment-setup-configuration.md). 

Tady jsou kroky nasazení:
1. Pomocí modelu Machine Learning uložené, vyškolení,
2. Vytvořte schéma pro webové služby vstupní a výstupní data
3. Vytvoření bitové kopie založené na Docker kontejneru
4. Vytvoření a nasazení webové služby

### <a name="1-save-your-model"></a>1. Uložit modelu
Začněte u souboru uložené trained model, například mymodel.pkl. Přípona souboru se liší podle platformy, které použijete k natrénování a model uložte. 

Například můžete použít jazyka Python okurky knihovnu modulu trained model uložit do souboru. Tady je [příklad](http://scikit-learn.org/stable/modules/model_persistence.html) použití Iris datové sady:

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
Tento krok je volitelný. 

Vytvořte schéma automaticky ověření vstupní a výstupní webové služby. CLIs taky použití schématu pro generování dokumentu Swagger pro webovou službu.

Pokud chcete vytvořit schéma, importujte následující knihovny:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
V dalším kroku definujte vstupní proměnné například Spark dataframe, Pandas dataframe nebo NumPy pole. Následující příklad používá Numpy pole:

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

### <a name="3-create-a-scorepy-file"></a>3. Vytvořte soubor score.py
Poskytnete score.py souboru, který načte modelu a vrátí výsledek předpovědi pomocí modelu.

Soubor musí obsahovat dvě funkce: init a spustit.

Přidejte následující kód v horní části souboru score.py povolit funkci shromažďování dat, která pomáhá shromažďovat data vstup a předpovědi modelu

```python
from azureml.datacollector import ModelDataCollector
```

Zkontrolujte [modelu shromažďování dat](how-to-use-model-data-collection.md) další podrobnosti o tom, jak tuto funkci používat.

#### <a name="init-function"></a>Init – funkce
Pomocí funkce init načíst uložené model.

Příklad jednoduchého init – funkce načítání modelu:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Run – funkce
Spuštění funkce používá k vrácení předpovědi v modelu a vstupní data.

Příklad jednoduchou spuštění funkce zpracování vstupu a vrátit výsledek předpovědi:

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

### <a name="4-register-a-model"></a>4. Zaregistrovat modelu
Následující příkaz slouží k registraci model vytvořený v kroku 1 výše,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Vytvoření manifestu
Následující příkaz vám pomůže vytvořit manifest pro model

```
az ml manifest create --manifest-name [your new manifest name] -f [path to score file] -r [runtime for the image, e.g. spark-py]
```
Model dříve zaregistrovaný můžete přidat k manifestu pomocí argumentu `--model-id` nebo `-i` ve výše uvedeném příkazu. S další lze zadat více modelů -i argumenty.

### <a name="6-create-an-image"></a>6. Vytvoření image 
Můžete vytvořit bitovou kopii s možností obsahující vytvořené před jeho manifestu. 

```
az ml image create -n [image name] --manifest-id [the manifest ID]
```

Nebo můžete vytvořit manifest a bitové kopie s jeden příkaz. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Další informace o parametrech příkazového typ -h na konci příkazu například bitové kopie ml az vytvořit -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Vytvoření a nasazení webové služby
Nasazení služby pomocí následujícího příkazu:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Také můžete jeden příkaz k provedení předchozí kroky 4. Použití -h službou vytvořit příkaz pro další podrobnosti.

### <a name="8-test-the-service"></a>8. Testování služby
Chcete-li získat informace o tom, jak volat služby použijte následující příkaz:

```
az ml service usage realtime -i <service id>
```

Volání služby pomocí funkce spuštění z příkazového řádku:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

V následujícím příkladu volání ukázku Iris webové služby:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Další postup
Teď, když testování k webové službě pro spustit místně, můžete nasadit do clusteru s podporou pro použití ve velkém měřítku. Podrobnosti o nastavení clusteru s podporou pro nasazení webu služby najdete v tématu [modelu Management Configuration](deployment-setup-configuration.md). 
