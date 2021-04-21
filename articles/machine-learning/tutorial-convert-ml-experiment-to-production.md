---
title: Převod kódu poznámkového bloku na skripty Pythonu
titleSuffix: Azure Machine Learning
description: Pomocí šablony kódu MLOpsPython zapněte experimentální poznámkové bloky pro strojové učení do kódu připraveného pro produkční prostředí. Pak můžete tento kód otestovat, nasadit a automatizovat.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817481"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Kurz: převod experimentů ML na produkční kód v Pythonu

V tomto kurzu se dozvíte, jak převést Juptyer poznámkové bloky na skripty Pythonu, aby se zajistilo jejich testování a automatizace pomocí šablony kódu MLOpsPython a Azure Machine Learning. Tento proces se obvykle používá k přezkoušení/školení kódu z poznámkového bloku Juptyer a k jeho převodu na skripty v Pythonu. Tyto skripty pak můžete použít k testování a automatizaci CI/CD v produkčním prostředí. 

Projekt strojového učení vyžaduje experimentování, kde se hypotéza testuje pomocí agilních nástrojů, jako je Jupyter Notebook pomocí reálných datových sad. Jakmile je model připravený k produkci, kód modelu by měl být umístěn v provozním úložišti kódu. V některých případech je nutné kód modelu převést na skripty v jazyce Python, aby byly umístěny do úložiště kódu v produkčním prostředí. Tento kurz se zabývá doporučeným přístupem k exportu kódu experimentování do skriptů Pythonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyčistit nenezbytný kód
> * Refaktoring Jupyter Notebook kódu do funkcí
> * Vytvoření skriptů v jazyce Python pro související úlohy
> * Create unit tests
(Vytvořit testy jednotek)

## <a name="prerequisites"></a>Požadavky

