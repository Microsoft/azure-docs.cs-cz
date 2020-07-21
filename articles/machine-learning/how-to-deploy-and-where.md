---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning
description: Naučte se, jak a kde nasadit modely Azure Machine Learning, včetně Azure Container Instances, služby Azure Kubernetes, Azure IoT Edge a programovatelné pole brány.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.custom: seoapril2019, tracking-python
ms.openlocfilehash: ee116d668b9c351ecf5b130a39e418a3da8fc053
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536381"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Nasazování modelů pomocí služby Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo Azure IoT Edge zařízení.

Pracovní postup je podobný bez ohledu na [to, kam model nasazujete](#target) :

1. Zaregistrujte model.
1. Připravte nasazení. (Zadejte prostředky, využití, cílový výpočetní objekt.)
1. Nasaďte model do cílového výpočetního objektu.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [sadu Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Následující kód ukazuje, jak se připojit k pracovnímu prostoru Azure Machine Learning pomocí informací uložených v mezipaměti do místního vývojového prostředí:

+ **Použití sady SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Další informace o použití sady SDK pro připojení k pracovnímu prostoru naleznete v dokumentaci [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Použití rozhraní příkazového řádku**

   Při použití rozhraní příkazového řádku použijte `-w` `--workspace-name` parametr nebo k určení pracovního prostoru pro příkaz.

+ **Používání nástroje Visual Studio Code**

   Když použijete Visual Studio Code, vybíráte pracovní prostor pomocí grafického rozhraní. Další informace najdete v tématu [nasazení a Správa modelů](how-to-manage-resources-vscode.md#endpoints) v dokumentaci k rozšíření Visual Studio Code.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](#load-model-files-in-your-entry-script).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Při registraci modelu můžete volitelně zadat metadata o modelu. `tags` `properties` Slovníky a, které použijete pro registraci modelu, lze použít k filtrování modelů.

Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-experiment-run"></a>Registrace modelu z experimentového běhu

Fragmenty kódu v této části ukazují, jak registrovat model z školicího běhu:

> [!IMPORTANT]
> Chcete-li použít tyto fragmenty kódu, musíte předtím provést školicí běh a potřebujete mít přístup k `Run` objektu (příklad sady SDK) nebo hodnotě ID běhu (příklad rozhraní příkazového řádku). Další informace o školicích modelech najdete v tématu [Nastavení výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md).

+ **Použití sady SDK**

  Když použijete sadu SDK k vytvoření výukového modelu, můžete získat buď objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) , nebo objekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) v závislosti na tom, jak jste model využívali. Každý objekt lze použít k registraci modelu vytvořeného spuštěním experimentu.

  + Registrace modelu z `azureml.core.Run` objektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory. Další informace najdete v dokumentaci ke [spuštění. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrace modelu z `azureml.train.automl.run.AutoMLRun` objektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    V tomto příkladu `metric` `iteration` nejsou zadány parametry a, takže iterace s nejlepší primární metrikou bude zaregistrována. `model_id`Hodnota vrácená z běhu se používá místo názvu modelu.

    Další informace najdete v dokumentaci k [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  `--asset-path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na cestu ke složce, která obsahuje soubory.

+ **Používání nástroje Visual Studio Code**

  Pomocí rozšíření [Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) Zaregistrujte modely pomocí libovolných souborů modelů nebo složek.

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat zadáním místní cesty k modelu. Můžete zadat cestu buď složky, nebo jednoho souboru. Tuto metodu můžete použít k registraci modelů vyškolených pomocí Azure Machine Learning a potom ke stažení. Tuto metodu můžete použít také k registraci modelů, které jsou vyškolené mimo Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Používání sady SDK a ONNX**

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

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory.

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na cestu ke složce, která obsahuje soubory.

**Časový odhad**: přibližně 10 sekund.

Další informace naleznete v dokumentaci k [třídě modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely poučenými mimo Azure Machine Learning najdete v tématu [Jak nasadit existující model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Jednoduché koncové body versus více modelů
Azure ML podporuje nasazení jednoho nebo více modelů za jediným koncovým bodem.

Koncové body s více modely používají sdílený kontejner pro hostování více modelů. To pomáhá snižovat režijní náklady, zlepšuje využití a umožňuje zřetězit moduly dohromady do kompletů. Modely, které zadáte ve svém skriptu nasazení, jsou připojené a zpřístupňují se na disku kontejneru obsluhy – můžete je načíst do paměti na vyžádání a skóre na základě konkrétního modelu, který je požadován v době vyhodnocování.

Příklad E2E, který ukazuje, jak použít více modelů za jedním kontejnerovým koncovým bodem, najdete v [tomto příkladu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) .

## <a name="prepare-to-deploy"></a>Příprava nasazení

K nasazení modelu jako služby potřebujete následující komponenty:

* **Definujte odvozené prostředí**. Toto prostředí zapouzdřuje závislosti potřebné ke spuštění modelu pro odvození.
* **Definujte kód bodování**. Tento skript přijímá požadavky, zarovnává požadavky pomocí modelu a vrátí výsledky.
* **Definujte odvozenou konfiguraci**. Konfigurace odvození určuje konfiguraci prostředí, vstupní skript a další součásti potřebné ke spuštění modelu jako služby.

Jakmile budete mít potřebné komponenty, můžete profilovat službu, která bude vytvořena v důsledku nasazení modelu za účelem pochopení požadavků na procesor a paměť.

### <a name="1-define-inference-environment"></a>1. definování prostředí odvození

Konfigurace odvození popisuje, jak nastavit webovou službu, která obsahuje váš model. Používá se později při nasazení modelu.

Odvozená konfigurace používá Azure Machine Learning prostředí k definování závislostí softwaru potřebných pro vaše nasazení. Prostředí umožňují vytvářet, spravovat a opakovaně používat závislosti softwaru vyžadované pro školení a nasazení. Můžete vytvořit prostředí z vlastních souborů závislosti nebo použít jedno z Azure Machine Learning prostředí. Následující YAML je příkladem souboru závislostí conda pro odvození. Všimněte si, že je třeba určit, že se ve službě AzureML-Defaults verze >= 1.0.45 jako závislost PIP, protože obsahuje funkce potřebné k hostování modelu jako webové služby. Pokud chcete použít automatické generování schématu, váš vstupní skript musí také naimportovat `inference-schema` balíčky.

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
> Pokud je vaše závislost k dispozici prostřednictvím conda i PIP (z PyPi), společnost Microsoft doporučuje používat verzi Conda, protože balíčky conda obvykle obsahují předem připravené binární soubory, které zajistí spolehlivější instalaci.
>
> Další informace najdete v tématu [Principy conda a PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Pokud chcete zjistit, jestli je vaše závislost k dispozici prostřednictvím Conda, použijte `conda search <package-name>` příkaz nebo použijte indexy balíčku v [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) a [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Pomocí souboru závislostí můžete vytvořit objekt prostředí a uložit ho do svého pracovního prostoru pro budoucí použití:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. definování kódu bodování

Vstupní skript přijímá data odeslaná do nasazené webové služby a předává je do modelu. Potom vezme odpověď vrácenou modelem a vrátí ji klientovi. *Skript je specifický pro váš model*. Musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce se spustí jenom jednou, když se spustí kontejner Docker pro vaši webovou službu.

* `run(input_data)`: Tato funkce používá model k předpovědi hodnoty založené na vstupních datech. Vstupy a výstupy spuštění obvykle pro serializaci a deserializaci používají JSON. Pracovat můžete také s nezpracovanými binárními daty. Data můžete transformovat před jejich odesláním do modelu nebo před jejich vrácením klientovi.

#### <a name="load-model-files-in-your-entry-script"></a>Načtení souborů modelu ve vstupním skriptu

Existují dva způsoby, jak ve skriptu pro vložení najít modely:
* `AZUREML_MODEL_DIR`: Proměnná prostředí obsahující cestu k umístění modelu.
* `Model.get_model_path`: Rozhraní API, které vrací cestu k souboru modelu pomocí názvu registrovaného modelu.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR je proměnná prostředí vytvořená během nasazování služby. Tuto proměnnou prostředí lze použít k vyhledání umístění nasazených modelů.

V následující tabulce je popsána hodnota AZUREML_MODEL_DIR v závislosti na počtu nasazených modelů:

| Nasazení | Hodnota proměnné prostředí |
| ----- | ----- |
| Jeden model | Cesta ke složce obsahující model |
| Více modelů | Cesta ke složce obsahující všechny modely Modely jsou umístěné podle názvu a verze v této složce ( `$MODEL_NAME/$VERSION` ). |

Během registrace a nasazení modelu jsou modely umístěny v AZUREML_MODEL_DIR cestě a jejich původní názvy souborů jsou zachovány.

Pokud chcete získat cestu k souboru modelu ve vašem vstupním skriptu, slučte proměnnou prostředí s cestou k souboru, který hledáte.

**Příklad jednoduchého modelu**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Příklad více modelů**

V tomto scénáři jsou v pracovním prostoru registrovány dva modely:

* `my_first_model`: Obsahuje jeden soubor ( `my_first_model.pkl` ) a existuje pouze jedna verze ( `1` ).
* `my_second_model`: Obsahuje jeden soubor ( `my_second_model.pkl` ) a existují dvě verze, `1` a `2` .

Po nasazení služby jsou v operaci nasazení k dispozici oba modely:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

V imagi Docker, který hostuje službu, `AZUREML_MODEL_DIR` obsahuje proměnná prostředí adresář, ve kterém se modely nacházejí.
V tomto adresáři se všechny modely nacházejí v cestě k adresáři `MODEL_NAME/VERSION` . Kde `MODEL_NAME` je název registrovaného modelu a `VERSION` je verze modelu. Soubory, které tvoří registrovaný model, jsou uloženy v těchto adresářích.

V tomto příkladu by byly cesty `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` a `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Pomocí tohoto názvu s metodou [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) můžete načíst cestu souboru modelu nebo souborů v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, toto rozhraní API vrátí cestu k adresáři, který obsahuje tyto soubory.

Když zaregistrujete model, přiřadíte mu název. Název odpovídá umístění modelu, a to buď místně, nebo během nasazování služby.

#### <a name="optional-define-model-web-service-schema"></a>Volitelné Definování schématu webové služby modelu

Chcete-li automaticky vygenerovat schéma pro webovou službu, poskytněte vzorek vstupu a/nebo výstupu v konstruktoru pro jeden z definovaných objektů typu. Typ a ukázka slouží k automatickému vytvoření schématu. Azure Machine Learning pak vytvoří specifikace [openapi](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazování.

Tyto typy jsou aktuálně podporovány:

* `pandas`
* `numpy`
* `pyspark`
* Standardní objekt Pythonu

Chcete-li použít generování schématu, zahrňte do `inference-schema` souboru závislostí Open Source balíček. Další informace o tomto balíčku najdete v tématu [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Definujte vstupní a výstupní ukázkové formáty v `input_sample` `output_sample` proměnných a, které reprezentují formáty požadavků a odpovědí webové služby. Tyto ukázky použijte ve funkci vstupu a výstupu dekoratéry na `run()` funkci. Následující scikit příklad používá generování schématu.

##### <a name="example-entry-script"></a>Ukázkový skript vstupu

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

##### <a name="power-bi-compatible-endpoint"></a>Power BI kompatibilní koncový bod 

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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binární data](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. definování konfigurace odvození
    
Následující příklad ukazuje načtení prostředí z pracovního prostoru a jeho následné použití s konfigurací odvození:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození naleznete v dokumentaci třídy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Příklad rozhraní příkazového řádku InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace určuje následující nastavení:

* Model vyžaduje Python.
* [Skript vstupu](#script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby
* Soubor Conda, který popisuje balíčky Pythonu potřebné pro odvození

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (volitelné) profil pro určení využití prostředků

Po zaregistrování modelu a přípravě dalších komponent nezbytných pro příslušné nasazení můžete určit procesor a paměť, které bude nasazená služba potřebovat. Profilace testuje službu, která spouští váš model, a vrací informace, jako je využití CPU, využití paměti a latence odezvy. Poskytuje taky doporučení pro procesor a paměť na základě využití prostředků.

Aby bylo možné profilovat model, budete potřebovat:
* Registrovaný model.
* Odvozená konfigurace založená na vstupním skriptu a definici prostředí pro odvození.
* Tabulková datová sada s jedním sloupcem, kde každý řádek obsahuje řetězec reprezentující ukázková data požadavku.

> [!IMPORTANT]
> V tuto chvíli podporujeme profilaci služeb, které očekávají, že data požadavku jsou řetězcem, například: řetězec serializovaného JSON, text, String serializovaná image atd. Obsah každého řádku datové sady (řetězce) bude vložen do těla požadavku HTTP a odeslán do služby zapouzdřující model pro účely bodování.

Níže je uveden příklad, jak můžete vytvořit vstupní datovou sadu pro profilování služby, která očekává, že data příchozího požadavku obsahují serializované JSON. V tomto případě jsme vytvořili instance 100 na základě datové sady, která má stejný obsah dat požadavků. V reálných scénářích doporučujeme používat větší datové sady obsahující různé vstupy, zejména v případě, že využití prostředků modelu/chování je závislé na vstupu.

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

Jakmile budete mít datovou sadu obsahující ukázková data požadavku připravené, vytvořte odvozenou konfiguraci. Odvozená konfigurace je založena na score.py a definici prostředí. Následující příklad ukazuje, jak vytvořit konfiguraci odvození a spustit profilaci:

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

Následující příkaz ukazuje, jak profilovat model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Chcete-li zachovat informace vrácené profilací, použijte pro model značky nebo vlastnosti. Použití značek nebo vlastností ukládá data s modelem v registru modelu. Následující příklady ukazují přidání nové značky obsahující `requestedCpu` `requestedMemoryInGb` informace a:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Nasadit do cíle

Nasazení používá k nasazení modelů konfiguraci nasazení nasazení konfigurace. Proces nasazení je podobný bez ohledu na cíl výpočtů. Nasazení do služby Azure Kubernetes Service (AKS) se mírně liší, protože musíte poskytnout odkaz na cluster AKS.

### <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

K hostování nasazení webové služby můžete použít následující výpočetní cíle nebo výpočetní prostředky:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

> [!NOTE]
> * ACI je vhodný jenom pro malé modely <1 GB. 
> * Pro vývoj a testování větších modelů doporučujeme použít AKS s jedním uzlem.

### <a name="define-your-deployment-configuration"></a>Definování konfigurace nasazení

Před nasazením modelu je nutné definovat konfiguraci nasazení. *Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby.* Pokud například nasadíte model místně, je nutné zadat port, kam služba přijímá požadavky. Konfigurace nasazení není součástí vašeho skriptu pro vložení. Slouží k definování charakteristik výpočetního cíle, který bude hostovat model a vstupní skript.

Je také možné, že budete muset vytvořit výpočetní prostředek, pokud například ještě nemáte k vašemu pracovnímu prostoru přidruženou instanci služby Azure Kubernetes (AKS).

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílový výpočetní objekt | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Třídy pro místní, Azure Container Instances a webové služby AKS lze importovat z `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Zabezpečení nasazení pomocí TLS

Další informace o tom, jak zabezpečit nasazení webové služby, najdete v tématu [Povolení TLS a nasazení](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Místní nasazení

Pokud chcete model nasadit lokálně, musíte na svém místním počítači nainstalovat Docker.

#### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace najdete v dokumentaci pro [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)a [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit model pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Další informace najdete v dokumentaci [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="understanding-service-state"></a>Princip stavu služby

Během nasazování modelu se může zobrazit Změna stavu služby během plného nasazení.

Následující tabulka popisuje různé stavy služby:

| Stav WebService | Popis | Konečný stav?
| ----- | ----- | ----- |
| Přechod | Služba je v procesu nasazení. | No |
| Není v pořádku | Služba je nasazená, ale v tuto chvíli není dostupná.  | No |
| Unschedulable | Službu nyní nelze nasadit z důvodu nedostatku prostředků. | No |
| Neúspěšný | Nasazení služby se nezdařilo z důvodu chyby nebo selhání. | Ano |
| V pořádku | Služba je v pořádku a koncový bod je k dispozici. | Ano |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Webová služba COMPUTE instance (vývoj/testování)

Přečtěte si téma [nasazení modelu pro Azure Machine Learning výpočetní instance](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure Container Instances (vývoj/testování)

Viz [nasazení na Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Služba Azure Kubernetes (vývoj, testování a produkce)

Viz [nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Testování a/B (řízené zavedení)
Další informace najdete v tématu [řízená zavedení modelů ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) pro další informace.

## <a name="consume-web-services"></a>Využívání webových služeb

Každá nasazená webová služba poskytuje koncový bod REST, takže můžete vytvářet klientské aplikace v libovolném programovacím jazyce.
Pokud jste pro vaši službu povolili ověřování na základě klíčů, musíte v hlavičce žádosti zadat klíč služby jako token.
Pokud jste povolili ověřování na základě tokenů pro vaši službu, musíte v hlavičce žádosti zadat jako nosný token Azure Machine Learning JSON Web Token (JWT). 

Hlavním rozdílem je, že **klíče jsou statické a dají se znovu vygenerovat ručně**a **tokeny je potřeba aktualizovat po vypršení platnosti**. Ověřování založené na klíčích se podporuje pro Azure Container instance a službu Azure Kubernetesed Web-Services a ověřování na základě tokenu je dostupné **jenom** pro nasazení služby Azure Kubernetes. Další informace a konkrétní ukázky kódu najdete v [tématu o ověřování](how-to-setup-authentication.md#web-service-authentication) .

> [!TIP]
> Po nasazení služby můžete načíst dokument JSON schématu. Použijte [vlastnost swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) z nasazené webové služby (například `service.swagger_uri` ) k získání identifikátoru URI do souboru Swagger místní webové služby.

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

Pokud jste s vaším nasazením použili automatické generování schématu, můžete získat adresu specifikace OpenAPI pro službu pomocí [vlastnosti swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Například `print(service.swagger_uri)` .) K načtení specifikace použijte požadavek GET nebo otevřete identifikátor URI v prohlížeči.

Následující dokument JSON je příklad schématu (specifikace OpenAPI) generovaného pro nasazení:

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

Další informace najdete v tématu [specifikace openapi](https://swagger.io/specification/).

Nástroj, který umožňuje vytvářet klientské knihovny ze specifikace, najdete v tématu [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány pomocí Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v tématu [How to run Batch předpovědi](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v tématu [nasazení Azure Machine Learning jako modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Aktualizovat webové služby

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Průběžné nasazování modelů

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Rozšíření Machine Learning pro Azure DevOps můžete použít k aktivaci kanálu nasazení, když je nový model strojového učení zaregistrován v pracovním prostoru Azure Machine Learning.

1. Zaregistrujte se do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), který umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu nebo Cloud. (Všimněte si, že Azure Pipelines nejsou stejné jako [Machine Learning kanály](concept-ml-pipelines.md#compare).)

1. [Vytvořte projekt Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Nainstalujte [Machine Learning rozšíření pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Pomocí připojení služby nastavte připojení instančního objektu k vašemu pracovnímu prostoru Azure Machine Learning, abyste mohli přistupovat ke artefaktům. Přejít na nastavení projektu, vyberte **připojení služby**a pak vyberte **Azure Resource Manager**:

    [![Vyberte Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. V seznamu **úroveň oboru** vyberte možnost **AzureMLWorkspace**a potom zadejte zbývající hodnoty:

    ![Vybrat AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

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

## <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Nasazení modelu bez kódu je momentálně ve verzi Preview a podporuje následující architektury strojového učení:

### <a name="tensorflow-savedmodel-format"></a>Formát Tensorflow SavedModel
Modely Tensorflow je nutné zaregistrovat ve **formátu SavedModel** pro práci s nasazením modelu bez kódu.

V [tomto odkazu](https://www.tensorflow.org/guide/saved_model) najdete informace o tom, jak vytvořit SavedModel.

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

Registrace a nasazení modelu ONNX se podporuje pro libovolný graf odvození ONNX. Kroky předběžného zpracování a postprocess se v tuto chvíli nepodporují.

Tady je příklad, jak zaregistrovat a nasadit model ONNX MNIST ručně zapsaných:

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

Chcete-li určit skóre modelu, přečtěte si téma věnované [využívání modelu Azure Machine Learning nasazeného jako webové služby](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service). Mnoho projektů ONNX používá soubory protobuf k komprimaci dat o školeních a ověřováních, což může být obtížné zjistit, co formát dat očekávala služba. Jako vývojář modelů byste měli dokument pro vaše vývojáře:

* Vstupní formát (JSON nebo binární)
* Vstupní datový tvar a typ (například pole plovoucích tvarů [100100, 3])
* Informace o doméně (například pro obrázek, barevný prostor, pořadí součástí a zda jsou hodnoty normalizovány)

Pokud používáte Pytorch, jsou [exportovány modely z Pytorch na ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) v podrobnostech o převodu a omezeních. 

### <a name="scikit-learn-models"></a>Modely Scikit – informace

Pro všechny integrované typy modelů scikit-učí není podporováno žádné nasazení modelu kódu.

Tady je příklad, jak zaregistrovat a nasadit model skriptu sklearn bez dalšího kódu:

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

Poznámka: modely, které podporují predict_proba použijí tuto metodu ve výchozím nastavení. Pokud chcete tuto možnost přepsat, abyste mohli použít předpověď, můžete tělo příspěvku upravit následujícím způsobem:
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

Poznámka: tyto závislosti jsou zahrnuté v předem sestaveném kontejneru pro odvození scikit:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
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
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Chcete-li spustit místní kontejner na základě tohoto obrázku, použijte následující příkaz pro spuštění pojmenovaného kontejneru z prostředí nebo příkazového řádku. Nahraďte `<imageid>` hodnotu identifikátorem image vráceným `docker images` příkazem.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage` . Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Vygenerovat souboru Dockerfile a závislosti

Následující příklad ukazuje, jak stáhnout souboru Dockerfile, model a další prostředky potřebné k vytvoření image místně. `generate_dockerfile=True`Parametr označuje, že chcete soubory, nikoli plně sestavenou bitovou kopii.

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

1. Z prostředí nebo relace příkazového řádku použijte následující příkaz k ověření Docker pomocí služby Azure Container Registry. Nahradí `<address>` `<username>` hodnoty, a `<password>` s hodnotami načtenými `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. K sestavení image použijte následující příkaz. Nahraďte `<imagefiles>` cestou k adresáři, kam se `package.save()` soubory uložily.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Tento příkaz nastaví název bitové kopie na `myimage` .

Chcete-li ověřit, zda je obrázek sestaven, použijte `docker images` příkaz. V seznamu by se měla zobrazit `myimage` obrázek:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Chcete-li spustit nový kontejner na základě tohoto obrázku, použijte následující příkaz:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Tento příkaz spustí nejnovější verzi image s názvem `myimage` . Mapuje místní port 6789 na port v kontejneru, ve kterém webová služba naslouchá (5001). Také přiřadí název `mycontainer` kontejneru, což usnadňuje zastavení kontejneru. Po spuštění kontejneru můžete odeslat požadavky na `http://localhost:6789/score` .

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

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()` .
Pokud chcete odstranit registrovaný model, použijte `model.delete()` .

Další informace najdete v dokumentaci pro [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) a [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Vytváření pokročilých vstupních skriptů

<a id="binary"></a>

### <a name="binary-data"></a>Binární data

Pokud váš model akceptuje binární data, jako je obrázek, musíte upravit soubor, který se `score.py` používá pro vaše nasazení, aby přijímal nezpracované požadavky HTTP. K přijetí nezpracovaných dat použijte `AMLRequest` třídu ve vašem vstupním skriptu a přidejte `@rawhttp` dekoratér do `run()` funkce.

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
> `AMLRequest`Třída je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`Třída umožňuje přístup k nezpracovaným publikovaným datům v Score.py, neexistuje žádná součást na straně klienta. Z klienta můžete data publikovat jako normální. Například následující kód Python přečte soubor obrázku a odešle data:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Sdílení prostředků mezi zdroji je způsob, jak u prostředků na webové stránce požádat z jiné domény. CORS funguje pomocí hlaviček HTTP odeslaných s požadavkem klienta a vrácených s odpovědí služby. Další informace o CORS a platných hlavičkách najdete v tématu [sdílení prostředků mezi zdroji](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) v Wikipedii.

Pokud chcete nakonfigurovat nasazení modelu tak, aby podporovalo CORS, použijte `AMLResponse` ve svém vstupním skriptu třídu. Tato třída umožňuje nastavit záhlaví objektu Response.

Následující příklad nastaví `Access-Control-Allow-Origin` hlavičku odpovědi z vstupního skriptu:

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse`Třída je v `azureml.contrib` oboru názvů. Entity v tomto oboru názvů se často mění při práci za účelem vylepšení služby. Cokoli v tomto oboru názvů by mělo být považováno za verzi Preview, která není plně podporovaná společností Microsoft.
>
> Pokud potřebujete tuto možnost otestovat ve vašem místním vývojovém prostředí, můžete komponenty nainstalovat pomocí následujícího příkazu:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning bude směrovat pouze požadavky POST a GET do kontejnerů, ve kterých je spuštěna služba bodování. To může způsobit chyby způsobené prohlížeči pomocí požadavků na požadavky na požadavky CORS před lety.
> 

## <a name="next-steps"></a>Další kroky

* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Využívání modelu služby Azure Machine Learning nasazeného jako webová služba](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)

