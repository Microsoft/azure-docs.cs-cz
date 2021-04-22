---
title: Jak spouštět a nasazovat místně
titleSuffix: Azure Machine Learning
description: Tento článek popisuje, jak použít místní počítač jako cíl pro školení, ladění nebo nasazení modelů vytvořených v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861207"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Nasazení modelů vyškolených pomocí Azure Machine Learning na místních počítačích 

Tento článek popisuje, jak použít místní počítač jako cíl pro školení nebo nasazení modelů vytvořených v Azure Machine Learning. Azure Machine Learning je dostatečně flexibilní, aby bylo možné pracovat s většinou architektur strojového učení pro Python. Řešení strojového učení mají obecně složité závislosti, které je obtížné duplikovat. V tomto článku se dozvíte, jak vyrovnávat celkovou kontrolu pomocí snadného použití.

Mezi scénáře pro místní nasazení patří:

* Rychlá iterace dat, skriptů a modelů na začátku projektu.
* Ladění a řešení potíží v pozdějších fázích.
* Konečné nasazení na hardware spravovaný uživatelem.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
- Model a prostředí. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](tutorial-train-models-with-aml.md).
- [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/intro).
- Conda Manager, jako je Anaconda nebo Miniconda, pokud chcete zrcadlit Azure Machine Learning závislosti balíčku.
- Docker, pokud chcete použít kontejnerové verze Azure Machine Learning prostředí.

## <a name="prepare-your-local-machine"></a>Příprava místního počítače

Nejspolehlivější způsob, jak místně spustit model Azure Machine Learning, je s imagí Docker. Image Docker poskytuje izolované, kontejnerové prostředí s duplicitami, s výjimkou potíží s hardwarem a prostředím pro provádění Azure. Další informace o instalaci a konfiguraci Docker pro vývojové scénáře najdete v tématu [Přehled vzdáleného vývoje Docker ve Windows](/windows/dev-environment/docker/overview).