- Vygenerujte [šablonu MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) a používejte `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` poznámkové bloky a. Tyto poznámkové bloky se používají jako příklad převodu z experimentování do produkčního prostředí. Tyto poznámkové bloky najdete na adrese [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Nainstalujte `nbconvert`. Postupujte podle pokynů k instalaci v části __instalace nbconvert__ na stránce [instalace](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Odebrat veškerý nenezbytný kód

Kód napsaný během experimentů je určený jenom pro průzkumné účely. Proto první krok převodu experimentálního kódu do produkčního kódu je odebrat tento nenezbytný kód. Odebrání nedůležitého kódu také způsobí, že kód bude udržovatelnější. V této části odeberete kód z `experimentation/Diabetes Ridge Regression Training.ipynb` poznámkového bloku. Příkazy, které tisknou tvar `X` a `y` a jsou voláním buňky `features.describe` pouze pro zkoumání dat a lze je odebrat. Po odebrání nedůležitého kódu `experimentation/Diabetes Ridge Regression Training.ipynb` by měl vypadat jako následující kód bez Markdownu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktoring kódu do funkcí

Za druhé, kód Jupyter je potřeba Refaktorovat do funkcí. Refaktoring kódu do funkcí usnadňuje testování jednotek a způsobuje, že kód je udržovatelnější. V této části se refaktoruje:

- Školicí notebook diabetes Ridge regrese ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Hodnoticí Poznámkový blok diabetes Ridge regrese ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktoring diabetes Ridge regrese školicí Poznámkový blok do funkcí

V nástroji `experimentation/Diabetes Ridge Regression Training.ipynb` proveďte následující kroky:

1. Vytvořte funkci volanou `split_data` pro rozdělení datového rámce do testovacích a výukových dat. Funkce by měla převzít datový rámec `df` jako parametr a vracet slovník obsahující klíče `train` a `test` .

    Přesuňte kód pod část *rozdělená data do okna pro školení a ověření* do `split_data` funkce a upravte ji tak, aby vrátila `data` objekt.

1. Vytvořte funkci s názvem `train_model` , která přebírá parametry `data` a `args` vrátí vyškolený model.

    Přesuňte kód v části školicí *model pro školení* do `train_model` funkce Set a upravte ji tak, aby vrátila `reg_model` objekt. Odeberte `args` slovník, hodnoty budou pocházet z `args` parametru.

1. Vytvořte funkci s názvem `get_model_metrics` , která přebírá parametry `reg_model` a `data` a vyhodnotí model a potom vrátí slovník metrik pro školený model.

    Přesuňte kód pod hlavičkou *ověřit model v sadě ověření* do `get_model_metrics` funkce a upravte jej tak, aby vrátil `metrics` objekt.

Tři funkce by měly být následující:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Pořád v nástroji `experimentation/Diabetes Ridge Regression Training.ipynb` proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `main` , která nepřijímá žádné parametry a vrátí hodnotu Nothing.
1. Přesuňte kód pod nadpisem načíst data do `main` funkce.
1. Přidejte vyvolání pro nově zapsané funkce do `main` funkce:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Přesuňte kód pod nadpisem "Uložit model" do `main` funkce.

`main`Funkce by měla vypadat jako v následujícím kódu:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

V této fázi by neměl žádný kód zůstat v poznámkovém bloku, který není ve funkci, kromě příkazů import v první buňce.

Přidejte příkaz, který volá `main` funkci.

```python
main()
```

Po refaktorování `experimentation/Diabetes Ridge Regression Training.ipynb` by měl vypadat jako následující kód bez Markdownu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktoring Poznámkový blok diabetes Ridge regrese do funkcí

V nástroji `experimentation/Diabetes Ridge Regression Scoring.ipynb` proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `init` , která nepřijímá žádné parametry a nevrátí žádnou hodnotu.
1. Zkopírujte kód pod nadpisem "model zatížení" do `init` funkce.

`init`Funkce by měla vypadat jako v následujícím kódu:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po `init` Vytvoření funkce nahraďte celý kód pod hlavičkou "model zatížení" jedním voláním metody `init` :

```python
init()
```

V nástroji `experimentation/Diabetes Ridge Regression Scoring.ipynb` proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `run` , která přebírá `raw_data` a `request_headers` jako parametry a vrátí slovník výsledků následujícím způsobem:

    ```python
    {"result": result.tolist()}
    ```

1. Zkopírujte kód pod nadpisy "Příprava dat" a "data skóre" do `run` funkce.

    `run`Funkce by měla vypadat podobně jako následující kód (Nezapomeňte odebrat příkazy, které nastavily proměnné `raw_data` a `request_headers` , které budou použity později při `run` volání funkce):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po `run` Vytvoření funkce nahraďte veškerý kód pod záhlavím "připravit data" a "data skóre" následujícím kódem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Předchozí proměnné sady kódu `raw_data` a `request_header` , volají `run` funkci s `raw_data` a `request_header` , a vytiskne předpovědi.

Po refaktorování `experimentation/Diabetes Ridge Regression Scoring.ipynb` by měl vypadat jako následující kód bez Markdownu:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kombinování souvisejících funkcí v souborech Pythonu

Třetí, související funkce musí být sloučeny do souborů Pythonu, aby lépe usnadnily opakované použití kódu. V této části budete vytvářet soubory Pythonu pro následující poznámkové bloky:

- Školicí notebook diabetes Ridge regrese ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Hodnoticí Poznámkový blok diabetes Ridge regrese ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Vytvořit soubor Pythonu pro školicí Poznámkový blok diabetes Ridge regrese

Převeďte svůj Poznámkový blok na spustitelný skript spuštěním následujícího příkazu na příkazovém řádku, který používá `nbconvert` balíček a cestu k `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Po převedení poznámkového bloku na `train.py` odeberte všechny nežádoucí komentáře. Nahraďte volání na `main()` konci souboru pomocí podmíněného vyvolání jako v následujícím kódu:

```python
if __name__ == '__main__':
    main()
```

Váš `train.py` soubor by měl vypadat jako v následujícím kódu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` lze nyní vyvolat z terminálu spuštěním `python train.py` .
Funkce z `train.py` lze také volat z jiných souborů.

Soubor, který se `train_aml.py` nachází v `diabetes_regression/training` adresáři úložiště MLOpsPython, volá funkce definované v `train.py` v kontextu Azure Machine Learning experimentu spustíte. Funkce lze také volat v testování částí popsaných dále v této příručce.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Vytvořit soubor Pythonu pro vyhodnocovací Poznámkový blok diabetes Ridge regrese

Přetajně Poznámkový blok na spustitelný skript spuštěním následujícího příkazu na příkazovém řádku, který používá `nbconvert` balíček a cestu k `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Po převedení poznámkového bloku na `score.py` odeberte všechny nežádoucí komentáře. Váš `score.py` soubor by měl vypadat jako v následujícím kódu:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model`Proměnná musí být globální, aby byla viditelná v celém skriptu. Na začátek funkce přidejte následující příkaz `init` :

```python
global model
```

Po přidání předchozího příkazu `init` by měla funkce vypadat jako v následujícím kódu:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Vytvořit testy jednotek pro každý soubor Pythonu

Čtvrtě vytvořte testy jednotek pro funkce Pythonu. Testování částí chrání kód před funkční regresí a usnadňuje údržbu. V této části budete vytvářet testy jednotek pro funkce v `train.py` .

`train.py` obsahuje více funkcí, ale v tomto kurzu vytvoříme jenom jeden test jednotky pro `train_model` funkci pomocí rozhraní Pytest Framework. Pytest není jedinou architekturou testování jednotek Pythonu, ale je to jedna z nejčastěji používaných. Další informace najdete na webu [Pytest](https://pytest.org).

Test jednotek obvykle obsahuje tři hlavní akce:

- Uspořádat objekt – vytváření a nastavování nezbytných objektů
- Pracovat na objektu
- Vyhodnocení očekávání

Test jednotek bude volat `train_model` s některými pevně zakódovanými daty a argumenty a ověří, zda se `train_model` jednalo podle očekávání, pomocí výsledného výukového modelu a vytvořit předpovědi a porovnat tuto předpověď s očekávanou hodnotou.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s postupem převodu z experimentu do produkčního kódu, si přečtěte následující odkazy, kde najdete další informace a další kroky:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): vytvoření kanálu CI/CD pro výuku, vyhodnocení a nasazení vlastního modelu pomocí Azure Pipelines a Azure Machine Learning
+ [Monitorování běhů a metriky Azure ML](./how-to-log-view-metrics.md)
+ [Monitorování a shromažďování dat z koncových bodů webové služby ML](./how-to-enable-app-insights.md)