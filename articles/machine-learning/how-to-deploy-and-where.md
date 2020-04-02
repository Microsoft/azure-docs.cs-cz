---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning
description: Zjistěte, jak a kde nasadit modely Azure Machine Learning, včetně instancí kontejnerů Azure, služby Azure Kubernetes, Azure IoT Edge a polí programovatelných polí brány.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: f6bab532b872a0974993f708edcb252d8bb54432
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529711"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Nasazování modelů pomocí služby Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo na zařízení Azure IoT Edge.

Pracovní postup je podobný bez ohledu na [to, kam model nasadíte:](#target)

1. Zaregistrujte model.
1. Připravte nasazení. (Zadejte prostředky, využití, cílový výpočetní objekt.)
1. Nasaďte model do cílového výpočetního objektu.
1. Otestujte nasazený model, nazývaný také webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v [tématu Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Modelka. Pokud nemáte trénovaný model, můžete použít model a závislost soubory uvedené v [tomto kurzu](https://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), Azure Machine Learning [SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo rozšíření Azure Machine Learning Visual Studio [Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Následující kód ukazuje, jak se připojit k pracovnímu prostoru Azure Machine Learning pomocí informací uložených v mezipaměti do místního vývojového prostředí:

+ **Použití sady SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Další informace o použití sady SDK pro připojení k pracovnímu prostoru najdete v dokumentaci [k Azure Machine Learning SDK pro Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)

+ **Použití cli**

   Při použití rozhraní příkazového `-w` `--workspace-name` řádku použijte parametr nebo k určení pracovního prostoru pro příkaz.

+ **Používání nástroje Visual Studio Code**

   Při použití kódu sady Visual Studio vyberete pracovní prostor pomocí grafického rozhraní. Další informace najdete [v tématu Nasazení a správa modelů](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) v dokumentaci k rozšíření kódu sady Visual Studio.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložen ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů můžete stáhnout nebo nasadit registrovaný model a přijímat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu, zadáte cestu buď umístění v cloudu (z trénovacího běhu) nebo místní adresář. Tato cesta je jen najít soubory pro upload jako součást procesu registrace. Nemusí odpovídat cestě použité v vstupním skriptu. Další informace naleznete [v tématu Vyhledání souborů modelu v vstupním skriptu](#load-model-files-in-your-entry-script).

Modely strojového učení jsou registrované v pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo odjinud. Při registraci modelu můžete volitelně poskytnout metadata o modelu. `tags` Slovníky `properties` a, které použijete pro registraci modelu, lze potom použít k filtrování modelů.

Následující příklady ukazují, jak zaregistrovat model.

### <a name="register-a-model-from-an-experiment-run"></a>Registrace modelu z experimentu

Fragmenty kódu v této části ukazují, jak zaregistrovat model z trénovacího běhu:

> [!IMPORTANT]
> Chcete-li použít tyto úryvky, musíte mít dříve provedené školení spustit `Run` a musíte mít přístup k objektu (SDK příklad) nebo hodnotu ID spuštění (příklad CLI). Další informace o trénovacích modelech najdete v tématu [Nastavení výpočetních cílů pro trénování modelu](how-to-set-up-training-targets.md).

+ **Použití sady SDK**

  Při použití sady SDK k trénování modelu můžete přijímat buď [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) objekt nebo [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) objekt, v závislosti na tom, jak jste trénoval model. Každý objekt lze zaregistrovat model vytvořený experimentu spustit.

  + Registrace modelu z `azureml.core.Run` objektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Parametr `model_path` odkazuje na umístění cloudu modelu. V tomto příkladu se používá cesta k jednomu souboru. Chcete-li do registrace modelu `model_path` zahrnout více souborů, nastavte na cestu ke složce, která soubory obsahuje. Další informace naleznete v dokumentaci [Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registrace modelu z `azureml.train.automl.run.AutoMLRun` objektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    V tomto `metric` příkladu `iteration` a parametry nejsou zadány, takže iterace s nejlepší primární metriky budou registrovány. Hodnota `model_id` vrácená z běhu se používá místo názvu modelu.

    Další informace naleznete v dokumentaci [K odsát register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Použití cli**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Parametr `--asset-path` odkazuje na umístění cloudu modelu. V tomto příkladu se používá cesta k jednomu souboru. Chcete-li do registrace modelu `--asset-path` zahrnout více souborů, nastavte na cestu ke složce, která soubory obsahuje.

+ **Používání nástroje Visual Studio Code**

  Zaregistrujte modely pomocí libovolné ho modelu soubory nebo složky pomocí rozšíření [Visual Studio Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat poskytnutím místní cesty modelu. Můžete zadat cestu ke složce nebo k jednomu souboru. Pomocí této metody můžete zaregistrovat modely trénované s Azure Machine Learning a pak stáhnout. Tuto metodu můžete také použít k registraci modelů trénovaných mimo Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Použití sad SDK a ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
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

  Chcete-li do registrace modelu `model_path` zahrnout více souborů, nastavte na cestu ke složce, která soubory obsahuje.

+ **Použití cli**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Chcete-li do registrace modelu `-p` zahrnout více souborů, nastavte na cestu ke složce, která soubory obsahuje.

**Odhad času**: Přibližně 10 sekund.

Další informace naleznete v dokumentaci k [třídě Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely trénovanými mimo Azure Machine Learning najdete v [tématu Jak nasadit existující model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Koncové body s jedním a více modely
Azure ML podporuje nasazení jednoho nebo více modelů za jeden koncový bod.

Koncové body s více modely používají sdílený kontejner k hostování více modelů. To pomáhá snižovat režijní náklady, zlepšuje využití a umožňuje řetězit moduly do souborů. Modely zadané ve skriptu nasazení jsou připojeny a zpřístupněny na disku sloužícího kontejneru – můžete je načíst do paměti na vyžádání a skóre na základě konkrétního modelu, který je požadován v době hodnocení.

Příklad E2E, který ukazuje, jak používat více modelů za jeden kontejnerizovaný koncový bod, naleznete [v tomto příkladu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Příprava nasazení

Chcete-li nasadit model jako službu, potřebujete následující součásti:

* **Definujte odvozené prostředí**. Toto prostředí zapouzdřuje závislosti potřebné ke spuštění modelu pro odvození.
* **Definujte bodovací kód**. Tento skript přijímá požadavky, skóre požadavky pomocí modelu a vrátí výsledky.
* **Definujte konfiguraci odvození**. Konfigurace odvození určuje konfiguraci prostředí, vstupní skript a další součásti potřebné ke spuštění modelu jako služby.

Jakmile budete mít potřebné součásti, můžete profilovat službu, která bude vytvořena v důsledku nasazení modelu pochopit jeho požadavky na procesor a paměť.

### <a name="1-define-inference-environment"></a>1. Definovat odvozené prostředí

Odvození konfigurace popisuje, jak nastavit webovou službu obsahující model. Používá se později, při nasazení modelu.

Konfigurace odvození používá prostředí Azure Machine Learning k definování softwarových závislostí potřebných pro vaše nasazení. Prostředí umožňují vytvářet, spravovat a znovu používat softwarové závislosti potřebné pro školení a nasazení. Můžete vytvořit prostředí z vlastních souborů závislostí nebo použít jedno z kurátorských prostředí Azure Machine Learning. Následující YAML je příkladem souboru závislostí Conda pro odvození. Všimněte si, že je nutné označit azureml výchozí hodnoty s verion >= 1.0.45 jako pip závislost, protože obsahuje funkce potřebné k hostování modelu jako webové služby. Pokud chcete použít automatické generování schématu, vstupní skript musí `inference-schema` také importovat balíčky.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Pokud je vaše závislost dostupná prostřednictvím conda a pip (od PyPi), společnost Microsoft doporučuje používat verzi Conda, protože balíčky Conda se obvykle docházejí s předem vytvořenými binárními soubory, které usnadňují instalaci.
>
> Další informace naleznete [v tématu Principy Conda a Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Chcete-li zkontrolovat, zda je vaše závislost `conda search <package-name>` k dispozici prostřednictvím Conda, použijte příkaz nebo použijte indexy balíčků na [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) adrese a [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Soubor závislostí můžete použít k vytvoření objektu prostředí a jeho uložení do pracovního prostoru pro budoucí použití:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Definovat bodovací kód

Vstupní skript přijímá data odeslaná do nasazené webové služby a předává je do modelu. Potom vezme odpověď vrácenou modelem a vrátí ji klientovi. *Skript je specifický pro váš model*. Musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce je spuštěna pouze jednou, když je spuštěn kontejner Dockeru pro webovou službu.

* `run(input_data)`: Tato funkce používá model k předvídání hodnoty založené na vstupních datech. Vstupy a výstupy spuštění obvykle pro serializaci a deserializaci používají JSON. Pracovat můžete také s nezpracovanými binárními daty. Data můžete transformovat před jejich odesláním do modelu nebo před jejich vrácením klientovi.

#### <a name="load-model-files-in-your-entry-script"></a>Načtení souborů modelu do vstupního skriptu

Existují dva způsoby, jak najít modely v vstupním skriptu:
* `AZUREML_MODEL_DIR`: Proměnná prostředí obsahující cestu k umístění modelu.
* `Model.get_model_path`: Rozhraní API, které vrací cestu k souboru modelu pomocí registrovaného názvu modelu.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR je proměnná prostředí vytvořená během nasazení služby. Tuto proměnnou prostředí můžete použít k nalezení umístění nasazených modelů.

Následující tabulka popisuje hodnotu AZUREML_MODEL_DIR v závislosti na počtu nasazených modelů:

| Nasazení | Hodnota proměnné prostředí |
| ----- | ----- |
| Jediný model | Cesta ke složce obsahující model. |
| Více modelů | Cesta ke složce obsahující všechny modely. Modely jsou umístěny podle názvu`$MODEL_NAME/$VERSION`a verze v této složce ( ) |

Během registrace modelu a nasazení modely jsou umístěny v cestě AZUREML_MODEL_DIR a jejich původní názvy souborů jsou zachovány.

Chcete-li získat cestu k souboru modelu v vstupním skriptu, zkombinujte proměnnou prostředí s cestou souboru, kterou hledáte.

**Příklad jednoho modelu**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Příklad více modelů**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Tento název se používá s metodou [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) k načtení cesty k souboru modelu nebo souborům v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, vrátí toto rozhraní API cestu k adresáři, který tyto soubory obsahuje.

Když zaregistrujete model, dáte mu název. Název odpovídá místu, kde je umístěn model, místně nebo během nasazení služby.

#### <a name="optional-define-model-web-service-schema"></a>(Nepovinné) Definovat schéma webové služby modelu

Chcete-li automaticky generovat schéma pro webovou službu, zadejte ukázku vstupu nebo výstupu v konstruktoru pro jeden z definovaných objektů typu. Typ a ukázka se používají k automatickému vytvoření schématu. Azure Machine Learning pak vytvoří specifikaci [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazení.

Tyto typy jsou aktuálně podporovány:

* `pandas`
* `numpy`
* `pyspark`
* Standardní objekt Pythonu

Chcete-li použít generování schématu, zahrňte balíček s otevřeným zdrojovým kódem `inference-schema` do souboru závislostí. Další informace o tomto [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)balíčku naleznete v tématu . Definujte vstupní a výstupní ukázkové formáty v proměnných `input_sample` a, `output_sample` které představují formáty požadavků a odpovědí pro webovou službu. Tyto ukázky použijte ve vstupní a výstupní `run()` funkce decorators na funkci. Následující příklad scikit-learn používá generování schématu.

##### <a name="example-entry-script"></a>Ukázkový vstupní skript

Následující příklad ukazuje, jak přijmout a vrátit data JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
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

Následující příklad ukazuje, jak definovat vstupní `<key: value>` data jako slovník pomocí DataFrame. Tato metoda je podporovaná pro využívání nasazené webové služby z Power BI. (Další[informace o využití webové služby z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
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

Další příklady naleznete v následujících skriptech:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binární data](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definovat konfiguraci odvození
    
Následující příklad ukazuje načítání prostředí z pracovního prostoru a jeho použití s konfigurací odvození:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Další informace o prostředích naleznete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození naleznete v dokumentaci třídy [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Informace o použití vlastní image Dockeru s odvozenou konfigurací najdete v tématu [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Příklad funkce CLI InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí příkazového příkazu k příkazu k příkazu:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace určuje následující nastavení:

* Že model vyžaduje Python.
* [Vstupní skript](#script), který se používá ke zpracování webových požadavků odeslaných nasazené službě.
* Soubor Conda, který popisuje balíčky Pythonu potřebné pro odvození.

Informace o použití vlastní image Dockeru s odvozenou konfigurací najdete v tématu [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Nepovinné) Profilmodelu k určení využití prostředků

Jakmile zaregistrujete model a připravíte další součásti nezbytné pro jeho nasazení, můžete určit procesor a paměť, které bude nasazená služba potřebovat. Profilování testuje službu, která spouští váš model a vrací informace, jako je využití procesoru, využití paměti a latence odezvy. Poskytuje také doporučení pro procesor a paměť na základě využití prostředků.

Chcete-li profilovat model, budete potřebovat:
* Registrovaný model.
* Odvození konfigurace na základě vstupního skriptu a definice prostředí odvození.
* Tabulková datová sada s jedním sloupcem, kde každý řádek obsahuje řetězec představující data ukázkového požadavku.

> [!IMPORTANT]
> V tomto okamžiku podporujeme pouze profilování služeb, které očekávají, že jejich data požadavku budou řetězec, například: řetězec serializovaný json, text, serializovaný obrázek řetězce atd. Obsah každého řádku datové sady (řetězce) bude vložen do těla požadavku HTTP a odeslán službě zapouzdření modelu pro vyhodnocování.

Níže je uveden příklad, jak můžete vytvořit vstupní datovou sadu pro profil služby, která očekává, že její data příchozích požadavků budou obsahovat serializovaný json. V tomto případě jsme vytvořili datovou sadu založenou na sto instancích stejného obsahu dat požadavku. V reálných scénářích doporučujeme použít větší datové sady obsahující různé vstupy, zejména v případě, že vaše využití prostředků modelu nebo chování je závislé na vstupu.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Jakmile budete mít připravenou datovou sadu obsahující ukázková data požadavku, vytvořte konfiguraci odvození. Odvození konfigurace je založena na score.py a definice prostředí. Následující příklad ukazuje, jak vytvořit konfiguraci odvození a spustit profilování:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Následující příkaz ukazuje, jak profilovat model pomocí příkazového příkazu k příkazu k příkazu:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Chcete-li zachovat informace vrácené profilováním, použijte značky nebo vlastnosti modelu. Pomocí značek nebo vlastností ukládá data s modelem v registru modelu. Následující příklady ukazují přidání nové značky obsahující informace `requestedCpu` a: `requestedMemoryInGb`
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Nasazení do cíle

Nasazení používá konfiguraci nasazení konfigurace konfigurace odvození k nasazení modelů. Proces nasazení je podobný bez ohledu na cíl výpočetního prostředí. Nasazení do AKS se mírně liší, protože je nutné zadat odkaz na cluster AKS.

### <a name="choose-a-compute-target"></a>Výběr výpočetního cíle

K hostování nasazení webové služby můžete použít následující výpočetní cíle nebo výpočetní prostředky:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Definování konfigurace nasazení

Před nasazením modelu je nutné definovat konfiguraci nasazení. *Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby.* Například při nasazení modelu místně, je nutné zadat port, kde služba přijímá požadavky. Konfigurace nasazení není součástí vstupního skriptu. Používá se k definování charakteristik výpočetního cíle, který bude hostitelem modelu a vstupního skriptu.

Můžete také potřebovat vytvořit výpočetní prostředek, pokud například nemáte ještě azure kubernetes service (AKS) instance přidružené k vašemu pracovnímu prostoru.

Následující tabulka obsahuje příklad vytvoření konfigurace nasazení pro každý výpočetní cíl:

| Cílový výpočetní objekt | Příklad konfigurace nasazení |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Třídy pro místní, Azure Container Instances a AKS `azureml.core.webservice`webové služby lze importovat z :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Zabezpečení nasazení pomocí TLS

Další informace o zabezpečení nasazení webové služby naleznete v tématu [Povolení tls a nasazení](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Místní nasazení

Chcete-li nasadit model místně, musíte mít Docker nainstalován na místním počítači.

#### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace naleznete v dokumentaci k [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)a [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Použití cli

Chcete-li nasadit model pomocí příkazového příkazu, použijte následující příkaz. Nahraďte `mymodel:1` se názvem a verzí registrovaného modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Další informace naleznete v dokumentaci [k nasazení modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Principy stavu služby

Během nasazení modelu se může zobrazit změna stavu služby při jeho plném nasazení.

Následující tabulka popisuje různé stavy služeb:

| Stav webové služby | Popis | Konečný stav?
| ----- | ----- | ----- |
| Přechod | Služba je v procesu nasazení. | Ne |
| Není v pořádku | Služba byla nasazena, ale momentálně je nedostupná.  | Ne |
| Unschedulable | Službu nelze v tuto chvíli nasadit z důvodu nedostatku prostředků. | Ne |
| Failed | Službě se nepodařilo nasadit z důvodu chyby nebo selhání. | Ano |
| V pořádku | Služba je v pořádku a koncový bod je k dispozici. | Ano |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Webová služba výpočetní instance (vývoj/test)

Viz [Nasazení modelu do výpočetní instance Azure Machine Learning](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Instance kontejneru Azure (vývoj a testování)

Viz [Nasazení do instancí kontejnerů Azure](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Služba Azure Kubernetes (vývoj/testování a výroba)

Viz [Nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Testování A/B (řízené zavádění)
Další informace naleznete [v tématu Řízené zavádění modelů ML.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Využívání webových služeb

Každá nasazená webová služba poskytuje koncový bod REST, takže můžete vytvářet klientské aplikace v libovolném programovacím jazyce.
Pokud jste pro svou službu povolili ověřování pomocí klíče, musíte v hlavičce požadavku zadat klíč služby jako token.
Pokud jste povolili ověřování na základě tokenu pro vaši službu, musíte poskytnout Azure Machine Learning JSON Web Token (JWT) jako nosný token v záhlaví požadavku. 

Hlavní rozdíl je, že **klíče jsou statické a mohou být regenerovány ručně**a **tokeny je třeba aktualizovat po vypršení platnosti**. Ověřování na klíči je podporované pro Azure Container Instance a Azure Kubernetes Service nasazené webové služby a ověřování založené na tokenech je k dispozici **jenom** pro nasazení služby Azure Kubernetes Service. Další informace a konkrétní ukázky kódu najdete v [návodu k](how-to-setup-authentication.md#web-service-authentication) ověřování.

> [!TIP]
> Po nasazení služby můžete načíst dokument JSON schématu. Pomocí [vlastnosti swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) z nasazené webové `service.swagger_uri`služby (například) získáte identifikátor URI do souboru Swagger místní webové služby.

### <a name="request-response-consumption"></a>Spotřeba požadavku a odpovědi

Tady je příklad, jak vyvolat službu v Pythonu:
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

Další informace naleznete v [tématu Vytváření klientských aplikací pro využití webových služeb](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schéma webové služby (specifikace OpenAPI)

Pokud jste s nasazením použili automatické generování schématu, můžete získat adresu specifikace OpenAPI pro službu pomocí [vlastnosti swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Například `print(service.swagger_uri)`.) K načtení specifikace použijte požadavek GET nebo otevřete identifikátor URI v prohlížeči.

Následující dokument JSON je příkladem schématu (specifikace OpenAPI) generovaného pro nasazení:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

Další informace naleznete v tématu [OpenAPI specifikace](https://swagger.io/specification/).

Nástroj, který můžete vytvořit klientské knihovny ze specifikace, naleznete v tématu [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Odvození dávky
Cíle Azure Machine Learning Compute jsou vytvořené a spravované azure machine learningem. Můžou se použít pro predikci dávek z kanálů Azure Machine Learning.

Návod dávkového odvození pomocí Azure Machine Learning Compute najdete v tématu [Jak spustit predikce dávek](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Odvození od IoT okraje
Podpora pro nasazení k okraji je ve verzi Preview. Další informace najdete [v tématu Nasazení Azure Machine Learning jako modul IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Aktualizace webových služeb

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Průběžné nasazování modelů

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Rozšíření Machine Learning pro Azure DevOps můžete použít k aktivaci kanálu nasazení, když se v pracovním prostoru Azure Machine Learning zaregistruje nový model strojového učení.

1. Zaregistrujte se k [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), což umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu nebo cloud. (Všimněte si, že Azure kanály není stejný jako [kanály Machine Learning](concept-ml-pipelines.md#compare).)

1. [Vytvořte projekt Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Nainstalujte [rozšíření Machine Learning pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Pomocí připojení služeb nastavte připojení k primárnímu servisnímu objektu k pracovnímu prostoru Azure Machine Learning, abyste měli přístup k artefaktům. Přejděte na nastavení projektu, vyberte **Připojení servisu**a pak vyberte **Správce prostředků Azure**:

    [![Vyberte Správce prostředků Azure](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. V seznamu **Úroveň oboru** vyberte **AzureMLWorkspace**a zadejte zbývající hodnoty:

    ![Vybrat AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Chcete-li průběžně nasazovat model strojového učení pomocí Azure Pipelines, v části kanály vyberte **vydání**. Přidejte nový artefakt a vyberte artefakt **modelu AzureML** a připojení služby, které jste vytvořili dříve. Vyberte model a verzi pro aktivaci nasazení:

    [![Výběr modelu AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Povolte aktivační událost modelu na artefaktu modelu. Když zapnete aktivační událost, pokaždé, když zadaná verze (to znamená nejnovější verze) tohoto modelu je registrována ve vašem pracovním prostoru, azure devops verze kanálu se aktivuje.

    [![Povolení aktivační události modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Další ukázkové projekty a příklady najdete v těchto ukázkových úložištích na GitHubu:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Stažení modelu
Pokud chcete stáhnout model a použít jej ve vlastním prostředí spuštění, můžete tak učinit pomocí následujících příkazů sady SDK / CLI:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Cli:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Náhled) Nasazení modelu bez kódu

Nasazení modelu bez kódu je aktuálně ve verzi preview a podporuje následující architektury strojového učení:

### <a name="tensorflow-savedmodel-format"></a>Formát Tentenzifukového uloženého modelu
Tensorflow modely musí být registrovány ve **formátu SavedModel** pro práci s nasazením modelu bez kódu.

Naleznete [v tomto odkazu](https://www.tensorflow.org/guide/saved_model) informace o tom, jak vytvořit SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modely ONNX

Registrace a nasazení modelu ONNX je podporována pro jakýkoli odvozený graf ONNX. Kroky předběžného zpracování a následného zpracování nejsou aktuálně podporovány.

Zde je příklad, jak zaregistrovat a nasadit model MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Pokud používáte Pytorch, [export modelů z PyTorch do ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) má podrobnosti o konverzi a omezení. 

### <a name="scikit-learn-models"></a>Scikit-učit modely

Pro všechny předdefinované typy modelů scikit-learn není podporováno žádné nasazení modelu kódu.

Zde je příklad, jak zaregistrovat a nasadit model sklearn bez dalšího kódu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Poznámka: Modely, které podporují predict_proba bude používat tuto metodu ve výchozím nastavení. Chcete-li přepsat toto použití předpovědět můžete upravit tělo POST, jak je uvedeno níže:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Poznámka: Tyto závislosti jsou zahrnuty v předem sestavené sklearn odvození kontejneru:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modely balíčků

V některých případech můžete chtít vytvořit image Dockeru bez nasazení modelu (pokud například plánujete [nasazení do služby Azure App Service).](how-to-deploy-app-service.md) Nebo můžete chtít stáhnout bitovou kopii a spustit ji v místní instalaci Dockeru. Můžete dokonce chtít stáhnout soubory použité k vytvoření bitové kopie, zkontrolovat je, upravit je a vytvořit bitovou kopii ručně.

Model balení vám umožní dělat tyto věci. Sbalí všechny prostředky potřebné k hostování modelu jako webové služby a umožňuje stáhnout si buď plně sestavenou bitovou kopii Dockeru, nebo soubory potřebné k jeho vytvoření. Modelové balení lze použít dvěma způsoby:

**Stáhněte si zabalený model:** Stáhněte si bitovou kopii Dockeru, která obsahuje model a další soubory potřebné k hostování jako webová služba.

**Generovat soubor Dockerfile:** Stáhněte si dockerfile, model, vstupní skript a další datové zdroje potřebné k vytvoření image Dockeru. Potom můžete zkontrolovat soubory nebo provést změny před sestavením bitové kopie místně.

Oba balíčky lze získat místní image Dockeru.

> [!TIP]
> Vytvoření balíčku je podobné nasazení modelu. Používáte registrovaný model a konfiguraci odvození.

> [!IMPORTANT]
> Chcete-li stáhnout plně vytvořenou bitovou kopii nebo vytvořit bitovou kopii místně, musíte mít [Docker](https://www.docker.com) nainstalovaný ve vývojovém prostředí.

### <a name="download-a-packaged-model"></a>Stažení baleného modelu

Následující příklad vytvoří bitovou kopii, která je registrovaná v registru kontejnerů Azure pro váš pracovní prostor:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po vytvoření balíčku můžete použít `package.pull()` k vyžádat image do místního prostředí Dockeru. Na výstupu tohoto příkazu se zobrazí název obrázku. Například: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po stažení modelu použijte `docker images` příkaz pro seznam místních obrázků:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Chcete-li spustit místní kontejner založený na této bitové kopii, použijte následující příkaz ke spuštění pojmenovaného kontejneru z prostředí nebo příkazového řádku. Nahraďte hodnotu `<imageid>` ID obrázku vráceným příkazem. `docker images`

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Tento příkaz spustí nejnovější verzi `myimage`obrázku s názvem . Mapuje místní port 6789 do portu v kontejneru, na kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat `http://localhost:6789/score`žádosti společnosti .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generování souboru Dockerfile a závislostí

Následující příklad ukazuje, jak stáhnout Dockerfile, model a další prostředky potřebné k vytvoření image místně. Parametr `generate_dockerfile=True` označuje, že chcete soubory, nikoli plně sestavenou bitovou kopii.

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

Tento kód stáhne soubory potřebné k `imagefiles` vytvoření bitové kopie do adresáře. Dockerfile zahrnuty v uložených souborů odkazuje na základní image uložené v registru kontejneru Azure. Při vytváření bitové kopie v místní instalaci Dockeru je třeba k ověření v registru použít adresu, uživatelské jméno a heslo. Pomocí následujících kroků vytvořte bitovou kopii pomocí místní instalace Dockeru:

1. Z relace prostředí nebo příkazového řádku použijte následující příkaz k ověření Dockeru pomocí registru kontejnerů Azure. Nahraďte `<address>`, `<username>`a `<password>` s `package.get_container_registry()`hodnotami načtenými .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Chcete-li vytvořit bitovou kopii, použijte následující příkaz. Nahraďte `<imagefiles>` cestou k `package.save()` adresáři, do kterého byly uloženy soubory.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Tento příkaz nastaví `myimage`název obrázku na .

Chcete-li ověřit, zda je `docker images` obraz vytvořen, použijte příkaz. Měli byste `myimage` vidět obrázek v seznamu:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Chcete-li spustit nový kontejner založený na této bitové kopii, použijte následující příkaz:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Tento příkaz spustí nejnovější verzi `myimage`obrázku s názvem . Mapuje místní port 6789 do portu v kontejneru, na kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat `http://localhost:6789/score`žádosti společnosti .

### <a name="example-client-to-test-the-local-container"></a>Příklad klienta pro testování místního kontejneru

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

Například klienti v jiných programovacích jazycích, naleznete v [tématu využívat modely nasazené jako webové služby](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zastavení kontejneru Dockeru

Chcete-li kontejner zastavit, použijte následující příkaz z jiného prostředí nebo příkazového řádku:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nasazenou `service.delete()`webovou službu, použijte .
Chcete-li odstranit registrovaný `model.delete()`model, použijte .

Další informace naleznete v dokumentaci k [webservice.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) a [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Vytváření rozšířeného vstupního skriptu

<a id="binary"></a>

### <a name="binary-data"></a>Binární data

Pokud váš model přijímá binární data, jako je `score.py` bitová kopie, je nutné upravit soubor použitý pro vaše nasazení přijímat nezpracované požadavky HTTP. Chcete-li přijmout nezpracovaná data, použijte třídu `AMLRequest` v vstupním skriptu `@rawhttp` a přidejte decorator do `run()` funkce.

Zde je `score.py` příklad, který přijímá binární data:

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
> Třída `AMLRequest` je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění, když pracujeme na zlepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za náhled, který není plně podporován společností Microsoft.
>
> Pokud potřebujete otestovat v místním vývojovém prostředí, můžete nainstalovat součásti pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Sdílení zdrojů mezi zdroji (CORS)

Sdílení prostředků mezi zdroji je způsob, jak povolit prostředky na webové stránce, které mají být požadovány z jiné domény. CORS funguje přes hlavičky HTTP odeslané s požadavkem klienta a vrácené s odpovědí služby. Další informace o CORS a platných záhlavích najdete v [tématu Sdílení prostředků mezi zdroji](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) ve Wikipedii.

Chcete-li nakonfigurovat nasazení modelu pro `AMLResponse` podporu CORS, použijte třídu v vstupním skriptu. Tato třída umožňuje nastavit záhlaví na objekt odpovědi.

Následující příklad nastaví `Access-Control-Allow-Origin` záhlaví odpovědi ze vstupního skriptu:

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
> Třída `AMLResponse` je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění, když pracujeme na zlepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za náhled, který není plně podporován společností Microsoft.
>
> Pokud potřebujete otestovat v místním vývojovém prostředí, můžete nainstalovat součásti pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning bude směrovat pouze požadavky POST a GET do kontejnerů, které spouštějí službu vyhodnocování. To může způsobit chyby způsobené prohlížečů pomocí požadavků možnosti před-letu CORS požadavky.
> 

## <a name="next-steps"></a>Další kroky

* [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Využití modelu Azure Machine Learning nasazeného jako webová služba](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)
* [Vytváření upozornění a aktivačních událostí pro nasazení modelu](how-to-use-event-grid.md)

