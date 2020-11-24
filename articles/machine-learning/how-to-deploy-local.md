---
title: Jak spouštět a nasazovat místně
titleSuffix: Azure Machine Learning
description: Naučte se spouštět školený modely na místním počítači.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549973"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Nasazení na modelech místních počítačů, které jsou vyškolené pomocí Azure Machine Learning

V tomto článku se naučíte, jak používat místní počítač jako cíl pro školení nebo nasazení modelů vytvořených v Azure Machine Learning. Flexibilita Azure Machine Learning umožňuje IT pracovat s většinou architektur strojového učení pro Python. Řešení strojového učení mají obecně složité závislosti, které je obtížné duplikovat. V tomto článku se dozvíte, jak obchodovat s celkovými náklady na kontrolu a jejich snadné použití.

Mezi scénáře pro místní nasazení patří:

* Rychlá iterace dat, skriptů a modelů v počátečním projektu
* Ladění a řešení potíží v pozdějších fázích
* Konečné nasazení na hardware spravovaný uživatelem

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md) .
- Model a prostředí. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](tutorial-train-models-with-aml.md) .
- [Sada SDK (Azure Machine Learning Software Development Kit) pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Conda Manager, jako je Anaconda nebo miniconda, pokud chcete zrcadlit závislosti balíčku Azure Machine Learning
- Docker, pokud chcete použít kontejnerové verze Azure Machine Learning prostředí

## <a name="prepare-your-local-machine"></a>Příprava místního počítače

Nejspolehlivější způsob, jak místně spustit model Azure Machine Learning, je s imagí Docker. Image Docker poskytuje izolované, kontejnerové prostředí s duplicitami, s výjimkou potíží s hardwarem a prostředím pro provádění Azure. Další informace o instalaci a konfiguraci Docker pro vývojové scénáře najdete v tématu [Přehled vzdáleného vývoje Docker ve Windows](/windows/dev-environment/docker/overview).

I když je možné připojit ladicí program k procesu běžícímu v Docker (viz [připojit ke spuštěnému kontejneru](https://code.visualstudio.com/docs/remote/attach-container)), můžete chtít ladit a iterovat kód Pythonu bez zahrnutí Docker. V tomto scénáři je důležité, aby váš místní počítač používal stejné knihovny, které se používají při spuštění experimentu v Azure Machine Learning. Pro správu závislostí Pythonu Azure používá [conda](https://docs.conda.io/). I když je možné prostředí znovu vytvořit pomocí jiných správců balíčků, je instalace a konfigurace conda na místním počítači nejjednodušší způsob, jak se synchronizovat. 

## <a name="prepare-your-entry-script"></a>Příprava skriptu pro vložení

I když použijete Docker ke správě modelu a závislostí, musí být skript bodování Pythonu místní. Skript musí mít dvě metody:

- `init()`Metoda, která nepřijímá žádné argumenty a vrací hodnotu Nothing 
- `run()`Metoda, která přebírá řetězec ve formátu JSON a vrací objekt serializovatelný pomocí JSON.

Argument `run()` metody bude ve formátu: 

```json
{
    "data": <model-specific-data-structure>
}
```

Objekt, který vrátíte z `run()` metody, musí implementovat `toJSON() -> string` .

Následující příklad ukazuje, jak načíst registrovaný model scikit-učení a určit jeho skóre pomocí dat numpy. Tento příklad je založený na modelu a závislostech [tohoto kurzu](tutorial-train-models-with-aml.md):

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

Pro pokročilejší příklady, včetně automatického generování schémat Swagger a způsobu, jak určit data binárních dat (například imagí), si přečtěte [pokročilý záznam pro vytváření skriptů](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Nasazení jako místní webové služby pomocí Docker

Nejjednodušší způsob, jak replikovat prostředí používané nástrojem Azure Machine Learning, je nasadit webovou službu pomocí Docker. S Docker spuštěným na místním počítači budete:

1. Připojte se k pracovnímu prostoru Azure Machine Learning, ve kterém je model registrovaný.
1. Vytvoření `Model` objektu představujícího model
1. Vytvořte `Environment` objekt, který obsahuje závislosti a definuje softwarové prostředí, ve kterém bude kód spuštěn.
1. Vytvořte `InferenceConfig` objekt, který přidruží skript vstupu a `Environment`
1. Vytvoření `DeploymentConfiguration` objektu podtřídy `LocalWebserviceDeploymentConfiguration`
1. Slouží `Model.deploy()` k vytvoření `Webservice` objektu. Tato metoda stáhne bitovou kopii Docker a přidruží ji k `Model` , `InferenceConfig` a. `DeploymentConfiguration`
1. Aktivovat `Webservice` s `Webservice.wait_for_deployment()`

Následující kód ukazuje tyto kroky:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Volání `Model.deploy()` může trvat několik minut. Po počátečním nasazení je efektivnější použít `update()` metodu místo začátku od nuly. Viz [aktualizace nasazené webové služby](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testování místního nasazení

Po spuštění předchozího skriptu nasazení bude výstupem identifikátor URI, na který můžete vystavit data pro bodování (například `http://localhost:6789/score` ). Následující příklad ukazuje skript, který vyhodnotí ukázková data s `"sklearn-mnist-local"` lokálně nasazeným modelem. Model, pokud je správně vyškolený, odvodí, který `normalized_pixel_values` by měl být interpretován jako "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Stažení a spuštění modelu přímo

Při použití Docker k nasazení modelu jako webové služby je nejběžnější možnost, že budete chtít spustit kód přímo pomocí místních skriptů Pythonu. Budete potřebovat dva důležité prvky: 

- samotný model,
- Závislosti, na které model spoléhá 

Stažení modelu lze provést:  

- Na portálu vyberte kartu **modely** , vyberte požadovaný model a na stránce **Podrobnosti** zvolte **Stáhnout** .
- Z příkazového řádku pomocí příkazu `az ml model download` (viz [reference ke stažení modelu](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- Se sadou Python SDK pomocí `Model.download()` metody (viz [Referenční dokumentace rozhraní API modelu](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

Model Azure je jeden nebo víc serializovaných objektů Pythonu, které se zabalí jako soubor s rozevíracími seznamy Pythonu (přípona **. pkl** ). Obsah souboru rozevíracího seznamu závisí na knihovně ML nebo technice použité ke výukě modelu. Například s modelem z kurzu můžete model načítat pomocí:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Závislosti jsou vždy obtížné získat právo, zejména pomocí strojového učení, kde často může být dizzying web s požadavky na konkrétní verzi. Azure Machine Learning prostředí můžete na místním počítači znovu vytvořit buď jako kompletní prostředí Conda, nebo jako image Docker pomocí `Environment` `build_local()` metody třídy. 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

Pokud nastavíte `build_local()` argument `useDocker` na `True` , funkce vytvoří obrázek Docker, nikoli conda prostředí. Pokud chcete více ovládacích prvků, můžete použít `Environment` `save_to_directory()` metodu, která zapisuje **conda_dependencies. yml** a **azureml_environment.jsv** definičních souborech, které můžete vyladit a použít jako základ pro rozšíření. 

Tato `Environment` Třída obsahuje řadu jiných metod pro synchronizaci prostředí napříč výpočetním hardwarem, pracovní prostor Azure a image Docker. Další informace najdete v referenčních informacích k [ `Environment` rozhraní API](/python/api/azureml-core/azureml.core.environment(class)).

Po stažení modelu a vyřešení jeho závislostí neexistují žádná omezení definovaná v Azure, která vám pomohou při vyhodnocování, vyladění modelu, použití učení přenosu a tak dále. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Nahrajte převlakový model do Azure Machine Learning

Pokud máte místně vyškolený nebo přeučený model, můžete ho zaregistrovat v Azure. Po registraci můžete pokračovat ve vyladění pomocí Azure COMPUTE nebo ho nasadit pomocí zařízení Azure, jako je [Služba Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) nebo [Server odvození Triton (Preview)](how-to-deploy-with-triton.md).

Aby bylo možné použít se sadou SDK Azure Machine Learning Pythonu, model musí být uložen jako serializovaný objekt Pythonu ve formátu rozevíracího seznamu (soubor **pkl** ) a musí implementovat `predict(data)` metodu, která vrací objekt serializovatelný pomocí JSON. Můžete například uložit místně vyškolený model scikit-učení diabetes s: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Aby byl model dostupný v Azure, můžete použít `register()` metodu `Model` třídy:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Nově registrovaný model pak můžete najít na kartě **model** Azure Machine Learning:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Snímek obrazovky karty modelu Azure Machine Learning zobrazující nahraný model":::

Další informace o nahrávání a aktualizaci modelů a prostředí najdete v tématu [registrace modelu a nasazení místně s využitím pokročilého použití](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Další kroky

- Další informace o správě prostředí najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](how-to-use-environments.md)
- Další informace o přístupu k datům z úložiště dat najdete v tématu [připojení ke službám úložiště v Azure](how-to-access-data.md) .