---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning service
description: Naučte se, jak a kde nasadit modely služby Azure Machine Learning, včetně Azure Container Instances, služby Azure Kubernetes, Azure IoT Edge a programovatelné pole brány.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 358cbfb80da03d20475e591f0fd0c5b907b83b22
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984703"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo Azure IoT Edge zařízení.

Pracovní postup je podobný bez ohledu na [to, kam model nasazujete](#target) :

1. Zaregistrujte model.
1. Připravte se na nasazení. (Zadejte prostředky, využití, cíl výpočtů.)
1. Model nasaďte do cílového výpočetního prostředí.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru služby Azure Machine Learning](how-to-manage-workspace.md).

- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [sadu Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Připojení k vašemu pracovnímu prostoru

Následující kód ukazuje, jak se připojit k pracovnímu prostoru služby Azure Machine Learning pomocí informací uložených v mezipaměti do místního vývojového prostředí:

+ **Používání sady SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Další informace o použití sady SDK pro připojení k pracovnímu prostoru naleznete v dokumentaci [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Použití rozhraní příkazového řádku**

   Při použití rozhraní příkazového řádku použijte `-w` parametr `--workspace-name` nebo k určení pracovního prostoru pro příkaz.

+ **Použití VS Code**

   Když použijete VS Code, vybíráte pracovní prostor pomocí grafického rozhraní. Další informace najdete v tématu [nasazení a Správa modelů](how-to-vscode-tools.md#deploy-and-manage-models) v dokumentaci k rozšíření vs Code.

## <a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [co je get_model_path?](#what-is-get_model_path).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-experiment-run"></a>Registrace modelu z experimentového běhu

Fragmenty kódu v této části ukazují, jak registrovat model z školicího běhu:

> [!IMPORTANT]
> Chcete-li použít tyto fragmenty kódu, musíte předtím provést školicí běh a potřebujete mít přístup k `Run` objektu (příklad sady SDK) nebo hodnotě ID běhu (příklad rozhraní příkazového řádku). Další informace o školicích modelech najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

+ **Používání sady SDK**

  Když použijete sadu SDK k vytvoření výukového modelu, můžete získat buď objekt [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) , nebo objekt [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) v závislosti na tom, jak jste model využívali. Každý objekt lze použít k registraci modelu vytvořeného spuštěním experimentu.

  + Registrace modelu z `azureml.core.Run` objektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory. Další informace najdete v dokumentaci ke [spuštění. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Registrace modelu z `azureml.train.automl.run.AutoMLRun` objektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    V tomto příkladu `metric` nejsou zadány `iteration` parametry a, takže iterace s nejlepší primární metrikou bude zaregistrována. `model_id` Hodnota vrácená z běhu se používá místo názvu modelu.

    Další informace najdete v dokumentaci k [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na cestu ke složce, která obsahuje soubory.

+ **Použití VS Code**

  Pomocí rozšíření [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) Zaregistrujte modely pomocí libovolných souborů modelů nebo složek.

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat zadáním místní cesty k modelu. Můžete zadat cestu buď složky, nebo jednoho souboru. Tuto metodu můžete použít k registraci modelů vyškolených pomocí služby Azure Machine Learning a potom ke stažení. Tuto metodu můžete použít také k registraci modelů, které jsou vyškolené mimo Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Používání sady SDK a ONNX**

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

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory.

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na cestu ke složce, která obsahuje soubory.

**Časový odhad**: Přibližně 10 sekund.

Další informace naleznete v dokumentaci k [třídě modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely, které jsou výukou mimo Azure Machine Learning službu, najdete v tématu [Jak nasadit existující model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

K hostování nasazení webové služby můžete použít následující výpočetní cíle nebo výpočetní prostředky:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Příprava nasazení

K nasazení modelu potřebujete následující položky:

* **Vstupní skript**. Tento skript přijímá požadavky, zarovnává požadavky pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > * Vstupní skript je specifický pro váš model. Musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    >   Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď před jejich vrácením klientovi.
    >
    > * Sada Azure Machine Learning SDK neposkytuje způsob, jak webové služby nebo IoT Edge nasazení získat přístup k úložišti dat nebo datovým sadám. Pokud váš nasazený model potřebuje přístup k datům uloženým mimo nasazení, jako jsou data v účtu úložiště Azure, musíte vytvořit vlastní řešení kódu pomocí příslušné sady SDK. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python).
    >
    >   Alternativou, která může být pro váš scénář fungovat, je [předpověď dávky](how-to-run-batch-predictions.md), která poskytuje přístup k úložištím dat během bodování.

* **Závislosti**, jako jsou například pomocné skripty nebo balíčky python/Conda, které jsou nutné ke spuštění vstupního skriptu nebo modelu.

* **Konfigurace nasazení** pro výpočetní cíl, který je hostitelem nasazeného modelu. Tato konfigurace popisuje například požadavky na paměť a procesor potřebný ke spuštění modelu.

Tyto položky jsou zapouzdřeny do *Konfigurace odvození* a *Konfigurace nasazení*. Konfigurace odvození odkazuje na skript vstupu a další závislosti. Tyto konfigurace můžete definovat programově při použití sady SDK k provedení nasazení. Můžete je definovat v souborech JSON při použití rozhraní příkazového řádku.

### <a id="script"></a> 1. Definice vstupního skriptu a závislostí

Skript vstupu přijímá data odeslaná do nasazené webové služby a předává je do modelu. Pak vezme odpověď vrácenou modelem a vrátí ji klientovi. *Skript je specifický pro váš model*. Musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce se spustí jenom jednou, když se spustí kontejner Docker pro vaši webovou službu.

* `run(input_data)`: Tato funkce používá model k předpovědi hodnoty založené na vstupních datech. Vstupy a výstupy běhu obvykle používají JSON pro serializaci a deserializaci. Můžete také pracovat s nezpracovanými binárními daty. Data je možné transformovat před jejich odesláním do modelu nebo před vrácením do klienta.

#### <a name="what-is-get_model_path"></a>Co je get_model_path?

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Tento název použijete s metodou [model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) , která načte cestu souboru modelu nebo souborů v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, toto rozhraní API vrátí cestu k adresáři, který obsahuje tyto soubory.

Když zaregistrujete model, přiřadíte mu název. Název odpovídá umístění modelu, a to buď místně, nebo během nasazování služby.

> [!IMPORTANT]
> Pokud jste pomocí automatizovaného strojového učení využívali model `model_id` , použije se jako název modelu hodnota. Příklad registrace a nasazení modelu vyškolený pomocí automatizovaného strojového učení najdete v tématu [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) na GitHubu.

Následující příklad vrátí cestu k jednomu souboru s názvem `sklearn_mnist_model.pkl` (který byl registrován s názvem `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Volitelné Automatické generování schématu

Chcete-li automaticky vygenerovat schéma pro webovou službu, poskytněte vzorek vstupu a/nebo výstupu v konstruktoru pro jeden z definovaných objektů typu. Typ a ukázka slouží k automatickému vytvoření schématu. Služba Azure Machine Learning pak vytvoří specifikace [openapi](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazování.

Tyto typy jsou aktuálně podporovány:

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

Pokud chcete použít automatické generování schématu, váš vstupní skript musí importovat `inference-schema` balíčky.

Definujte vstupní a výstupní ukázkové formáty v `input_sample` proměnných a `output_sample` , které reprezentují formáty požadavků a odpovědí webové služby. Tyto ukázky použijte ve funkci vstupu a výstupu dekoratéry na `run()` funkci. Následující scikit příklad používá generování schématu.

##### <a name="example-entry-script"></a>Ukázkový skript vstupu

Následující příklad ukazuje, jak přijmout a vrátit data JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # Note that here "sklearn_regression_model.pkl" is the name of the model registered under.
    # This is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Následující příklad ukazuje, jak definovat vstupní data jako `<key: value>` slovník pomocí datového rámce. Tato metoda je podporována pro využívání nasazené webové služby od Power BI. (Další[informace o tom, jak využívat webovou službu z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    # Replace model_name with your actual model name, if necessary.
    model_path = Model.get_model_path('model_name')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Další příklady najdete v následujících skriptech:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Binární data

Pokud váš model akceptuje binární data, jako je obrázek, musíte upravit `score.py` soubor, který se používá pro vaše nasazení, aby přijímal nezpracované požadavky HTTP. K přijetí nezpracovaných dat použijte `AMLRequest` třídu ve vašem vstupním skriptu a `@rawhttp` přidejte dekoratér do `run()` funkce.

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` Třída je`azureml.contrib` v oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Sdílení prostředků mezi zdroji je způsob, jak povolí vyžádání prostředků na webové stránce z jiné domény. CORS funguje pomocí hlaviček HTTP odeslaných s požadavkem klienta a vrácených s odpovědí služby. Další informace o CORS a platných hlavičkách najdete v tématu [sdílení prostředků mezi zdroji](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) v Wikipedii.

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` Třída je`azureml.contrib` v oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definování InferenceConfig

Konfigurace odvození popisuje, jak nakonfigurovat model pro vytvoření předpovědi. Tato konfigurace není součástí vašeho skriptu pro zadávání. Odkazuje na váš vstupní skript a používá se k vyhledání všech prostředků vyžadovaných nasazením. Používá se později při nasazení modelu.

Konfigurace odvození může použít Azure Machine Learning prostředí k definování závislostí softwaru potřebných pro vaše nasazení. Prostředí umožňují vytvářet, spravovat a opakovaně používat závislosti softwaru vyžadované pro školení a nasazení. Následující příklad ukazuje načtení prostředí z pracovního prostoru a jeho následné použití s konfigurací odvození:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

Závislosti můžete zadat také přímo bez použití prostředí. Následující příklad ukazuje, jak vytvořit konfiguraci odvození, která načte závislosti softwaru ze souboru conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Další informace najdete v dokumentaci ke třídě [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Příklad rozhraní příkazového řádku InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace určuje následující nastavení:

* Model vyžaduje Python.
* [Skript vstupu](#script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby.
* Soubor Conda, který popisuje balíčky Pythonu potřebné pro odvození.

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definování konfigurace nasazení

Před nasazením modelu je nutné definovat konfiguraci nasazení. *Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby.* Pokud například nasadíte model místně, je nutné zadat port, kam služba přijímá požadavky. Konfigurace nasazení není součástí vašeho skriptu pro vložení. Slouží k definování charakteristik výpočetního cíle, který bude hostovat model a vstupní skript.

Je také možné, že budete muset vytvořit výpočetní prostředek, pokud například ještě nemáte k vašemu pracovnímu prostoru přidruženou instanci služby Azure Kubernetes (AKS).

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílové výpočetní prostředí | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Třídy pro místní, Azure Container Instances a webové služby AKS lze importovat z `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilace

Než model nasadíte jako službu, můžete ho chtít profilovat a zjistit optimální požadavky na procesor a paměť. K profilování modelu můžete použít sadu SDK nebo rozhraní příkazového řádku. Následující příklady ukazují, jak profilovat model pomocí sady SDK.

> [!IMPORTANT]
> Když použijete profilování, odvozená konfigurace, kterou zadáte, nemůže odkazovat na Azure Machine Learning prostředí. Místo toho definujte závislosti softwaru pomocí `conda_file` parametru `InferenceConfig` objektu.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Tento kód zobrazí výsledek podobný následujícímu výstupu:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Výsledky profilování modelu jsou generovány jako `Run` objekt.

Informace o používání profilování z rozhraní příkazového řádku najdete v tématu [AZ ml model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Další informace najdete v těchto dokumentech:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [Profil ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Odvození schématu konfiguračního souboru](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Nasadit do cíle

Nasazení používá k nasazení modelů konfiguraci nasazení nasazení konfigurace. Proces nasazení je podobný bez ohledu na cíl výpočtů. Nasazení na AKS se mírně liší, protože musíte zadat odkaz na cluster AKS.

### <a id="local"></a>Místní nasazení

Pokud chcete model nasadit lokálně, musíte na svém místním počítači nainstalovat Docker.

#### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace najdete v dokumentaci pro [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)a [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit model pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Další informace najdete v dokumentaci [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Webová služba poznámkového bloku (vývoj/testování)

Viz [nasazení modelu na virtuální počítače poznámkového bloku](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (vývoj/testování)

Viz [nasazení na Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Služba Azure Kubernetes (vývoj, testování a produkce)

Viz [nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Využívání webových služeb

Každá nasazená webová služba poskytuje REST API, takže můžete vytvářet klientské aplikace v různých programovacích jazycích.
Pokud jste povolili ověřování pomocí klíče pro vaši službu, musíte v hlavičce žádosti zadat klíč služby jako token.
Pokud jste povolili ověřování pomocí tokenu pro vaši službu, musíte v hlavičce žádosti zadat token Azure Machine Learning JWT jako nosný token.

> [!TIP]
> Po nasazení služby můžete načíst dokument JSON schématu. Použijte [vlastnost swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) z nasazené webové služby (například `service.swagger_uri`) k získání identifikátoru URI do souboru Swagger místní webové služby.

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

Další informace najdete v tématu [vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schéma webové služby (specifikace OpenAPI)

Pokud jste s vaším nasazením použili automatické generování schématu, můžete získat adresu specifikace OpenAPI pro službu pomocí [vlastnosti swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Například `print(service.swagger_uri)`.) K načtení specifikace použijte požadavek GET nebo otevřete identifikátor URI v prohlížeči.

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

Další informace najdete v tématu [specifikace openapi](https://swagger.io/specification/).

Nástroj, který umožňuje vytvářet klientské knihovny ze specifikace, najdete v tématu [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány službou Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v tématu [How to run Batch předpovědi](how-to-run-batch-predictions.md).

### <a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v tématu [nasazení Azure Machine Learning jako modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Aktualizovat webové služby

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Průběžné nasazování modelů

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Rozšíření Machine Learning pro Azure DevOps můžete použít k aktivaci kanálu nasazení, když je nový model strojového učení zaregistrován v pracovním prostoru služby Azure Machine Learning.

1. Zaregistrujte se do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), který umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu nebo Cloud. (Všimněte si, že Azure Pipelines nejsou stejné jako [Machine Learning kanály](concept-ml-pipelines.md#compare).)

1. [Vytvořte projekt Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Nainstalujte [Machine Learning rozšíření pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Pomocí připojení služby nastavte připojení instančního objektu k vašemu pracovnímu prostoru služby Azure Machine Learning, abyste mohli přistupovat ke artefaktům. Přejít na nastavení projektu, vyberte **připojení služby**a pak vyberte **Azure Resource Manager**:

    [![Vyberte Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. V seznamu **úroveň oboru** vyberte možnost **AzureMLWorkspace**a potom zadejte zbývající hodnoty:

    ![Vybrat AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Pokud chcete model strojového učení průběžně nasadit pomocí Azure Pipelines, vyberte v části kanály možnost **verze**. Přidejte nový artefakt a pak vyberte artefakt **modelu AzureML** a připojení služby, které jste vytvořili dříve. Vyberte model a verzi pro aktivaci nasazení:

    [![Vybrat model AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Povolte na artefaktu modelu aktivační událost modelu. Když zapnete aktivační událost, pokaždé, když je v pracovním prostoru zaregistrovaná verze daného modelu (tj. nejnovější verze), se aktivuje kanál vydané verze Azure DevOps.

    [![Povolení triggeru modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Další ukázkové projekty a příklady najdete v těchto ukázkových úložištích na GitHubu:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Stažení modelu
Pokud chcete svůj model stáhnout, abyste ho mohli použít ve svém vlastním prostředí pro spouštění, můžete to udělat pomocí následujících příkazů sady SDK/CLI:

SADĚ
```python
model_path = Model(ws,'mymodel').download()
```

CLI
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="package-models"></a>Modely balíčků

V některých případech můžete chtít vytvořit bitovou kopii Docker bez nasazení modelu (Pokud například plánujete [nasazení nasadit do Azure App Service](how-to-deploy-app-service.md)). Nebo můžete chtít stáhnout image a spustit ji v místní instalaci Docker. Můžete dokonce chtít stáhnout soubory používané k sestavení obrázku, zkontrolovat je, upravit je a sestavit bitovou kopii ručně.

Balení modelu vám umožní provádět tyto akce. Zabalí všechny prostředky potřebné k hostování modelu jako webové služby a umožňuje stáhnout zcela sestavenou image Docker nebo soubory potřebné k jeho sestavení. Existují dva způsoby použití balení modelu:

**Stáhnout zabalený model:** Stáhněte si image Docker, která obsahuje model a další soubory potřebné k jeho hostování jako webové služby.

**Vygenerujte souboru Dockerfile:** Stáhněte si souboru Dockerfile, model, vstupní skript a další materiály potřebné k vytvoření image Docker. Pak můžete soubory zkontrolovat nebo provést změny před tím, než Image sestavíte místně.

Oba balíčky lze použít k získání místní image Docker.

> [!TIP]
> Vytvoření balíčku se podobá nasazení modelu. Použijete registrovaný model a odvozenou konfiguraci.

> [!IMPORTANT]
> Pokud chcete stáhnout plně sestavenou Image nebo sestavit image místně, musíte mít ve svém vývojovém prostředí nainstalovaný [Docker](https://www.docker.com) .

### <a name="download-a-packaged-model"></a>Stažení zabaleného modelu

Následující příklad vytvoří image, která je zaregistrovaná ve službě Azure Container registry pro váš pracovní prostor:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po vytvoření balíčku můžete použít `package.pull()` k načtení image do místního prostředí Docker. Výstup tohoto příkazu zobrazí název obrázku. Příklad: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po stažení modelu použijte `docker images` příkaz k vypsání místních imagí:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Chcete-li spustit místní kontejner na základě tohoto obrázku, použijte následující příkaz pro spuštění pojmenovaného kontejneru z prostředí nebo příkazového řádku. Nahraďte `docker images` hodnotu identifikátorem image vráceným příkazem. `<imageid>`

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage`. Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Vygenerovat souboru Dockerfile a závislosti

Následující příklad ukazuje, jak stáhnout souboru Dockerfile, model a další prostředky potřebné k vytvoření image místně. `generate_dockerfile=True` Parametr označuje, že chcete soubory, nikoli plně sestavenou bitovou kopii.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Tento kód stáhne soubory potřebné k sestavení image do `imagefiles` adresáře. Souboru Dockerfile zahrnuté do uložených souborů odkazuje na základní image uloženou v registru služby Azure Container Registry. Při sestavování image v místní instalaci Docker musíte pro ověření v registru použít adresu, uživatelské jméno a heslo. Pomocí následujících kroků sestavíte Image pomocí místní instalace Docker:

1. Z prostředí nebo relace příkazového řádku použijte následující příkaz k ověření Docker pomocí služby Azure Container Registry. Nahradí `<address>`hodnoty `<username>`, `package.get_container_registry()`a `<password>` s hodnotami načtenými.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. K sestavení image použijte následující příkaz. Nahraďte `<imagefiles>` cestou k adresáři, kam `package.save()` se soubory uložily.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Tento příkaz nastaví název bitové kopie na `myimage`.

Chcete-li ověřit, zda je obrázek sestaven, `docker images` použijte příkaz. V seznamu by se `myimage` měla zobrazit obrázek:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Chcete-li spustit nový kontejner na základě tohoto obrázku, použijte následující příkaz:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage`. Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Příklad klienta pro otestování místního kontejneru

Následující kód je příkladem klienta Pythonu, který lze použít s kontejnerem:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Například klienty v jiných programovacích jazycích najdete v tématu [využívání modelů nasazených jako webové služby](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zastavení kontejneru Docker

Pokud chcete kontejner zastavit, použijte následující příkaz z jiného prostředí nebo příkazového řádku:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.
Chcete-li odstranit registrovaný model, použijte `model.delete()`.

Další informace najdete v dokumentaci pro [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) a [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Další postup
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Využití modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)