Je možné připojit ladicí program k procesu běžícímu v Docker. (Viz [připojit ke spuštěnému kontejneru](https://code.visualstudio.com/docs/remote/attach-container).) Možná ale dáváte přednost ladění a iterování kódu Pythonu bez nutnosti používat Docker. V tomto scénáři je důležité, aby váš místní počítač používal stejné knihovny, které se používají při spuštění experimentu v Azure Machine Learning. Pro správu závislostí Pythonu Azure používá [conda](https://docs.conda.io/). Prostředí můžete znovu vytvořit pomocí dalších správců balíčků, ale instalaci a konfiguraci conda na místním počítači představuje nejjednodušší způsob, jak se synchronizovat. 

## <a name="prepare-your-entry-script"></a>Příprava skriptu pro vložení

I když použijete Docker ke správě modelu a závislostí, musí být skript bodování Pythonu místní. Skript musí mít dvě metody:

- `init()`Metoda, která nepřijímá žádné argumenty a vrací hodnotu Nothing 
- `run()`Metoda, která přebírá řetězec ve formátu JSON a vrací objekt serializovatelný pomocí JSON

Argument `run()` metody bude v tomto formátu: 

```json
{
    "data": <model-specific-data-structure>
}
```

Objekt, který vrátíte z `run()` metody, musí implementovat `toJSON() -> string` .

Následující příklad ukazuje, jak načíst registrovaný model scikit-učení a určit jeho skóre pomocí dat NumPy. Tento příklad je založen na modelu a závislostech [tohoto kurzu](tutorial-train-models-with-aml.md).

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Pokročilejší příklady, včetně automatického generování schématu Swagger a binárních dat bodování (například Image), najdete v tématu [pokročilé vytváření vstupních skriptů](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Nasazení jako místní webové služby pomocí Docker

Nejjednodušší způsob, jak replikovat prostředí používané nástrojem Azure Machine Learning, je nasadit webovou službu pomocí Docker. S Docker spuštěným na místním počítači budete:

1. Připojte se k pracovnímu prostoru Azure Machine Learning, ve kterém je model zaregistrován.
1. Vytvořte `Model` objekt, který představuje model.
1. Vytvořte `Environment` objekt, který obsahuje závislosti a definuje softwarové prostředí, ve kterém bude váš kód spuštěn.
1. Vytvořte `InferenceConfig` objekt, který přidruží vstupní skript k `Environment` .
1. Vytvořte `DeploymentConfiguration` objekt podtřídy `LocalWebserviceDeploymentConfiguration` .
1. Slouží `Model.deploy()` k vytvoření `Webservice` objektu. Tato metoda stáhne bitovou kopii Docker a přidruží ji k `Model` , `InferenceConfig` a `DeploymentConfiguration` .
1. Aktivujte pomocí `Webservice` `Webservice.wait_for_deployment()` .

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

Volání `Model.deploy()` může trvat několik minut. Po počátečním nasazení webové služby je efektivnější použít `update()` metodu místo začátku od začátku. Viz [aktualizace nasazené webové služby](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testování místního nasazení

Po spuštění předchozího skriptu nasazení bude výstupem identifikátor URI, na který můžete vystavit data pro bodování (například `http://localhost:6789/score` ). Následující příklad ukazuje skript, který vyhodnotí ukázková data pomocí `"sklearn-mnist-local"` lokálně nasazeného modelu. Model, pokud je správně vyškolený, odvodí, který `normalized_pixel_values` by měl být interpretován jako "2". 

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

Použití Docker k nasazení modelu jako webové služby je nejběžnější možnost. Můžete ale chtít spustit kód přímo pomocí místních skriptů Pythonu. Budete potřebovat dvě důležité komponenty: 

- samotný model,
- Závislosti, na které model spoléhá 

Můžete si stáhnout model:  

- Na portálu vyberte kartu **modely** , vyberte požadovaný model a na stránce **Podrobnosti** vyberte **Stáhnout**.
- Z příkazového řádku pomocí `az ml model download` . (Viz [stažení modelu.](/cli/azure/ml/model#az_ml_model_download))
- Pomocí metody Python SDK `Model.download()` . (Viz [třída modelu.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Model Azure je jeden nebo víc serializovaných objektů Pythonu, které se zabalí jako soubor s rozevíracími seznamy Pythonu (přípona. pkl). Obsah souboru rozevíracího seznamu závisí na knihovně strojového učení nebo na technice, která se používá pro výuku modelu. Například pokud používáte model z kurzu, můžete model načíst pomocí:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Závislosti jsou vždy obtížné získat právo, zejména pomocí strojového učení, kde často může být dizzying web s požadavky na konkrétní verzi. Azure Machine Learning prostředí můžete na místním počítači znovu vytvořit buď jako úplné prostředí Conda, nebo jako image Docker pomocí `build_local()` metody `Environment` třídy: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Pokud nastavíte `build_local()` `useDocker` argument na `True` , funkce vytvoří obrázek Docker, nikoli conda prostředí. Pokud chcete více ovládacích prvků, můžete použít `save_to_directory()` metodu `Environment` , která zapisuje conda_dependencies. yml a azureml_environment.jsv definičních souborech, které lze doladit a použít jako základ pro rozšíření. 

Tato `Environment` Třída obsahuje řadu jiných metod pro synchronizaci prostředí napříč výpočetním hardwarem, pracovní prostor Azure a image Docker. Další informace naleznete v tématu [Třída prostředí](/python/api/azureml-core/azureml.core.environment(class)).

Po stažení modelu a vyřešení jeho závislostí neexistují žádná omezení definovaná v Azure, která vám pomohou při vyhodnocování, vyladění modelu, použití učení a tak dále. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Nahrajte převlakový model do Azure Machine Learning

Pokud máte místně vyškolený nebo přeučený model, můžete ho zaregistrovat v Azure. Po registraci můžete pokračovat ve vyladění pomocí Azure COMPUTE nebo ho nasadit pomocí zařízení Azure, jako je [Služba Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) nebo [Server odvození Triton (Preview)](how-to-deploy-with-triton.md).

Aby bylo možné použít se sadou Azure Machine Learning Python SDK, musí být model uložen jako serializovaný objekt Pythonu ve formátu rozevíracího seznamu (soubor. pkl). Musí také implementovat `predict(data)` metodu, která vrací objekt serializovatelný ve formátu JSON. Můžete například uložit místně vycvičený diabetes model scikit-učení s: 

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

Nově registrovaný model pak můžete najít na kartě Azure Machine Learning **model** :

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Snímek obrazovky karty modelu Azure Machine Learning zobrazující nahraný model":::

Další informace o nahrávání a aktualizaci modelů a prostředí najdete v tématu [registrace modelu a nasazení místně s využitím pokročilého použití](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Další kroky

- Další informace o správě prostředí najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).
- Další informace o přístupu k datům z úložiště dat najdete v tématu [připojení ke službám úložiště v Azure](how-to-access-data.md).
