---
title: Použití a nasazení existujících modelů
titleSuffix: Azure Machine Learning
description: Zjistěte, jak můžete používat Azure Machine Learning s modely, které byly trénované mimo službu. Můžete zaregistrovat modely vytvořené mimo Azure Machine Learning a pak je nasadit jako webovou službu nebo modul Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472371"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Použití existujícího modelu s Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak používat existující model strojového učení s Azure Machine Learning.

Pokud máte model strojového učení, který byl trénovaný mimo Azure Machine Learning, můžete stále použít službu k nasazení modelu jako webové služby nebo na zařízení IoT Edge. 

> [!TIP]
> Tento článek obsahuje základní informace o registraci a nasazení existujícího modelu. Po nasazení Azure Machine Learning poskytuje monitorování pro váš model. Umožňuje také ukládat vstupní data odeslaná do nasazení, která lze použít pro analýzu posunu dat nebo trénování nových verzí modelu.
>
> Další informace o konceptech a termínech, které se zde používají, najdete v [tématu Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).
>
> Obecné informace o procesu nasazení najdete v tématu [Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace naleznete [v tématu Vytvoření pracovního prostoru](how-to-manage-workspace.md).

    > [!TIP]
    > Příklady Pythonu v tomto `ws` článku předpokládají, že proměnná je nastavena na pracovní prostor Azure Machine Learning.
    >
    > Příklady zapínání/klišé `myworkspace` používají `myresourcegroup`zástupný symbol a . Nahraďte je názvem pracovního prostoru a skupinou prostředků, která jej obsahuje.

* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Rozšíření [cli Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a [Machine Learning CLI](reference-azure-machine-learning-cli.md).

* Trénovaný model. Model musí být trvalé na jeden nebo více souborů ve vývojovém prostředí.

    > [!NOTE]
    > Chcete-li demonstrovat registraci modelu trénovaného mimo Azure Machine Learning, ukázkové fragmenty kódu v tomto [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)článku používají modely vytvořené projektem analýzy mínění na Twitteru Paola Ripamontiho: .

## <a name="register-the-models"></a>Registrace modelu (modelů)

Registrace modelu umožňuje ukládat, verzí a sledovat metadata o modelech v pracovním prostoru. V následujících příkladech Pythonu `models` a ROZHRANÍ `model.h5` `model.w2v`PŘÍKAZOVÉHO příkazu obsahuje adresář soubory , `encoder.pkl`, a `tokenizer.pkl` soubory. Tento příklad nahraje soubory obsažené `models` v adresáři jako `sentiment`nový model registrace s názvem :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Další informace naleznete v odkazu [Model.register().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Můžete také nastavit `tags` `properties` objekty add a dictionary do registrovaného modelu. Tyto hodnoty lze později pomoci identifikovat konkrétní model. Například použitý rámec, parametry školení atd.

Další informace naleznete v odkazu na [registr modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Další informace o registraci modelu obecně naleznete v [tématu Správa, nasazení a monitorování modelů strojového učení](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definování konfigurace odvození

Konfigurace odvození definuje prostředí používané ke spuštění nasazeného modelu. Konfigurace odvození odkazuje na následující entity, které se používají ke spuštění modelu při jeho nasazení:

* Vstupní skript. Tento soubor `score.py`(s názvem) načte model při spuštění nasazené služby. Je také zodpovědný za příjem dat, jejich předání modelu a potom vrácení odpovědi.
* Prostředí Azure Machine [Learning](how-to-use-environments.md). Prostředí definuje softwarové závislosti potřebné ke spuštění modelu a vstupního skriptu.

Následující příklad ukazuje, jak pomocí sady SDK vytvořit prostředí a potom ji použít s konfigurací odvození:

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

Další informace najdete v těchto článcích:

+ [Jak používat prostředí](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) odkaz.


ClI načte konfiguraci odvození ze souboru YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Pomocí cli je prostředí conda definováno v souboru, `myenv.yml` na který odkazuje konfigurace odvození. Obsah tohoto souboru je následující YAML:

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

Další informace o konfiguraci odvození najdete v [tématu nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Vstupní skript

Vstupní skript má pouze dvě `init()` `run(data)`požadované funkce a . Tyto funkce se používají k inicializaci služby při spuštění a spuštění modelu pomocí dat požadavků předaná klientem. Zbytek skriptu zpracovává načítání a spouštění modelů.

> [!IMPORTANT]
> Neexistuje obecný vstupní skript, který funguje pro všechny modely. Je vždy specifické pro model, který se používá. Musí pochopit, jak načíst model, formát dat, který model očekává, a jak skóre dat pomocí modelu.

Následující kód Pythonu je ukázkový vstupní skript (`score.py`):

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

Další informace o vstupních skriptech najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definovat nasazení

Balíček [webové služby](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) obsahuje třídy používané pro nasazení. Třída, kterou používáte určuje, kde je model nasazen. Chcete-li například nasadit jako webovou službu ve službě Azure Kubernetes, použijte k vytvoření konfigurace nasazení [aksWebService.deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)

Následující kód Pythonu definuje konfiguraci nasazení pro místní nasazení. Tato konfigurace nasazuje model jako webovou službu do místního počítače.

> [!IMPORTANT]
> Místní nasazení vyžaduje funkční instalaci [Dockeru](https://www.docker.com/) v místním počítači:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Další informace naleznete v odkazu [LocalWebservice.deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)

ClI načte konfiguraci nasazení ze souboru YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Nasazení na jiný výpočetní cíl, jako je například služba Azure Kubernetes v cloudu Azure, je stejně snadné jako změna konfigurace nasazení. Další informace naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Nasazení modelu

Následující příklad načte informace o `sentiment`registrovaném modelu s názvem `sentiment`a poté jej nasadí jako službu s názvem . Během nasazení se konfigurace odvození a konfigurace nasazení používají k vytvoření a konfiguraci prostředí služby:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Další informace naleznete v odkazu [Model.deploy().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Chcete-li nasadit model z příkazového příkazu, použijte následující příkaz. Tento příkaz nasadí verzi 1`sentiment:1`registrovaného modelu ( ) pomocí `inferenceConfig.json` konfigurace `deploymentConfig.json` odvození a nasazení uložené v souborech a:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Další informace naleznete v odkazu [na nasazení modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Další informace o nasazení najdete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Spotřeba požadavku a odpovědi

Po nasazení se zobrazí bodovací identifikátor URI. Tento identifikátor URI mohou klienti použít k odesílání požadavků na službu. Následující příklad je základní klient Pythonu, který odesílá data do služby a zobrazuje odpověď:

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

Další informace o tom, jak využívat nasazenou službu, naleznete [v tématu Vytvoření klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Další kroky

* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)
* [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Jak vytvořit klienta pro nasazený model](how-to-consume-web-service.md)
