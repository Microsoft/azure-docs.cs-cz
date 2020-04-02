---
title: Převést kód experimentu strojového učení na produkční kód
titleSuffix: Azure Machine Learning
description: Zjistěte, jak převést experimentální kód strojového učení na produkční kód pomocí šablony kódu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: e3c9b16ae3d2b06ec19ecd29d15762a065c0c1ae
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521445"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Kurz: Převod experimentálního kódu ML na produkční kód

Projekt strojového učení vyžaduje experimentování, kde jsou hypotézy testovány pomocí agilních nástrojů, jako je Jupyter Notebook pomocí reálných datových sad. Jakmile je model připraven k výrobě, kód modelu by měl být umístěn do úložiště kódu výroby. V některých případech musí být kód modelu převeden na skripty Pythonu, které mají být umístěny v úložišti produkčního kódu. Tento kurz popisuje doporučený přístup k exportu kódu experimentování do skriptů Pythonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyčistit nepotřebný kód
> * Refaktorování kódu notebooku Jupyter na funkce
> * Vytvoření skriptů Pythonu pro související úkoly
> * Create unit tests
(Vytvořit testy jednotek)

## <a name="prerequisites"></a>Požadavky

- Vygenerujte [šablonu MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) a použijte poznámkové bloky `experimentation/Diabetes Ridge Regression Training.ipynb` a. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Tyto poznámkové bloky se používají jako příklad převodu z experimentování do výroby. Tyto poznámkové bloky [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)najdete na adrese .
- Nainstalujte `nbconvert`. Postupujte pouze podle pokynů k instalaci v části __Instalace nbconvert__ na stránce [Instalace.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Odebrat veškerý nepotřebný kód

Některý kód napsaný během experimentování je určen pouze pro průzkumné účely. Proto prvním krokem k převodu experimentálního kódu do produkčního kódu je odebrání tohoto nepotřebného kódu. Odebrání nepotřebného kódu také způsobí, že kód bude více udržovatelný. V této části odeberete kód `experimentation/Diabetes Ridge Regression Training.ipynb` z poznámkového bloku. Příkazy tisk tvar `X` `y` a volání `features.describe` buňky jsou pouze pro zkoumání dat a lze odebrat. Po odebrání nepotřebný `experimentation/Diabetes Ridge Regression Training.ipynb` kód, by měl vypadat jako následující kód bez markdown:

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
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktorovat kód do funkcí

Za druhé, kód Jupyter musí být refaktorován do funkcí. Refaktoring kód do funkcí usnadňuje testování částí a umožňuje kód více udržovatelné. V této části budete refaktorovat:

- Diabetes Ridge Regrese Školení`experimentation/Diabetes Ridge Regression Training.ipynb`notebook( )
- Diabetes Ridge Regrese Bodování`experimentation/Diabetes Ridge Regression Scoring.ipynb`notebook( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktorovat Diabetes Ridge Regrese Školení notebook do funkcí

V `experimentation/Diabetes Ridge Regression Training.ipynb`, proveďte následující kroky:

1. Vytvořte funkci `split_data` volanou k rozdělení datového rámce na data testu a trénování. Funkce by měla vzít `df` datový rámec jako parametr a vrátit `train` slovník `test`obsahující klíče a .

    Přesuňte kód pod *rozdělená data do směrovací a ověřovací sady* nadpis do `split_data` funkce a upravit ji vrátit `data` objekt.

1. Vytvořte funkci `train_model`s názvem , `data` `args` která přebírá parametry a vrací trénovaný model.

    Přesuňte kód pod nadpisem *Trénovací model na trénovací sadu* do `train_model` funkce a upravte jej tak, aby vrátil `reg_model` objekt. Odeberte `args` slovník, hodnoty budou `args` pocházet z parametru.

1. Vytvořte funkci `get_model_metrics`s názvem `reg_model` , `data`která přebírá parametry a , a vyhodnotí model a pak vrátí slovník metrik pro trénovaný model.

    Přesuňte kód pod *nadpisem Ověřit model při sadě ověření* do `get_model_metrics` funkce a upravte jej tak, aby vrátil `metrics` objekt.

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

Stále `experimentation/Diabetes Ridge Regression Training.ipynb`v , proveďte následující kroky:

1. Vytvořte novou `main`funkci s názvem , která nepřebírá žádné parametry a nic nevrátí.
1. Přesuňte kód pod nadpisem "Načíst data" do `main` funkce.
1. Přidejte vyvolání nově zapsaných `main` funkcí do funkce:
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
1. Přesuňte kód pod nadpisem "Uložit `main` model" do funkce.

Funkce `main` by měla vypadat jako následující kód:

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

V této fázi by měl být žádný kód zbývající v poznámkovém bloku, který není ve funkci, než příkazy importu v první buňce.

Přidejte příkaz, `main` který volá funkci.

```python
main()
```

Po refaktoringu by `experimentation/Diabetes Ridge Regression Training.ipynb` měl vypadat jako následující kód bez markdownu:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktorovat Diabetes Ridge Regrese Bodování notebook do funkcí

V `experimentation/Diabetes Ridge Regression Scoring.ipynb`, proveďte následující kroky:

1. Vytvořte novou `init`funkci s názvem , která nepřebírá žádné parametry a nic nevrátí.
1. Zkopírujte kód pod nadpisem "Načíst model" do `init` funkce.

Funkce `init` by měla vypadat jako následující kód:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po `init` vytvoření funkce nahraďte veškerý kód pod nadpisem "Načíst `init` model" jedním voláním takto:

```python
init()
```

V `experimentation/Diabetes Ridge Regression Scoring.ipynb`, proveďte následující kroky:

1. Vytvořte novou `run`funkci s `raw_data` `request_headers` názvem , která bere a jako parametry a vrátí slovník výsledků takto:

    ```python
    {"result": result.tolist()}
    ```

1. Do `run` funkce zkopírujte kód pod nadpisy "Připravit data" a "Data skóre".

    Funkce `run` by měla vypadat jako následující kód (Nezapomeňte odebrat `raw_data` `request_headers`příkazy, které nastavují proměnné a , které budou použity později při volání `run` funkce):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po `run` vytvoření funkce nahraďte veškerý kód pod nadpisy "Připravit data" a "Data skóre" následujícím kódem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Předchozí kód nastaví `raw_data` `request_header`proměnné a `run` , `raw_data` `request_header`volá funkci s a , a vytiskne předpovědi.

Po refaktoringu by `experimentation/Diabetes Ridge Regression Scoring.ipynb` měl vypadat jako následující kód bez markdownu:

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

Za třetí, související funkce je třeba sloučit do souborů Pythonu, aby bylo možné lépe pomoci s opětovným použitím kódu. V této části budete vytvářet soubory Pythonu pro následující poznámkové bloky:

- Diabetes Ridge Regrese Školení`experimentation/Diabetes Ridge Regression Training.ipynb`notebook( )
- Diabetes Ridge Regrese Bodování`experimentation/Diabetes Ridge Regression Scoring.ipynb`notebook( )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Vytvoření souboru Pythonu pro poznámkový blok Diabetes Ridge Regrese Training

Převeďte poznámkový blok na spustitelný skript spuštěním následujícího `nbconvert` příkazu v `experimentation/Diabetes Ridge Regression Training.ipynb`příkazovém řádku, který používá balíček a cestu :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po převodu poznámkového `train.py`bloku na program odstraňte všechny nežádoucí komentáře. Nahraďte `main()` volání na konci souboru podmíněným vyvoláním, jako je následující kód:

```python
if __name__ == '__main__':
    main()
```

Soubor `train.py` by měl vypadat takto:

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

`train.py`lze nyní vyvolat z terminálu `python train.py`spuštěním .
Funkce z `train.py` lze také volat z jiných souborů.

Soubor `train_aml.py` nalezený v `diabetes_regression/training` adresáři v úložišti MLOpsPython `train.py` volá funkce definované v aplikaci v kontextu spuštění experimentu Azure Machine Learning. Funkce lze také volat v testování částí, které jsou uvedeny dále v této příručce.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Vytvoření souboru Pythonu pro poznámkový blok Diabetes Ridge Regrese Scoring

Skrytí poznámkového bloku do spustitelného `nbconvert` `experimentation/Diabetes Ridge Regression Scoring.ipynb`skriptu spuštěním následujícího příkazu v příkazovém řádku, který používá balíček a cestu :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po převodu poznámkového `score.py`bloku na program odstraňte všechny nežádoucí komentáře. Soubor `score.py` by měl vypadat takto:

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

Proměnná `model` musí být globální, aby byla viditelná v celém skriptu. Na začátek funkce přidejte `init` následující příkaz:

```python
global model
```

Po přidání předchozího `init` příkazu by funkce měla vypadat jako následující kód:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Vytvoření testů částí pro každý soubor Pythonu

Za čtvrté, vytvořte testy částí pro vaše funkce Pythonu. Jednotkové testy chrání kód před funkčníregresí a usnadňují údržbu. V této části budete vytvářet testy částí `train.py`pro funkce v aplikaci .

`train.py`obsahuje více funkcí, ale vytvoříme pouze jeden `train_model` test částí pro funkci pomocí pytest rámce v tomto kurzu. Pytest není jediný rámec testování částí Pythonu, ale je to jeden z nejčastěji používaných. Pro více informací navštivte [Pytest](https://pytest.org).

Jednotkový test obvykle obsahuje tři hlavní akce:

- Uspořádat objekt - vytváření a nastavování potřebných objektů
- Jednat o objektu
- Prosadit, co se očekává

Testování částí bude `train_model` volat s některá pevně zakódovaná `train_model` data a argumenty a ověřit, které fungovaly podle očekávání pomocí výsledného trénovaného modelu k vytvoření předpovědi a porovnání této předpovědi s očekávanou hodnotou.

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

Nyní, když jste pochopili, jak převést z experimentu na produkční kód, naleznete v následujících odkazech další informace a další kroky:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)Vytvoření kanálu CI/CD pro trénování, vyhodnocování a nasazování vlastního modelu pomocí Azure Pipelines a Azure Machine Learning
+ [Monitorování spuštění experimentu Azure ML a metrik](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Sledování a shromažďování dat z koncových bodů webové služby ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
