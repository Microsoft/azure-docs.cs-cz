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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 796118999041b2bef2d51657901e9e399578e97c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327040"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Naučte se, jak nasadit model strojového učení jako webovou službu v cloudu Azure nebo IoT Edge zařízení. 

Pracovní postup je podobný bez ohledu na [to, kam](#target) model nasazujete:

1. Zaregistrujte model.
1. Příprava na nasazení (určení prostředků, využití, cíl výpočtů)
1. Model nasaďte do cílového výpočetního prostředí.
1. Otestujte nasazený model, označovaný také jako webová služba.

Další informace o konceptech, které jsou součástí pracovního postupu nasazení, najdete v tématu [Správa, nasazení a monitorování modelů pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

- Model. Pokud nemáte školený model, můžete použít soubory závislostí & modelu, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)nebo [rozšíření Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a>Registrace modelu

Registrovaný logický kontejner modelu pro jeden nebo více souborů, které tvoří model. Například pokud máte model, který je uložený ve více souborech, můžete ho zaregistrovat jako jeden model v pracovním prostoru. Po registraci můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které byly zaregistrovány.

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo může pocházet z někde jinde. Následující příklady ukazují, jak registrovat model ze souboru:

### <a name="register-a-model-from-an-experiment-run"></a>Registrace modelu z Experimentového běhu

+ **Scikit – příklad použití sady SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na adresář, který obsahuje soubory.

+ **Použití rozhraní příkazového řádku**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na adresář, který obsahuje soubory.

+ **Použití VS Code**

  Zaregistrujte modely pomocí všech souborů modelů nebo složek s rozšířením [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-an-externally-created-model"></a>Registrace externě vytvořeného modelu

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Externě vytvořený model můžete zaregistrovat zadáním **místní cesty** k modelu. Můžete zadat buď složku, nebo jeden soubor.

+ **ONNX příklad se sadou Python SDK:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na adresář, který obsahuje soubory.

+ **Použití rozhraní příkazového řádku**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na adresář, který obsahuje soubory.

**Časový odhad**: Přibližně 10 sekund.

Další informace najdete v tématu v referenční dokumentaci [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely vyškolenými mimo Azure Machine Learning službu najdete v tématu [Jak nasadit existující model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

K hostování nasazení webové služby lze použít následující výpočetní cíle nebo výpočetní prostředky. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Příprava nasazení

Chcete-li nasadit jako webovou službu, je nutné vytvořit odvozenou konfiguraci (`InferenceConfig`) a konfiguraci nasazení. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. V konfiguraci odvození můžete určit skripty a závislosti potřebné k obsluze modelu. V konfiguraci nasazení zadáte podrobné informace o tom, jak řídit model na výpočetním cíli.

> [!IMPORTANT]
> Sada Azure Machine Learning SDK neposkytuje způsob, jak webové služby nebo IoT Edge nasazení získat přístup k úložišti dat nebo datovým sadám. Pokud potřebujete nasadit model pro přístup k datům uloženým mimo nasazení, jako je například v účtu Azure Storage, je nutné vyvinout vlastní řešení kódu pomocí příslušné sady SDK. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python).
>
> Další alternativou, která může fungovat pro váš scénář, je [Batch předpovědi](how-to-run-batch-predictions.md), která poskytuje přístup k úložišti dat při bodování.

### <a id="script"></a> 1. Definování vstupního skriptu & závislosti

Skript vstupu přijímá data odeslaná do nasazené webové služby a předává je do modelu. Pak vezme odpověď vrácenou modelem a vrátí ji klientovi. **Skript je specifický pro váš model**; musí pochopit data, která model očekává a vrátí.

Skript obsahuje dvě funkce, které načítají a spouštějí model:

* `init()`: Tato funkce obvykle načte model do globálního objektu. Tato funkce je spuštěna pouze jednou při spuštění kontejneru Docker pro vaši webovou službu.

* `run(input_data)`: Tato funkce používá model k předpovědi hodnoty založené na vstupních datech. Vstupy a výstupy do běhu obvykle používají JSON pro serializaci a deserializaci. Můžete také pracovat s nezpracovanými binárními daty. Data můžete transformovat před odesláním do modelu nebo před návratem do klienta.

#### <a name="what-is-getmodelpath"></a>Co je get_model_path?

Při registraci modelu zadáte název modelu, který se používá pro správu modelu v registru. Tento název použijete s [modelem. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pro načtení cesty k souborům modelů v místním systému souborů. Pokud zaregistrujete složku nebo kolekci souborů, toto rozhraní API vrátí cestu k adresáři, který obsahuje tyto soubory.

Při registraci modelu mu dáte název, který odpovídá umístění modelu, a to buď místně, nebo během nasazování služby.

Následující příklad vrátí cestu k jednomu souboru s názvem `sklearn_mnist_model.pkl` (který byl registrován s názvem `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>Volitelné Automatické generování schématu Swagger

Chcete-li automaticky vygenerovat schéma pro webovou službu, poskytněte vzorek vstupu a/nebo výstupu v konstruktoru pro jeden z definovaných objektů typu a typ a vzorek slouží k automatickému vytvoření schématu. Služba Azure Machine Learning pak vytvoří specifikaci [openapi](https://swagger.io/docs/specification/about/) (Swagger) pro webovou službu během nasazování.

V současné době jsou podporovány následující typy:

* `pandas`
* `numpy`
* `pyspark`
* standardní objekt Pythonu

Chcete-li použít generování schématu, `inference-schema` zahrňte balíček do vašeho souboru prostředí conda. Následující příklad používá `[numpy-support]` , protože vstupní skript používá typ parametru numpy: 

#### <a name="example-dependencies-file"></a>Příklad souboru závislostí
Následující YAML je příkladem souboru závislostí conda pro odvození.

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

> [!TIP]
> Po nasazení služby použijte `swagger_uri` vlastnost k načtení dokumentu JSON schématu.

#### <a name="example-entry-script"></a>Ukázkový skript vstupu

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Ukázkový skript se vstupem ze slovníku (podpora spotřeby z Power BI)

Následující příklad ukazuje, jak definovat vstupní data jako < Key: Value > Dictionary pomocí dataframe. Tato metoda je podporovaná pro využívání nasazené webové služby od Power BI (další[informace o tom, jak používat webovou službu z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
* Bodování proti binárním datům: [Jak využívat webovou službu](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definování InferenceConfig

Konfigurace odvození popisuje, jak nakonfigurovat model pro vytvoření předpovědi. Následující příklad ukazuje, jak vytvořit odvozenou konfiguraci. Tato konfigurace určuje modul runtime, skript vstupu a (volitelně) soubor prostředí conda:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Další informace najdete v referenčních informacích ke třídě [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Příklad rozhraní příkazového řádku InferenceConfig

Následující dokument JSON je příkladem odvození konfigurace pro použití s rozhraním příkazového řádku Machine Learning:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

V tomto souboru jsou platné následující entity:

* __entryScript__: Cesta k místnímu souboru, který obsahuje kód, který se má pro obrázek spustit.
* __modul runtime__: Který modul runtime má být použit pro bitovou kopii. Aktuální podporované moduly runtime jsou Spark-py a Python.
* __condaFile__ (volitelné): Cesta k místnímu souboru obsahujícímu definici prostředí Conda, která se má použít pro bitovou kopii
* __extraDockerFileSteps__ (volitelné): Cesta k místnímu souboru, který obsahuje další kroky Docker, které se mají spustit při nastavování image
* __sourceDirectory__ (volitelné): Cesta ke složkám, které obsahují všechny soubory pro vytvoření bitové kopie.
* __enableGpu__ (volitelné): Určuje, jestli se má v imagi povolit podpora GPU. Image GPU se musí používat v Microsoft Azurech službách, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Výchozí hodnota je false.
* __baseImage__ (volitelné): Vlastní image, která se má použít jako základní image Pokud není zadána žádná základní image, použije se na základě zadaného parametru runtime základní image.
* __baseImageRegistry__ (volitelné): Registr imagí, který obsahuje základní image.
* __cudaVersion__ (volitelné): Verze CUDA, která se má nainstalovat pro image, které potřebují podporu GPU Image GPU se musí používat v Microsoft Azurech službách, jako je Azure Container Instances, Azure Machine Learning COMPUTE, Azure Virtual Machines a Azure Kubernetes Service. Podporované verze jsou 9,0, 9,1 a 10,0. Pokud je nastavená možnost enable_gpu, použije se výchozí hodnota 9,1.

Tyto entity se mapují na parametry pro třídu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Thee následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace obsahuje následující položky:

* Adresář, který obsahuje prostředky potřebné k odvození
* Tento model vyžaduje Python.
* [Skript vstupu](#script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby
* Soubor Conda, který popisuje balíčky Pythonu potřebné k odvození

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definování konfigurace nasazení

Před nasazením musíte definovat konfiguraci nasazení. Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby. Například při nasazování místně musíte zadat port, kam služba přijímá požadavky.

Může být také potřeba vytvořit výpočetní prostředek. Například pokud ještě nemáte službu Azure Kubernetes přidruženou k vašemu pracovnímu prostoru.

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílové výpočetní prostředí | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instance kontejneru Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Následující části ukazují, jak vytvořit konfiguraci nasazení a pak ji použít k nasazení webové služby.

### <a name="optional-profile-your-model"></a>Volitelné: Profilace modelu

Před nasazením modelu jako služby ho můžete profilovat a určit optimální požadavky na procesor a paměť pomocí sady SDK nebo rozhraní příkazového řádku.  Výsledky profilování modelu jsou generovány jako `Run` objekt. Úplné podrobnosti [schématu profilu modelu najdete v dokumentaci k rozhraní API](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

Další informace o [tom, jak profilovat model pomocí sady SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-).

K profilaci modelu pomocí rozhraní příkazového řádku použijte příkaz [AZ ml model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

## <a name="deploy-to-target"></a>Nasadit do cíle

### <a id="local"></a>Místní nasazení

K místnímu nasazení musíte mít v místním počítači **nainstalovaný Docker** .

+ **Používání sady SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Použití rozhraní příkazového řádku**

    Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Položky v `deploymentconfig.json` dokumentu jsou mapovány na parametry pro [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

    | Entita JSON | Parametr metody | Popis |
    | ----- | ----- | ----- |
    | `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro místní musí být `local`hodnota. |
    | `port` | `port` | Místní port, na kterém má být vystaven koncový bod HTTP služby. |

    Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Použití Azure Container Instances pro nasazení modelů jako webové služby, pokud jeden nebo více z následujících podmínek je splněných:
- Potřebujete k rychlému nasazení a ověření modelu.
- Testování modelu, který je ve vývoji. 

Pokud chcete zobrazit dostupnost kvót a oblastí pro ACI, přečtěte si článek [kvóty a dostupnost oblasti pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

+ **Používání sady SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Použití rozhraní příkazového řádku**

    Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    Položky v `deploymentconfig.json` dokumentu jsou mapovány na parametry pro [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

    | Entita JSON | Parametr metody | Popis |
    | ----- | ----- | ----- |
    | `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro ACI musí být `ACI`hodnota. |
    | `containerResourceRequirements` | Není k dispozici | Obsahuje prvky konfigurace pro procesor a paměť přidělené kontejneru. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které se mají přidělit této webové službě. Upravovaný`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB), která má být přidělena této webové službě. Výchozí`0.5` |
    | `location` | `location` | Oblast Azure, do které se má tato webová služba nasadit Pokud není zadáno, bude použito umístění pracovního prostoru. Další podrobnosti o dostupných oblastech najdete tady: [ACI oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | Určuje, jestli se má pro tuto webovou službu povolit ověřování. Výchozí hodnota je false. |
    | `sslEnabled` | `ssl_enabled` | Určuje, jestli se má pro tuto webovou službu povolit protokol SSL. Výchozí hodnota je false. |
    | `appInsightsEnabled` | `enable_app_insights` | Určuje, jestli se má pro tuto webovou službu povolit AppInsights. Výchozí hodnota je false. |
    | `sslCertificate` | `ssl_cert_pem_file` | Soubor certifikátu potřebný, pokud je povolený protokol SSL |
    | `sslKey` | `ssl_key_pem_file` | Soubor klíče potřebný v případě, že je povolený protokol SSL |
    | `cname` | `ssl_cname` | Záznam CNAME pro, pokud je povolený protokol SSL |
    | `dnsNameLabel` | `dns_name_label` | Popisek názvu DNS pro koncový bod bodování. Pokud není zadán, bude pro koncový bod bodování vygenerován jedinečný popisek názvu DNS. |

    Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **Použití VS Code**

  K [nasazení modelů pomocí vs Code](how-to-vscode-tools.md#deploy-and-manage-models) nemusíte vytvářet kontejner ACI k testování předem, protože kontejnery ACI se vytvářejí za běhu.

Další informace najdete v tématu v referenční dokumentaci [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) třídy.

### <a id="aks"></a>Služba Azure Kubernetes (DEVTEST & produkce)

Můžete použít existující cluster AKS, nebo vytvořte novou pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.

<a id="deploy-aks"></a>

Pokud již máte připojený cluster AKS, můžete do něj nasadit. Pokud jste nevytvořili nebo nepřipojili cluster AKS, postupujte podle kroků v části <a href="#create-attach-aks">Vytvoření nového clusteru AKS</a>.

+ **Používání sady SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Použití rozhraní příkazového řádku**

    Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `myaks` názvem výpočetního cíle AKS. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    Položky v `deploymentconfig.json` dokumentu jsou mapovány na parametry pro [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). Následující tabulka popisuje mapování mezi entitami v dokumentu JSON a parametry pro metodu:

    | Entita JSON | Parametr metody | Popis |
    | ----- | ----- | ----- |
    | `computeType` | Není k dispozici | Cílové výpočetní prostředí. Pro AKS musí být `aks`hodnota. |
    | `autoScaler` | Není k dispozici | Obsahuje prvky konfigurace pro automatické škálování. Podívejte se na tabulku automatického škálování. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Určuje, zda má být povoleno automatické škálování webové služby. Pokud `numReplicas` ,;v =  opačném`False`případě. `0` `True` |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimální počet kontejnerů, které se mají použít při automatickém škálování této webové služby. Výchozí hodnota `1`,. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maximální počet kontejnerů, které se mají použít při automatickém škálování této webové služby. Výchozí hodnota `10`,. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak často se automatické škálování pokusí škálovat tuto webovou službu. Výchozí hodnota `1`,. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Cílové využití (v procentech z 100), které by měl modul automatického škálování zkusit zachovat pro tuto webovou službu. Výchozí hodnota `70`,. |
    | `dataCollection` | Není k dispozici | Obsahuje prvky konfigurace pro shromažďování dat. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Určuje, jestli se má pro webovou službu povolit shromažďování dat modelu. Výchozí hodnota `False`,. |
    | `authEnabled` | `auth_enabled` | Určuje, zda má být povoleno ověřování webové služby. Výchozí hodnota `True`,. |
    | `containerResourceRequirements` | Není k dispozici | Obsahuje prvky konfigurace pro procesor a paměť přidělené kontejneru. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | Počet jader procesoru, které se mají přidělit této webové službě. Upravovaný`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | Velikost paměti (v GB), která má být přidělena této webové službě. Výchozí`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Určuje, jestli se má povolit Application Insights protokolování webové služby. Výchozí hodnota `False`,. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | Časový limit pro vykonání volání bodování webové službě. Výchozí hodnota `60000`,. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maximální počet souběžných požadavků na uzel pro tuto webovou službu. Výchozí hodnota `1`,. |
    | `maxQueueWaitMs` | `max_request_wait_time` | Maximální doba, po kterou požadavek zůstane ve frontě Thee (v milisekundách), než se vrátí chyba 503. Výchozí hodnota `500`,. |
    | `numReplicas` | `num_replicas` | Počet kontejnerů, které se mají přidělit této webové službě. Žádná výchozí hodnota. Pokud tento parametr není nastaven, je automatické škálování ve výchozím nastavení povolené. |
    | `keys` | Není k dispozici | Obsahuje prvky konfigurace pro klíče. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | Primární ověřovací klíč, který se má použít pro tuto webovou službu |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | Sekundární ověřovací klíč, který se má použít pro tuto webovou službu |
    | `gpuCores` | `gpu_cores` | Počet jader GPU, které se mají přidělit pro tuto webovou službu. Výchozí hodnota je 1. |
    | `livenessProbeRequirements` | Není k dispozici | Obsahuje prvky konfigurace pro požadavky sondy pro živý provoz. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak často (v sekundách) provést test živého provozu Výchozí hodnota je 10 sekund. Minimální hodnota je 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Počet sekund po zahájení kontejneru, než se iniciují sondy pro živou práci. Výchozí hodnota je 310. |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Počet sekund, po jejichž uplynutí vyprší platnost testu živého provozu. Výchozí hodnota je 2 sekundy. Minimální hodnota je 1. |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Minimální po sobě jdoucí úspěšnost testu za provozu, aby bylo možné považovat za úspěšné po selhání. Výchozí hodnota je 1. Minimální hodnota je 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | Když se spustí pod a sonda živého vysílání selže, Kubernetes se pokusí failureThreshold časy před tím, než zadáte. Výchozí hodnota je 3. Minimální hodnota je 1. |
    | `namespace` | `namespace` | Obor názvů Kubernetes, do kterého je webová služba nasazena. Až 63 malých alfanumerických znaků (' a-z ', ' 0 '-' 9 ') a spojovníky ('-'). První a poslední znak nesmí být spojovníky. |

    Následující kód JSON je příkladem konfigurace nasazení pro použití s rozhraním příkazového řádku:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **Použití VS Code**

  Můžete ho také [nasadit do AKS prostřednictvím rozšíření vs Code](how-to-vscode-tools.md#deploy-and-manage-models), ale budete muset nakonfigurovat clustery AKS předem.

Další informace o nasazení AKS a automatickém škálování najdete v referenčních informacích k [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) .

#### Vytvoření nového clusteru AKS<a id="create-attach-aks"></a>
**Časový odhad**: Přibližně 20 minut.

Vytvoření nebo připojení clusteru AKS je jednorázový proces pro váš pracovní prostor. Tento cluster pro více nasazení můžete znovu použít. Pokud odstraníte cluster nebo skupinu prostředků, která ho obsahuje, musíte při příštím nasazení vytvořit nový cluster. K vašemu pracovnímu prostoru můžete připojit více clusterů AKS.

Pokud chcete vytvořit cluster AKS pro vývoj, ověřování a testování, nastavte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` při použití. [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) Cluster vytvořený pomocí tohoto nastavení bude mít jenom jeden uzel.

> [!IMPORTANT]
> Nastavení `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` vytvoří cluster AKS, který není vhodný pro zpracování produkčního provozu. Časy odvození můžou být delší než u clusteru vytvořeného pro produkční prostředí. Odolnost proti chybám není pro clustery pro vývoj a testování zaručená.
>
> Doporučujeme, aby clustery vytvořené pro vývoj a testování používaly alespoň dva virtuální procesory.

Následující příklad ukazuje, jak vytvořit nový cluster služby Azure Kubernetes:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Další informace o vytvoření clusteru AKS mimo sadu SDK Azure Machine Learning najdete v následujících článcích:
* [Vytvoření clusteru AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Další informace o `cluster_purpose` parametru najdete v referenčních informacích k [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

> [!IMPORTANT]
> Pokud [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)pro vyberete vlastní hodnoty pro agent_count a vm_size, musíte zajistit, aby agent_count vynásobené vm_size je větší nebo rovna 12 virtuálním procesorům. Pokud například použijete vm_size typu "Standard_D3_v2", který má 4 virtuální procesory, měli byste vybrat agent_count z 3 nebo vyšší.
>
> Sada SDK pro Azure Machine Learning neposkytuje podporu škálování clusteru AKS. Pro horizontální navýšení kapacity uzlů v clusteru použijte uživatelské rozhraní pro cluster AKS v Azure Portal. Můžete změnit jenom počet uzlů, nikoli velikost virtuálního počítače v clusteru.

#### <a name="attach-an-existing-aks-cluster"></a>Připojit existující cluster AKS
**Časový odhad:** Přibližně 5 minut.

Pokud už máte v předplatném Azure cluster AKS a je to verze 1.12. # #, můžete ho použít k nasazení image.

> [!WARNING]
> Při připojování clusteru AKS k pracovnímu prostoru můžete definovat, jak budete cluster používat, nastavením `cluster_purpose` parametru.
>
> Pokud parametr nezadáte nebo nastavíte `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, cluster musí mít k dispozici alespoň 12 virtuálních procesorů. `cluster_purpose`
>
> Pokud nastavíte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, cluster nemusí mít 12 virtuálních procesorů. Cluster nakonfigurovaný pro vývoj a testování ale nebude vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození.

Následující kód ukazuje, jak připojit existující cluster AKS 1.12. # # k vašemu pracovnímu prostoru:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Další informace o `attack_configuration()`naleznete v tématu [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) reference.

Další informace o `cluster_purpose` parametru najdete v referenčních informacích k [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

## <a name="consume-web-services"></a>Využívání webových služeb

Každá nasazená webová služba poskytuje REST API, takže můžete vytvářet klientské aplikace v různých programovacích jazycích. Pokud jste povolili ověřování pro službu, musíte v hlavičce žádosti zadat klíč služby jako token.

### <a name="request-response-consumption"></a>Spotřeba požadavků a odpovědí

Tady je příklad, jak volat vaši službu v Pythonu:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Další informace najdete v tématu [vytvoření klientských aplikací pro využívání WebServices](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a>Odvození dávky
Azure Machine Learning výpočetní cíle jsou vytvářeny a spravovány službou Azure Machine Learning. Je možné je použít ke dávkové předpovědi z Azure Machine Learningch kanálů.

Návod pro odvození dávky s Azure Machine Learning COMPUTE najdete v článku [Jak spustit Batch předpovědi](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>Odvození IoT Edge
Podpora pro nasazení na Edge je ve verzi Preview. Další informace najdete v článku [nasazení Azure Machine Learning jako IoT Edge modulu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Aktualizovat webové služby

Při vytváření nového modelu je nutné ručně aktualizovat každou službu, kterou chcete použít pro nový model. Pokud chcete aktualizovat webovou službu, použijte `update` metody. Následující kód ukazuje, jak aktualizovat webovou službu tak, aby používala nový model:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="continuous-model-deployment"></a>Průběžné nasazování modelu 

Modely můžete průběžně nasazovat pomocí rozšíření Machine Learning pro [Azure DevOps](https://azure.microsoft.com/services/devops/). Pomocí rozšíření Machine Learning pro Azure DevOps můžete aktivovat kanál nasazení, když je nový model strojového učení zaregistrován v pracovním prostoru služby Azure Machine Learning. 

1. Zaregistrujte se do [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), který umožňuje průběžnou integraci a doručování vaší aplikace na libovolnou platformu/libovolný Cloud. Azure Pipelines [se liší od kanálů ml](concept-ml-pipelines.md#compare). 

1. [Vytvořte projekt Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instalace [rozšíření Machine Learning pro Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Pomocí __připojení služby__ nastavte připojení instančního objektu k vašemu pracovnímu prostoru služby Azure Machine Learning, abyste měli přístup ke všem artefaktům. Přejděte na nastavení projektu, klikněte na připojení služby a vyberte Azure Resource Manager.

    ![zobrazení-služba-připojení](media/how-to-deploy-and-where/view-service-connection.png) 

1. Definujte AzureMLWorkspace jako __úroveň oboru__ a vyplňte následující parametry.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Abyste mohli model strojového učení průběžně nasazovat pomocí Azure Pipelines, vyberte v části kanály možnost __vydaná verze__. Přidejte nový artefakt, vyberte artefakt modelu AzureML a připojení služby, které jste vytvořili v předchozím kroku. Vyberte model a verzi pro aktivaci nasazení. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Povolte na artefaktu modelu aktivační událost modelu. Zapnutím triggeru pokaždé, když je zadaná verze (tj. nejnovější verze) tohoto modelu se registruje ve vašem pracovním prostoru, aktivuje se kanál verze Azure DevOps. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Ukázkové projekty a příklady najdete [v úložišti MLOps](https://github.com/Microsoft/MLOps) .

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

