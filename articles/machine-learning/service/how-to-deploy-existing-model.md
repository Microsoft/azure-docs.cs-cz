---
title: Jak používat existující model
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak můžete pomocí služby Azure Machine Learning s modely, které byly školení mimo službu. Můžete zaregistrovat modelů vytvořených mimo službu Azure Machine Learning a potom je můžete nasadit jako webovou službu nebo modulu Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453681"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Použití existujícího modelu pomocí služby Azure Machine Learning

Další informace o použití existující model strojového učení ve službě Azure Machine Learning.

Pokud máte služby machine learning model, který se naučil mimo službu Azure Machine Learning, stále můžete službu model nasadit jako webovou službu nebo do zařízení IoT Edge. 

> [!TIP]
> Tento článek obsahuje základní informace o registrací a nasazením existující model. Po nasazení služby Azure Machine Learning poskytuje monitorování pro váš model. Umožňuje také k uložení vstupních dat odesílaných do nasazení, které lze použít pro data odchylek analýzy nebo školení nové verze modelu.
>
> Další informace o konceptů a termínů používaných zde najdete v tématu [spravovat, nasazovat a monitorovat modelů strojového učení](concept-model-management-and-deployment.md).
>
> Obecné informace o procesu nasazení najdete v tématu [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [vytvořit pracovní prostor](setup-create-workspace.md).

    > [!TIP]
    > Příklady Python v tomto článku se předpokládá, že `ws` proměnná je nastavená na váš pracovní prostor služby Azure Machine Learning.
    >
    > Příklady rozhraní příkazového řádku pomocí zástupného symbolu z `myworkspace` a `myresourcegroup`. Nahraďte tyto kontrolou na název pracovního prostoru a skupinu prostředků, který jej obsahuje.

* Azure Machine Learning SDK. Další informace najdete v části SDK pro Python v [vytvořit pracovní prostor](setup-create-workspace.md#sdk).

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a [rozšíření rozhraní příkazového řádku Machine Learning](reference-azure-machine-learning-cli.md).

* Trénovaného modelu. Model musí uložit trvale na jeden nebo více souborů ve svém vývojovém prostředí.

    > [!NOTE]
    > Abychom si předvedli registraci modelu školení mimo službu Azure Machine Learning, fragmentů kódu v tomto článku použijte modelů vytvořených projektem analýzy subjektivního hodnocení Twitteru Paolo Ripamonti: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrace modely

Registrace modelu umožňuje ukládat, verze a sledovat metadata o modelech ve vašem pracovním prostoru. V následujících příkladech Pythonu a rozhraní příkazového řádku `models` adresář obsahuje `model.h5`, `model.w2v`, `encoder.pkl`, a `tokenizer.pkl` soubory. Nahraje soubory obsažené v tomto příkladu `models` adresáře jako novou registraci modelu s názvem `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Další informace najdete v tématu [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) odkaz.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Další informace najdete v tématu [registrace modelu ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) odkaz.


Další informace o registraci modelu obecné naleznete v tématu [spravovat, nasazovat a monitorovat modelů strojového učení](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definování konfigurace odvození

Odvozování configuration definuje prostředí použitý ke spuštění nasazeného modelu. Odvozování configuration odkazuje na následující soubory, které se používají ke spuštění modelu při nasazení:

* Modul runtime. Jediná platná hodnota pro modul runtime momentálně je Python.
* Skriptu položka. Tento soubor (s názvem `score.py`) načte model, při spuštění v nasazované službě. Také je zodpovědná za příjem dat, předávání do modelu a poté vrácením odpovědi.
* Soubor prostředí conda. Tento soubor definuje balíčky Pythonu potřebné ke spuštění skriptu modelu a položka. 

Následující příklad ukazuje základní odvozování configuration pomocí sady Python SDK:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Další informace najdete v tématu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) odkaz.

Rozhraní příkazového řádku načte konfiguraci odvození ze souboru YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Další informace o konfiguraci odvození, naleznete v tématu [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Položka skriptu

Tento skript položka má jenom dvě požadované funkce, `init()` a `run(data)`. Tyto funkce slouží k inicializaci služby při spuštění a spuštění modelu s použitím žádosti o data předaná klientem. Zbývající část skriptu zpracovává načítání a spuštěná modely.

> [!IMPORTANT]
> Není obecná položka skript, který se dá použít pro všechny modely. Je vždy specifický pro model, který se používá. Je nutné pochopit, jak načíst model, formát dat, který očekává, že model a jak ke stanovení skóre pro data pomocí modelu.

Následující kód Pythonu je položka ukázkového skriptu (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Další informace o skriptech položky, naleznete v tématu [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Prostředí Conda

Následující kód YAML popisuje prostředí conda potřebných ke spuštění skriptu modelu a položka:

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

Další informace najdete v tématu [nasazujte modely pomocí služby Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definovat nasazení

[Webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) balíček obsahuje třídy používané pro nasazení. Třídy, které používáte Určuje, ve kterém je model nasazený. Například pokud chcete nasadit jako webovou službu ve službě Azure Kubernetes Service, použít [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) pro vytvoření konfigurace nasazení.

Následující kód Python definuje konfiguraci nasazení pro místní nasazení. Tato konfigurace implementuje model jako webovou službu do místního počítače.

> [!IMPORTANT]
> Místní nasazení vyžaduje funkční instalací [Docker](https://www.docker.com/) v místním počítači:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Další informace najdete v tématu [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) odkaz.

Rozhraní příkazového řádku načte konfiguraci nasazení ze souboru YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Nasazení jiného cílového výpočetního prostředí, jako je Azure Kubernetes Service v cloudu Azure, je stejně snadné jako změna konfigurace nasazení. Další informace najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Nasazení modelu

Následující příklad načte informace o registrovaných modelu s názvem `sentiment`a pak nasadí jako služba s názvem `sentiment`. Během nasazování konfigurace odvození a konfigurace nasazení slouží k vytvoření a konfigurace prostředí služby:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Další informace najdete v tématu [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) odkaz.

Nasazení modelu z rozhraní příkazového řádku, použijte následující příkaz. Tento příkaz nasadí verze 1 registrovanému modelu (`sentiment:1`) pomocí odvození a nasazení konfigurace, které jsou uložené v `inferenceConfig.json` a `deploymentConfig.json` soubory:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Další informace najdete v tématu [nasazení modelu ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) odkaz.

Další informace o nasazení naleznete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Spotřeba typu žádost odpověď

Po nasazení se zobrazí bodování identifikátoru URI. Pomocí tohoto identifikátoru URI může používají klienti k odesílání žádostí o službu. Následující příklad je základní Pythonového klienta, který odesílá data do služby a zobrazí odpovědi:

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

Další informace o tom, jak využívat nasazené služby zobrazit [vytvoření klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Další postup

* [Monitorování vašich modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md)
* [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Jak vytvořit klienta pro nasazenou modelu](how-to-consume-web-service.md)