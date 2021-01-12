---
title: 'Kurz: Vytvoření prediktivního modelu pomocí poznámkového bloku (část 1 ze 2)'
titleSuffix: Azure Machine Learning
description: Naučte se, jak sestavit a nasadit model strojového učení pomocí kódu v Jupyter Notebook. Vytvořte také skript bodování definující vstup a výstup pro jednoduchou integraci do Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 29b340448f3ce3e18a649065bdcd0b335bab8b73
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108241"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Kurz: Power BI Integration – Vytvoření prediktivního modelu s Jupyter Notebook (část 1 ze 2)

V části 1 tohoto kurzu jste prosadili a nasadili prediktivní model strojového učení pomocí kódu ve Jupyter Notebook. Vytvoří se také skript bodování, který definuje vstupní a výstupní schéma modelu pro integraci do Power BI.  V části 2 použijete model k předpovídání výsledků v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoříte Jupyter Notebook.
> * Vytvořte instanci služby Azure Machine Learning Compute.
> * Naučte se regresní model pomocí scikit-učit.
> * Napište vyhodnocování skriptu definující vstup a výstup pro jednoduchou integraci do Microsoft Power BI.
> * Nasaďte model do bodování koncového bodu v reálném čase.

Existují tři způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek popisuje možnost A: vytvoření modelů a nasazení pomocí poznámkových bloků.  Tato možnost je prostředí pro vytváření obsahu s prvním kódem. Používá poznámkové bloky Jupyter hostované v Azure Machine Learning Studio. 

Místo toho ale můžete použít jednu z dalších možností:

