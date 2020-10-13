---
title: Použití a nasazení stávajících modelů
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak přenést místně vyškolené modely ML do cloudu Azure pomocí Azure Machine Learning.  Můžete registrovat modely vytvořené mimo Azure Machine Learning a pak je nasadit jako webovou službu nebo modul Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 64180320ab57996984f5e886639dfd4977ae5e7c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999064"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Nasazení stávajícího modelu pomocí Azure Machine Learning


V tomto článku se dozvíte, jak zaregistrovat a nasadit model strojového učení, který jste naučili mimo Azure Machine Learning. Můžete nasadit jako webovou službu nebo do zařízení IoT Edge.  Po nasazení můžete monitorovat model a detekovat posun dat Azure Machine Learning. 

Další informace o konceptech a pojmech v tomto článku najdete v tématu [Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

* [Pracovní prostor Azure Machine Learning](how-to-manage-workspace.md)
  + V příkladech Pythonu se předpokládá, že `ws` je proměnná nastavená na váš pracovní prostor Azure Machine Learning. Další informace o tom, jak se připojit k pracovnímu prostoru, najdete v [dokumentaci k sadě Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace).
  
  + Příklady rozhraní příkazového řádku používají zástupné symboly `myworkspace` a `myresourcegroup` , které byste měli nahradit názvem vašeho pracovního prostoru a skupinou prostředků, která ho obsahuje.

* [Sada SDK Azure Machine Learning Pythonu](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).  

* Rozšíření rozhraní příkazového řádku [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) a [Machine Learning CLI](reference-azure-machine-learning-cli.md).

* Školený model. Model musí být trvale uložený na jeden nebo více souborů ve vývojovém prostředí. <br><br>V příkladu kódu v tomto článku se předvádí registrace vyškolených modelů, které používají modely z [projektu Paolo Ripamonti pro analýzu mínění pro Twitter](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrovat model (y)

Registrace modelu umožňuje ukládat, uchovávat verze a sledovat metadata o modelech v pracovním prostoru. V následujících příkladech Pythonu a CLI `models` obsahuje adresář `model.h5` `model.w2v` soubory,, `encoder.pkl` a `tokenizer.pkl` . Tento příklad nahraje soubory obsažené v `models` adresáři jako novou registraci modelu s názvem `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Další informace naleznete v tématu [model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) reference.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Můžete také nastavit objekty Add `tags` a `properties` Dictionary na registrovaný model. Tyto hodnoty můžete použít později, abyste mohli identifikovat konkrétní model. Například použitý rámec, parametry školení atd.

Další informace najdete v referenčních informacích k [registru AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register) .


Další informace o registraci modelů obecně najdete v tématu [Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definovat odvozenou konfiguraci

Odvozená konfigurace definuje prostředí používané pro spuštění nasazeného modelu. Konfigurace odvození odkazuje na následující entity, které se používají ke spuštění modelu při jeho nasazení:

* Vstupní skript s názvem `score.py` načte model při spuštění nasazené služby. Tento skript je zodpovědný taky za příjem dat, předávání do modelu a vrácení odpovědi.
* Azure Machine Learning [prostředí](how-to-use-environments.md). Prostředí definuje závislosti softwaru potřebné ke spuštění modelu a vstupního skriptu.

Následující příklad ukazuje, jak pomocí sady SDK vytvořit prostředí a pak ho použít s konfigurací odvození:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Další informace najdete v následujících článcích:

+ [Jak používat prostředí](how-to-use-environments.md).
+ Odkaz na [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true)


Rozhraní příkazového řádku načte konfiguraci odvození ze souboru YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

V rozhraní příkazového řádku je prostředí conda definované v souboru, na `myenv.yml` který odkazuje konfigurace odvození. Následující YAML je obsahem tohoto souboru:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Vstupní skript (score.py)

Vstupní skript má pouze dvě požadované funkce `init()` a `run(data)` . Tyto funkce se používají k inicializaci služby při spuštění a spuštění modelu pomocí dat požadavku předaných klientem. Zbytek skriptu zpracovává načítání a spouštění modelů.

> [!IMPORTANT]
> Není k dispozici žádný skript obecného záznamu, který funguje pro všechny modely. Vždy je specifické pro model, který se používá. Musí pochopit, jak načíst model, datový formát, který model očekává, a jak určit skóre dat pomocí modelu.

Následující kód Pythonu je příkladem skriptu pro vstup ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Další informace o vstupních skriptech najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definovat nasazení

Balíček [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py&preserve-view=true) obsahuje třídy používané pro nasazení. Třída, kterou použijete, určuje, kde je model nasazen. Například pro nasazení jako webové služby ve službě Azure Kubernetes použijte k vytvoření konfigurace nasazení [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) .

Následující kód Pythonu definuje konfiguraci nasazení pro místní nasazení. Tato konfigurace nasadí model jako webovou službu do místního počítače.

> [!IMPORTANT]
> Místní nasazení vyžaduje funkční instalaci [Docker](https://www.docker.com/) v místním počítači:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Další informace naleznete v odkazu [LocalWebservice.deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-port-none-) .

Rozhraní příkazového řádku načte konfiguraci nasazení ze souboru YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Nasazení do jiného cílového výpočetního prostředí, jako je Azure Kubernetes Service v cloudu Azure, je stejně snadné jako změna konfigurace nasazení. Další informace najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Nasazení modelu

Následující příklad načte informace o registrovaném modelu s názvem `sentiment` a poté jej nasadí jako službu s názvem `sentiment` . Během nasazování se k vytvoření a konfiguraci prostředí služby používá konfigurace odvození konfigurace a nasazení:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Další informace naleznete v tématu [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) reference.

Chcete-li model nasadit z rozhraní příkazového řádku, použijte následující příkaz. Tento příkaz nasadí verzi 1 registrovaného modelu ( `sentiment:1` ) pomocí odvození a konfigurace nasazení uložené v `inferenceConfig.json` `deploymentConfig.json` souborech a:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) .

Další informace o nasazení najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Spotřeba požadavků a odpovědí

Po nasazení se zobrazí identifikátor URI pro vyhodnocování. Tento identifikátor URI můžou klienti použít k odesílání požadavků službě. V následujícím příkladu je jednoduchý klient Pythonu, který odesílá data do služby a zobrazuje odpověď:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Další informace o tom, jak využít nasazenou službu, najdete v tématu [Vytvoření klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Další kroky

* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Postup vytvoření klienta pro nasazený model](how-to-consume-web-service.md)
