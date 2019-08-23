---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning service
description: 'Naučte se, jak a kde nasadit modely služby Azure Machine Learning, včetně: Azure Container Instances, služby Azure Kubernetes, Azure IoT Edge a programovatelné pole brány.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: acb3717f0e71ca1e67f1ddec79a259935f6cc539
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897701"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo IoT Edge zařízení.

Pracovní postup je podobný bez ohledu na [to, kam](#target) model nasazujete:

1. Zaregistrujte model.
1. Příprava na nasazení (určení prostředků, využití, výpočetního cíle).
1. Model nasaďte do cílového výpočetního prostředí.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru služby Azure Machine Learning](how-to-manage-workspace.md).

- Model. Pokud nemáte školený model, můžete použít soubory závislostí & modelu, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)nebo [rozšíření Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Připojení k vašemu pracovnímu prostoru

Následující kód ukazuje, jak se připojit k pracovnímu prostoru služby Azure Machine Learning pomocí informací uložených v mezipaměti do místního vývojového prostředí:

**Používání sady SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Další informace o tom, jak se pomocí sady SDK připojit k pracovnímu prostoru, najdete v tématu [sada sdk Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Použití rozhraní příkazového řádku**

Při použití rozhraní příkazového řádku použijte `-w` parametr `--workspace-name` nebo k určení pracovního prostoru pro příkaz.

**Použití VS Code**

Při použití VS Code se pracovní prostor vybere pomocí grafického rozhraní. Další informace najdete v tématu [nasazení a Správa modelů](how-to-vscode-tools.md#deploy-and-manage-models) v dokumentaci k rozšíření vs Code.

## <a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený ve více souborech, můžete ho zaregistrovat jako jeden model v pracovním prostoru. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

> [!TIP]
> Při registraci modelu zadáte buď cestu k umístění v cloudu (z školicích běhů), nebo místní adresář. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace; nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [co je get_model_path](#what-is-get_model_path).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo může pocházet z někde jinde. Následující příklady ukazují, jak registrovat model:

### <a name="register-a-model-from-an-experiment-run"></a>Registrace modelu z Experimentového běhu

Fragmenty kódu v této části ukazují, jak registrovat model z školicího běhu:

> [!IMPORTANT]
> Tyto fragmenty kódu předpokládají, že jste dříve provedli školicí běh a máte přístup `run` k objektu (příklad sady SDK) nebo hodnotě ID běhu (příklad CLI). Další informace o školicích modelech najdete v tématu [Vytvoření a použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

+ **Používání sady SDK**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  `model_path` Odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na adresář, který obsahuje soubory.

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na adresář, který obsahuje soubory.

+ **Použití VS Code**

  Zaregistrujte modely pomocí všech souborů modelů nebo složek s rozšířením [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat zadáním **místní cesty** k modelu. Můžete zadat buď složku, nebo jeden soubor. Tuto metodu můžete použít k registraci obou modelů, které jsou vyškolené pomocí služby Azure Machine Learning Service a poté staženy, nebo v modelech vyškolených mimo Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **ONNX příklad se sadou Python SDK:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na adresář, který obsahuje soubory.

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na adresář, který obsahuje soubory.

**Časový odhad**: Přibližně 10 sekund.

Další informace najdete v tématu v referenční dokumentaci [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely vyškolenými mimo Azure Machine Learning službu najdete v tématu [Jak nasadit existující model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

K hostování nasazení webové služby lze použít následující výpočetní cíle nebo výpočetní prostředky. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Příprava nasazení

Nasazení modelu vyžaduje několik věcí:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než se vrátí do klienta.

    > [!IMPORTANT]
    > Sada Azure Machine Learning SDK neposkytuje způsob, jak webové služby nebo IoT Edge nasazení získat přístup k úložišti dat nebo datovým sadám. Pokud potřebujete nasadit model pro přístup k datům uloženým mimo nasazení, jako je například v účtu Azure Storage, je nutné vyvinout vlastní řešení kódu pomocí příslušné sady SDK. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python).
    >
    > Další alternativou, která může fungovat pro váš scénář, je [Batch předpovědi](how-to-run-batch-predictions.md), která poskytuje přístup k úložišti dat při bodování.

* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

* __Konfigurace nasazení__ pro výpočetní cíl, který je hostitelem nasazeného modelu. Tato konfigurace popisuje například požadavky na paměť a procesor potřebný ke spuštění modelu.

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__a __Konfigurace nasazení__. Konfigurace odvození odkazuje na skript vstupu a další závislosti. Tyto konfigurace jsou definovány programově při použití sady SDK a jako soubory JSON při použití rozhraní příkazového řádku k provedení nasazení.

### <a id="script"></a> 1. Definování vstupního skriptu & závislosti

Skript vstupu přijímá data odeslaná do nasazené webové služby a předává je do modelu. Pak vezme odpověď vrácenou modelem a vrátí ji klientovi. **Skript je specifický pro váš model**; musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce je spuštěna pouze jednou při spuštění kontejneru Docker pro vaši webovou službu.

* `run(input_data)`: Tato funkce používá model k předpovědi hodnoty založené na vstupních datech. Vstupy a výstupy do běhu obvykle používají JSON pro serializaci a deserializaci. Můžete také pracovat s nezpracovanými binárními daty. Data můžete transformovat před odesláním do modelu nebo před návratem do klienta.

#### <a name="what-is-get_model_path"></a>Co je get_model_path?

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Tento název použijete s [modelem. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pro načtení cesty k souborům modelů v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, toto rozhraní API vrátí cestu k adresáři, který obsahuje tyto soubory.

Při registraci modelu mu dáte název, který odpovídá umístění modelu, a to buď místně, nebo během nasazování služby.

Následující příklad vrátí cestu k jednomu souboru s názvem `sklearn_mnist_model.pkl` (který byl registrován s názvem `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Volitelné Automatické generování schématu

Chcete-li automaticky vygenerovat schéma pro webovou službu, poskytněte vzorek vstupu a/nebo výstupu v konstruktoru pro jeden z definovaných objektů typu a typ a vzorek slouží k automatickému vytvoření schématu. Služba Azure Machine Learning pak vytvoří specifikaci [openapi](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazování.

V současné době jsou podporovány následující typy:

* `pandas`
* `numpy`
* `pyspark`
* standardní objekt Pythonu

Chcete-li použít generování schématu, `inference-schema` zahrňte balíček do vašeho souboru prostředí conda.

##### <a name="example-dependencies-file"></a>Příklad souboru závislostí

Následující YAML je příkladem souboru závislostí conda pro odvození:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Pokud chcete použít automatické generování schématu, váš vstupní skript **musí** importovat `inference-schema` balíčky.

Definujte vstupní a výstupní ukázkové formáty v `input_sample` proměnných a `output_sample` , které reprezentují formáty požadavků a odpovědí webové služby. Tyto ukázky použijte ve funkci vstupu a výstupu dekoratéry na `run()` funkci. Níže uvedený příklad scikit používá generování schématu.

##### <a name="example-entry-script"></a>Ukázkový skript vstupu

Následující příklad ukazuje, jak přijmout a vrátit data JSON:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Následující příklad ukazuje, jak definovat vstupní data jako `<key: value>` slovník pomocí datového rámce. Tato metoda je podporovaná pro využívání nasazené webové služby od Power BI ([Další informace o tom, jak webovou službu využívat z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Další ukázkové skripty najdete v následujících příkladech:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Binární data

Pokud váš model přijímá binárních dat, jako je například image, musíte změnit `score.py` soubor se používá pro své nasazení tak, aby přijímal nezpracovaná požadavky HTTP. K přijetí nezpracovaných dat použijte `AMLRequest` třídu ve vašem vstupním skriptu a `@rawhttp` přidejte dekoratér do `run()` funkce.

Tady je příklad `score.py` , který přijímá binární data:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` Třída je`azureml.contrib` v oboru názvů. Věci v tomto oboru názvů se často mění při práci na vylepšení služby. V takovém případě by se cokoli v tomto oboru názvů měly považovat za verzi Preview a společnost Microsoft je plně Nepodporovaná.
>
> Pokud potřebujete tuto možnost otestovat v místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Sdílení prostředků mezi zdroji je způsob, jak povolí vyžádání prostředků na webové stránce z jiné domény. CORS funguje na základě hlaviček protokolu HTTP odeslaných s požadavkem klienta a vrácených s odpovědí služby. Další informace o CORS a platných hlavičkách najdete v tématu [sdílení prostředků mezi zdroji](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) v Wikipedii.

Pokud chcete nakonfigurovat nasazení modelu tak, aby podporovalo CORS `AMLResponse` , použijte ve svém vstupním skriptu třídu. Tato třída umožňuje nastavit záhlaví objektu Response.

Následující příklad nastaví `Access-Control-Allow-Origin` hlavičku odpovědi z vstupního skriptu:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` Třída je`azureml.contrib` v oboru názvů. Věci v tomto oboru názvů se často mění při práci na vylepšení služby. V takovém případě by se cokoli v tomto oboru názvů měly považovat za verzi Preview a společnost Microsoft je plně Nepodporovaná.
>
> Pokud potřebujete tuto možnost otestovat v místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definování InferenceConfig

Konfigurace odvození popisuje, jak nakonfigurovat model pro vytvoření předpovědi. Tato konfigurace není součástí skriptu pro zadávání. odkazuje na váš vstupní skript a používá se k vyhledání všech prostředků vyžadovaných nasazením. Používá se později při skutečně nasazování modelu.

Následující příklad ukazuje, jak vytvořit odvozenou konfiguraci. Tato konfigurace určuje modul runtime, skript vstupu a (volitelně) soubor prostředí conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Další informace najdete v referenčních informacích ke třídě [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Příklad rozhraní příkazového řádku InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace obsahuje následující položky:

* Tento model vyžaduje Python.
* [Skript vstupu](#script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby
* Soubor Conda, který popisuje balíčky Pythonu potřebné k odvození

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definování konfigurace nasazení

Před nasazením musíte definovat konfiguraci nasazení. __Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby__. Například při nasazování místně musíte zadat port, kam služba přijímá požadavky. Konfigurace nasazení není součástí skriptu pro vložení. Slouží k definování charakteristik výpočetního cíle, který bude hostovat model a vstupní skript.

Může být také potřeba vytvořit výpočetní prostředek. Například pokud ještě nemáte službu Azure Kubernetes přidruženou k vašemu pracovnímu prostoru.

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílové výpočetní prostředí | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instance kontejneru Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Každá z těchto tříd pro webové služby Local, ACI a AKS se dá importovat z `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> Před nasazením modelu jako služby ho můžete chtít profilovat a zjistit optimální požadavky na procesor a paměť. Model můžete profilovat pomocí sady SDK nebo rozhraní příkazového řádku. Další informace najdete v tématu [profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) a [AZ ml model model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) reference.
>
> Výsledky profilování modelu jsou generovány jako `Run` objekt. Další informace najdete v referenčních informacích ke třídě [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Nasadit do cíle

Nasazení používá k nasazení modelů konfiguraci nasazení nasazení konfigurace. Proces nasazení je podobný bez ohledu na cíl výpočtů. Nasazení na AKS se mírně liší, protože musíte poskytnout odkaz na cluster AKS.

### <a id="local"></a>Místní nasazení

K místnímu nasazení musíte mít v místním počítači nainstalovaný Docker.

#### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace naleznete v referenční dokumentaci pro [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)a [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Webová služba NotebookVM (DEVTEST)

Viz [nasazení modelu na virtuální počítače poznámkového bloku](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Viz [nasazení na Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Služba Azure Kubernetes (DEVTEST & produkce)

Viz [nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Využívání webových služeb

Každá nasazená webová služba poskytuje REST API, takže můžete vytvářet klientské aplikace v různých programovacích jazycích. Pokud jste povolili ověřování klíčů pro vaši službu, musíte v hlavičce žádosti zadat klíč služby jako token.
Pokud jste povolili ověřování pomocí tokenu pro vaši službu, musíte v hlavičce žádosti zadat token Azure Machine Learning JWT jako nosný token.

> [!TIP]
> Po nasazení služby můžete načíst dokument JSON schématu. Použijte [vlastnost swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) z nasazené webové služby, například `service.swagger_uri`, k získání identifikátoru URI pro soubor Swagger místní webové služby.

### <a name="request-response-consumption"></a>Spotřeba požadavků a odpovědí

Tady je příklad, jak volat vaši službu v Pythonu:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Další informace najdete v tématu [vytvoření klientských aplikací pro využívání WebServices](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schéma webové služby (specifikace OpenAPI)

Pokud jste pro nasazení použili automatické generování schématu, můžete získat adresu specifikace OpenAPI pro službu pomocí [vlastnosti swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Například, `print(service.swagger_uri)`. K načtení specifikace použijte požadavek GET (nebo otevřete identifikátor URI v prohlížeči).

Následující dokument JSON je příklad schématu (specifikace OpenAPI) generovaného pro nasazení:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Další informace o specifikaci najdete v tématu věnovaném [specifikaci rozhraní API pro otevření](https://swagger.io/specification/).

Nástroj, který umožňuje vytvářet klientské knihovny ze specifikace, najdete v tématu [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány službou Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v článku [Jak spustit Batch předpovědi](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v článku [nasazení Azure Machine Learning jako IoT Edge modulu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Aktualizovat webové služby

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Průběžné nasazování modelu 

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Pomocí rozšíření Machine Learning pro Azure DevOps můžete aktivovat kanál nasazení, když je nový model strojového učení zaregistrován v pracovním prostoru služby Azure Machine Learning. 

1. Zaregistrujte se do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), který umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu/libovolný Cloud. Azure Pipelines [se liší od kanálů ml](concept-ml-pipelines.md#compare). 

1. [Vytvořte projekt Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instalace [rozšíření Machine Learning pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Pomocí __připojení služby__ nastavte připojení instančního objektu k vašemu pracovnímu prostoru služby Azure Machine Learning, abyste měli přístup ke všem artefaktům. Přejděte na nastavení projektu, klikněte na připojení služby a vyberte Azure Resource Manager.

    [![zobrazení-služba-připojení](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. Definujte AzureMLWorkspace jako __úroveň oboru__ a vyplňte následující parametry.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Abyste mohli model strojového učení průběžně nasazovat pomocí Azure Pipelines, vyberte v části kanály možnost __vydaná verze__. Přidejte nový artefakt, vyberte artefakt modelu AzureML a připojení služby, které jste vytvořili v předchozím kroku. Vyberte model a verzi pro aktivaci nasazení. 

    [![SELECT-AzureMLmodel-artefakt](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Povolte na artefaktu modelu aktivační událost modelu. Zapnutím triggeru pokaždé, když je zadaná verze (tj. nejnovější verze) tohoto modelu se registruje ve vašem pracovním prostoru, aktivuje se kanál verze Azure DevOps. 

    [![Enable-model-Trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Další ukázkové projekty a příklady najdete v následujících ukázkových úložištích:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.
Chcete-li odstranit registrovaný model, použijte `model.delete()`.

Další informace naleznete v referenční dokumentaci pro [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)a [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Další postup
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)