* [Možnost B: analýza a nasazení modelů pomocí návrháře Azure Machine Learning](tutorial-power-bi-designer-model.md). Toto prostředí pro vytváření s nízkým kódem používá uživatelské rozhraní přetahování myší.
* [Možnost C: analýza a nasazení modelů pomocí automatizovaného strojového učení](tutorial-power-bi-automated-model.md). Prostředí pro vytváření obsahu bez kódu plně automatizuje automatické školení pro přípravu dat a modelování.


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné, můžete použít [bezplatnou zkušební verzi](https://aka.ms/AMLFree). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, přečtěte si téma [Vytvoření a správa Azure Machine Learningch pracovních prostorů](./how-to-manage-workspace.md#create-a-workspace).
- Úvodní znalost jazyka Pythonu a pracovních postupů strojového učení.

## <a name="create-a-notebook-and-compute"></a>Vytvoření poznámkového bloku a výpočtů

Na domovské stránce [**Azure Machine Learning Studio**](https://ml.azure.com) vyberte **vytvořit nový**  >  **Poznámkový blok**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Snímek obrazovky ukazující, jak vytvořit Poznámkový blok":::
 
Na stránce **vytvořit nový soubor** :

1. Pojmenujte svůj Poznámkový blok (například *my_model_notebook*).
1. Změňte **typ souboru** na **Poznámkový blok**.
1. Vyberte **Vytvořit**. 
 
Chcete-li spustit buňky kódu, vytvořte výpočetní instanci a připojte ji k poznámkovému bloku. Začněte tím, že v horní části poznámkového bloku vyberete ikonu se symbolem plus:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní instanci.":::

Na stránce **vytvoření instance COMPUTE** :

1. Vyberte velikost virtuálního počítače procesoru. Pro účely tohoto kurzu můžete zvolit **Standard_D11_v2** se 2 jádry a 14 GB paměti RAM.
1. Vyberte **Další**. 
1. Na stránce **Konfigurovat nastavení** zadejte platný **výpočetní název**. Platné znaky jsou velká a malá písmena, číslice a spojovníky (-).
1. Vyberte **Vytvořit**.

V poznámkovém bloku si můžete všimnout kružnice vedle **výpočtů** s azurovou funkcí. Tato změna barvy indikuje, že se vytváří výpočetní instance:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Snímek obrazovky znázorňující vytvářený výpočetní výkon":::

> [!NOTE]
> Instance COMPUTE může trvat 2 až 4 minuty, než se zřídí.

Po zřízení výpočetní aplikace můžete pomocí poznámkového bloku spouštět buňky kódu. Například v buňce můžete zadat následující kód:

```python
import numpy as np

np.sin(3)
```

Pak vyberte SHIFT + ENTER (nebo vyberte CTRL + ENTER nebo vyberte tlačítko **Přehrát** vedle buňky). Měl by se zobrazit následující výstup:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Snímek obrazovky znázorňující výstup buňky":::

Teď jste připraveni sestavit model strojového učení.

## <a name="build-a-model-by-using-scikit-learn"></a>Sestavování modelu pomocí scikit-učení

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Tato datová sada je dostupná v [Azure Open](https://azure.microsoft.com/services/open-datasets/)DataSet.


### <a name="import-data"></a>Import dat

Pokud chcete importovat data, zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Datový rámec PANDAS obsahuje 10 vstupních proměnných pro standardní hodnoty. Mezi tyto proměnné patří věk, pohlaví, hmotnostní index těla, průměrný krevní tlak a šest měření krevního séra. `y_df`Datový rámec PANDAS je cílová proměnná. Obsahuje kvantitativní míru průběhu nákazy jeden rok po standardních hodnotách. Datový rámec obsahuje záznamy 442.

### <a name="train-the-model"></a>Trénování modelu

Vytvoří novou *buňku kódu* v poznámkovém bloku. Pak zkopírujte následující kód a vložte ho do buňky. Tento fragment kódu vytvoří Ridge regresní model a serializaci modelu pomocí rozevíracího formátu Pythonu.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrace modelu

Kromě obsahu samotného souboru modelu budou v registrovaném modelu uloženy metadata. Metadata obsahují popis modelu, značky a informace o rozhraní. 

Metadata jsou užitečná při správě a nasazování modelů v pracovním prostoru. Pomocí značek můžete například kategorizovat modely a použít filtry při výpisu modelů v pracovním prostoru. Také Pokud označíte tento model pomocí architektury scikit-učení, Zjednodušte si ho nasazení jako webovou službu.

Zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku.

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Model můžete zobrazit také v Azure Machine Learning Studio. V nabídce na levé straně vyberte **modely**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Snímek obrazovky znázorňující zobrazení modelu":::

## <a name="define-the-scoring-script"></a>Definování vyhodnocovacího skriptu

Když nasadíte model, který bude integrovaný do Power BI, je potřeba definovat *skript bodování* a vlastní prostředí pro Python. Skript bodování obsahuje dvě funkce:

- `init()`Funkce se spustí při spuštění služby. Načte model (který se automaticky stáhne z registru modelu) a deserializace.
- Tato `run(data)` funkce se spustí, když volání služby zahrnuje vstupní data, která je třeba určit skóre. 

>[!NOTE]
> Dekoratéry Python v následujícím kódu definuje schéma vstupních a výstupních dat, což je důležité pro integraci do Power BI.

Zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku. Následující fragment kódu obsahuje buňku Magic, která zapisuje kód do souboru s názvem *Score.py*.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definování vlastního prostředí

Dále definujte prostředí pro určení skóre modelu. V prostředí definujte balíčky Pythonu, například PANDAS a scikit-učení, které skript pro bodování (*Score.py*) vyžaduje.

Pokud chcete definovat prostředí, zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Nasazení modelu

Pokud chcete model nasadit, zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Nasazení služby může trvat 2 až 4 minuty.

Pokud se služba úspěšně nasazuje, měl by se zobrazit následující výstup:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Službu můžete zobrazit také v Azure Machine Learning Studio. V nabídce na levé straně vyberte **koncové body**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Snímek obrazovky ukazující, jak zobrazit službu":::

Doporučujeme, abyste otestovali webovou službu, abyste zajistili, že funguje podle očekávání. Pokud chcete vrátit svůj Poznámkový blok, Azure Machine Learning Studio v nabídce na levé straně vyberte **poznámkové bloky** v nabídce vlevo. Pak zkopírujte následující kód a vložte ho do nové *buňky kódu* v poznámkovém bloku pro otestování služby.

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Výstup by měl vypadat jako tato struktura JSON: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste viděli, jak sestavit a nasadit model, aby ho bylo možné spotřebovat Power BI. V další části se dozvíte, jak tento model využít v sestavě Power BI.

> [!div class="nextstepaction"]
> [Kurz: využívání modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
