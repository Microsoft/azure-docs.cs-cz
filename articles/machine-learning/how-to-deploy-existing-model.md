---
title: Použití a nasazení stávajících modelů
titleSuffix: Azure Machine Learning
description: Naučte se, jak můžete použít Azure Machine Learning s modely, které byly vyškoleny mimo službu. Můžete registrovat modely vytvořené mimo Azure Machine Learning a pak je nasadit jako webovou službu nebo modul Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: d8f0f6c63c584cacfa5c996bd541ce4dfc4bd289
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763925"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Použít existující model s Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se používat existující model strojového učení s Azure Machine Learning.

Pokud máte model strojového učení, který byl vyškolený mimo Azure Machine Learning, můžete tuto službu i nadále používat k nasazení modelu jako webové služby nebo do IoT Edge zařízení. 

> [!TIP]
> Tento článek poskytuje základní informace o registraci a nasazení existujícího modelu. Po nasazení Azure Machine Learning poskytuje monitorování pro váš model. Umožňuje také ukládat vstupní data odesílaná do nasazení, která lze použít pro analýzu posunu dat nebo pro nové verze modelu.
>
> Další informace o konceptech a pojmech, které se tady používají, najdete v tématu [Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).
>
> Obecné informace o procesu nasazení najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru](how-to-manage-workspace.md).

    > [!TIP]
    > V příkladech Pythonu v tomto článku se předpokládá, že je proměnná `ws` nastavená na pracovní prostor Azure Machine Learning.
    >
    > Příklady rozhraní příkazového řádku používají zástupný text `myworkspace` a `myresourcegroup`. Nahraďte názvem vašeho pracovního prostoru a skupinou prostředků, která ho obsahuje.

* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Rozšíření rozhraní příkazového řádku [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a [Machine Learning CLI](reference-azure-machine-learning-cli.md).

* Školený model. Model musí být trvale uložený na jeden nebo více souborů ve vývojovém prostředí.

    > [!NOTE]
    > Ukázkové fragmenty kódu v tomto článku vám poukazují, jak se registruje model vyškolený mimo Azure Machine Learning v tomto článku se používají modely vytvořené pomocí projektu Twitter mínění Analysis Ripamonti pro: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrovat model (y)

Registrace modelu umožňuje ukládat, uchovávat verze a sledovat metadata o modelech v pracovním prostoru. V následujících příkladech Pythonu a CLI `models` adresář obsahuje soubory `model.h5`, `model.w2v`, `encoder.pkl`a `tokenizer.pkl`. Tento příklad nahraje soubory obsažené v adresáři `models` jako novou registraci modelu s názvem `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Další informace naleznete v tématu [model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) reference.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Další informace najdete v referenčních informacích k [registru AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) .


Další informace o registraci modelů obecně najdete v tématu [Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definovat odvozenou konfiguraci

Odvozená konfigurace definuje prostředí používané pro spuštění nasazeného modelu. Konfigurace odvození odkazuje na následující entity, které se používají ke spuštění modelu při jeho nasazení:

* Vstupní skript. Tento soubor (s názvem `score.py`) načte model při spuštění nasazené služby. Zodpovídá taky za příjem dat, předávání do modelu a vrácení odpovědi.
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

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Další informace najdete v těchto článcích:

+ [Jak používat prostředí](how-to-use-environments.md).
+ Odkaz na [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)


Rozhraní příkazového řádku načte konfiguraci odvození ze souboru YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

V rozhraní příkazového řádku je prostředí conda definované v souboru `myenv.yml`, na který odkazuje konfigurace odvození. Následující YAML je obsahem tohoto souboru:

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
```

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Vstupní skript

Vstupní skript má pouze dvě požadované funkce `init()` a `run(data)`. Tyto funkce se používají k inicializaci služby při spuštění a spuštění modelu pomocí dat požadavku předaných klientem. Zbytek skriptu zpracovává načítání a spouštění modelů.

> [!IMPORTANT]
> Není k dispozici žádný skript obecného záznamu, který funguje pro všechny modely. Vždy je specifické pro model, který se používá. Musí pochopit, jak načíst model, datový formát, který model očekává, a jak určit skóre dat pomocí modelu.

Následující kód Pythonu je ukázkový skript pro vkládání (`score.py`):

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

Balíček [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) obsahuje třídy používané pro nasazení. Třída, kterou použijete, určuje, kde je model nasazen. Například pro nasazení jako webové služby ve službě Azure Kubernetes použijte [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) a vytvořte konfiguraci nasazení.

Následující kód Pythonu definuje konfiguraci nasazení pro místní nasazení. Tato konfigurace nasadí model jako webovou službu do místního počítače.

> [!IMPORTANT]
> Místní nasazení vyžaduje funkční instalaci [Docker](https://www.docker.com/) v místním počítači:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Další informace naleznete v tématu [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) reference.

Rozhraní příkazového řádku načte konfiguraci nasazení ze souboru YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Nasazení do jiného cílového výpočetního prostředí, jako je Azure Kubernetes Service v cloudu Azure, je stejně snadné jako změna konfigurace nasazení. Další informace najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Nasazení modelu

Následující příklad načte informace o registrovaném modelu s názvem `sentiment`a poté jej nasadí jako službu s názvem `sentiment`. Během nasazování se k vytvoření a konfiguraci prostředí služby používá konfigurace odvození konfigurace a nasazení:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Další informace naleznete v tématu [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) reference.

Chcete-li model nasadit z rozhraní příkazového řádku, použijte následující příkaz. Tento příkaz nasadí verzi 1 registrovaného modelu (`sentiment:1`) pomocí odvození a konfigurace nasazení uložené v souborech `inferenceConfig.json` a `deploymentConfig.json`:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

Další informace o nasazení najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Spotřeba požadavků a odpovědí

Po nasazení se zobrazí identifikátor URI pro vyhodnocování. Tento identifikátor URI můžou klienti použít k odesílání požadavků službě. Následující příklad je základní klient Pythonu, který odesílá data do služby a zobrazuje odpověď:

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
* [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Postup vytvoření klienta pro nasazený model](how-to-consume-web-service.md)
