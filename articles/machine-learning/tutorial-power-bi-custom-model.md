---
title: 'Kurz: Vytvoření prediktivního modelu pomocí poznámkového bloku (část 1 ze 2)'
titleSuffix: Azure Machine Learning
description: Naučte se, jak sestavit a nasadit model strojového učení pomocí kódu v Jupyter Notebook, abyste ho mohli použít k předpovědi výsledků v Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370187"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Kurz: Power BI Integration – Vytvoření prediktivního modelu s poznámkovým blokem (část 1 ze 2)

V první části tohoto kurzu se naučíte a nasazujete prediktivní model strojového učení s využitím kódu v Jupyter Notebook. V části 2 pak pomocí modelu předpovídáte výsledky v Microsoft Power BI.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoříte poznámkový blok Jupyter Notebooks.
> * Vytvoření instance služby Azure Machine Learning COMPUTE
> * Výuka regresního modelu pomocí scikit-učení
> * Nasazení modelu do vyhodnocovacího koncového bodu v reálném čase

Existují tři různé způsoby, jak vytvořit a nasadit model, který budete používat v Power BI.  Tento článek se zabývá možnostmi: výuka a nasazování modelů pomocí poznámkových bloků.  Tato možnost zobrazuje prostředí pro vytváření obsahu s využitím poznámkových bloků Jupyter hostovaných v Azure Machine Learning Studiu. 

Místo toho můžete použít:

* [Možnost B: analýza a nasazení modelů pomocí návrháře](tutorial-power-bi-designer-model.md)– prostředí pro vytváření nízkých kódů pomocí návrháře (uživatelské rozhraní přetahování myší).
* [Možnost C: analýza a nasazení modelů pomocí automatizovaného](tutorial-power-bi-automated-model.md) prostředí pro vytváření obsahu, které plně automatizuje přípravu dat a školení k modelu.


## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure ([k dispozici je bezplatná zkušební verze](https://aka.ms/AMLFree)). 
- Pracovní prostor služby Azure Machine Learning. Pokud ještě nemáte pracovní prostor, postupujte podle pokynů v tématu [jak vytvořit pracovní prostor Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Úvodní znalost jazyka Pythonu a pracovních postupů strojového učení.

## <a name="create-a-notebook-and-compute"></a>Vytvoření poznámkového bloku a výpočtů

Na domovské stránce [Azure Machine Learning Studio](https://ml.azure.com) vyberte **vytvořit nový** a pak na **Poznámkový blok**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Snímek obrazovky ukazující, jak vytvořit Poznámkový blok":::
 
Zobrazí se dialogové okno pro **Vytvoření nového souboru** . zadejte:

1. Název souboru pro Poznámkový blok (například `my_model_notebook` )
1. Změnit **typ souboru** na **Poznámkový blok**

Vyberte **Vytvořit**. Dále musíte vytvořit nějaké výpočetní prostředky a připojit je ke svému poznámkovému bloku, aby bylo možné spouštět buňky kódu. Uděláte to tak, že v horní části poznámkového bloku vyberete ikonu se symbolem plus:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Snímek obrazovky ukazující, jak vytvořit výpočetní instanci":::

V dalším kroku na stránce **vytvořit výpočetní instanci** :

1. Vyberte velikost virtuálního počítače s PROCESORem – pro účely tohoto kurzu **Standard_D11_v2** (dvě jádra, 14 GB RAM) bude stačit.
1. Vyberte **Další**. 
1. Na stránce **Konfigurovat nastavení** zadejte platný **výpočetní název** (platné znaky jsou velká a malá písmena, číslice a znak-znaku).
1. Vyberte **Vytvořit**.

Na poznámkovém bloku si můžete všimnout, že kroužek vedle **výpočetních** prostředků vypnul azurový, což značí, že se vytváří výpočetní instance:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Snímek obrazovky zobrazující výpočetní prostředky, které se vytváří":::

> [!NOTE]
> Zřizování výpočetních prostředků může trvat přibližně 2-4 minut.

Po zřízení výpočetní metody můžete pomocí poznámkového bloku spouštět buňky kódu. Zadejte například text do buňky:

```python
import numpy as np

np.sin(3)
```

Následuje **klávesa SHIFT-ENTER** (nebo **Control-ENTER** nebo vyberte tlačítko Přehrát vedle buňky). Měli byste vidět následující výstup:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Snímek obrazovky znázorňující spuštění buňky":::

Nyní jste připraveni vytvořit model Machine Learning.

## <a name="build-a-model-using-scikit-learn"></a>Vytvoření modelu pomocí scikit – učení

V tomto kurzu použijete [datovou sadu diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), která je dostupná v [otevřených datových sadách Azure](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Import dat

Pokud chcete importovat data, zkopírujte a vložte kód uvedený níže do nové **buňky kódu** v poznámkovém bloku:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Datový rámec PANDAS obsahuje 10 vstupních proměnných standardních hodnot (například věk, pohlaví, hmotnostní index karoserie, průměrný krevní tlak a šest měření krevního séra). `y_df`Datový rámec PANDAS je cílová proměnná, která obsahuje kvantitativní míru průběhu výskytu choroby v jednom roce po směrném plánu. K dispozici jsou celkem 442 záznamů.

### <a name="train-model"></a>Trénování modelu

Vytvořte v poznámkovém bloku novou **buňku kódu** a zkopírujte a vložte fragment kódu níže, který sestaví Ridge regresní model a serializaci modelu pomocí formátu rozevíracího seznamu Pythonu:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrace modelu

Kromě obsahu samotného souboru modelu bude registrovaný model také ukládat metadata modelu – popis modelu, značky a informace o architektuře – to bude užitečné při správě a nasazování modelů v pracovním prostoru. Pomocí značek můžete například kategorizovat modely a použít filtry při výpisu modelů v pracovním prostoru. Označením tohoto modelu pomocí architektury scikit-učení se taky zjednoduší jeho nasazení jako webové služby, jak uvidíme později.

Zkopírujte a vložte kód uvedený níže do nové **buňky kódu** v poznámkovém bloku:

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

Model v Azure Machine Learning Studio můžete zobrazit také tak, že přejdete k **koncovým bodům** v levé nabídce:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Snímek obrazovky znázorňující model":::

### <a name="define-the-scoring-script"></a>Definování vyhodnocovacího skriptu

Při nasazování modelu, který se má integrovat do Microsoft Power BI, je potřeba definovat *skript bodování* a vlastní prostředí pro Python. Skript bodování obsahuje dvě funkce:

- `init()` – Tato funkce se spustí po spuštění služby. Tato funkce načte model (Všimněte si, že se model automaticky stáhne z registru modelu) a deserializace.
- `run(data)` – Tato funkce se spustí, když se ve službě provede volání s některými vstupními daty, která potřebují bodování. 

>[!NOTE]
> Dekoratéry Python používáme k definování schématu vstupních a výstupních dat, což je důležité, aby mohla integrace s Microsoft Power BI fungovat.

Zkopírujte a vložte kód uvedený níže do nové **buňky kódu** v poznámkovém bloku. Následující fragment kódu obsahuje buňku Magic, která bude zapisovat kód do názvu s názvem score.py.

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
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definování vlastního prostředí

Dál je potřeba definovat prostředí pro stanovení skóre modelu – musíme v tomto prostředí definovat balíčky Pythonu vyžadované skriptem bodování (score.py), které jsou definované výše, jako je PANDAS, scikit-učení atd.

Pokud chcete definovat prostředí, zkopírujte a vložte kód uvedený níže do nové **buňky kódu** v poznámkovém bloku:

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

Model nasadíte tak, že zkopírujete a vložíte kód níže do nové **buňky kódu** v poznámkovém bloku:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Nasazení služby může trvat 2-4 minut.

Měl by se zobrazit následující výstup úspěšné nasazené služby:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Službu můžete zobrazit také v Azure Machine Learning Studio tak, že přejdete do **koncových bodů** v levé nabídce:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Snímek obrazovky zobrazující koncový bod":::

Doporučuje se otestovat webovou službu, aby se zajistilo, že funguje podle očekávání. Přejděte zpátky do svého poznámkového bloku tak, že v nabídce na levé straně vyberete **poznámkové bloky** v Azure Machine Learning Studio. Zkopírováním a vložením kódu níže do nové **buňky kódu** v poznámkovém bloku otestujete službu:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Výstup by měl vypadat jako následující struktura JSON: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste viděli, jak sestavit a nasadit model takovým způsobem, že ho můžou využívat Microsoft Power BI. V další části se dozvíte, jak tento model využít ze sestavy Power BI.

> [!div class="nextstepaction"]
> [Kurz: využití modelu v Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
