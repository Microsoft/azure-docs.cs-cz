---
title: Převod kódu experimentování ze strojového učení do produkčního kódu
titleSuffix: Azure Machine Learning
description: Naučte se, jak převést experimentální kód strojového učení do produkčního kódu pomocí šablony kódu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 1be97f9754733561111cedcf95fe237b7788583e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122571"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Kurz: převod experimentálního kódu ML na produkční kód

Projekt strojového učení vyžaduje experimentování, kde se hypotéza testuje pomocí agilních nástrojů, jako je Jupyter Notebook pomocí reálných datových sad. Jakmile je model připravený k produkci, kód modelu by měl být umístěn v provozním úložišti kódu. V některých případech je nutné kód modelu převést na skripty v jazyce Python, aby byly umístěny do úložiště kódu v produkčním prostředí. Tento kurz se zabývá doporučeným přístupem k exportu kódu experimentování do skriptů Pythonu.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vyčistit nenezbytný kód
> * Refaktoring Jupyter Notebook kódu do funkcí
> * Vytvoření skriptů v jazyce Python pro související úlohy
> * Vytvořit testy jednotek

## <a name="prerequisites"></a>Požadavky

- Vygenerujte [šablonu MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) a použijte poznámkové bloky `experimentation/Diabetes Ridge Regression Training.ipynb` a `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Tyto poznámkové bloky se používají jako příklad převodu z experimentování do produkčního prostředí.
- Nainstalujte nbconvert. Postupujte podle pokynů k instalaci v části __instalace nbconvert__ na stránce [instalace](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Odebrat veškerý nenezbytný kód

Kód napsaný během experimentů je určený jenom pro průzkumné účely. Proto první krok převodu experimentálního kódu do produkčního kódu je odebrat tento nenezbytný kód. Odebrání nedůležitého kódu také způsobí, že kód bude udržovatelnější. V této části odeberete kód z poznámkového bloku `experimentation/Diabetes Ridge Regression Training.ipynb`. Příkazy, které tisknou tvar `X` a `y` a `features.describe` buňky, jsou pouze pro zkoumání dat a lze je odebrat. Po odebrání nedůležitého kódu by `experimentation/Diabetes Ridge Regression Training.ipynb` mělo vypadat jako následující kód bez Markdownu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktoring kódu do funkcí

Za druhé, kód Jupyter je potřeba Refaktorovat do funkcí. Refaktoring kódu do funkcí usnadňuje testování jednotek a způsobuje, že kód je udržovatelnější. V této části se refaktoruje:
- Školicí notebook diabetes Ridge regrese (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Poznámkový blok pro bodování diabetes Ridge regrese (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktoring diabetes Ridge regrese školicí Poznámkový blok do funkcí
V `experimentation/Diabetes Ridge Regression Training.ipynb`proveďte následující kroky:

1. Vytvořte funkci s názvem `train_model`, která převezme parametry `data` a `alpha` a vrátí model. 
1. Zkopírujte kód pod nadpisy "model výuky v sadě školení" a "ověřit model při ověření sady" do funkce `train_model`.

Funkce `train_model` by měla vypadat jako v následujícím kódu:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Po vytvoření funkce `train_model` nahraďte kód pod nadpisy "model výuky v sadě školení" a "ověřit model pro sadu ověřování" následujícím příkazem:

```python
reg = train_model(data, alpha)
```

Předchozí příkaz volá funkci `train_model`, která předává parametry `data` a `alpha` a vrací model.

V `experimentation/Diabetes Ridge Regression Training.ipynb`proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `main`, která nepřijímá žádné parametry a vrátí hodnotu Nothing.
1. Zkopírujte kód pod nadpisy "načíst data", "rozdělit data do školicích a ověřovacích sad" a "Uložit model" do funkce `main`.
1. Zkopírujte nově vytvořené volání `train_model` do funkce `main`.

Funkce `main` by měla vypadat jako v následujícím kódu:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Po vytvoření funkce `main` nahraďte veškerý kód pod nadpisy "načíst data", "rozdělit data do školicích a ověřovacích sad" a "Uložit model" společně s nově vytvořeným voláním `train_model` následujícím příkazem:

```python
main()
```

Po refaktoringu by `experimentation/Diabetes Ridge Regression Training.ipynb` měla vypadat jako následující kód bez Markdownu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktoring Poznámkový blok diabetes Ridge regrese do funkcí
V `experimentation/Diabetes Ridge Regression Scoring.ipynb`proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `init`, která nepřijímá žádné parametry a nevrátí žádnou hodnotu.
1. Zkopírujte kód pod nadpisem "model zatížení" do funkce `init`.

Funkce `init` by měla vypadat jako v následujícím kódu:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po vytvoření funkce `init` nahraďte celý kód pod hlavičkou "model zatížení" jedním voláním `init` následujícím způsobem:

```python
init()
```

V `experimentation/Diabetes Ridge Regression Scoring.ipynb`proveďte následující kroky:

1. Vytvořte novou funkci nazvanou `run`, která jako parametry přebírá `raw_data` a `request_headers` a vrátí slovník výsledků následujícím způsobem:

    ```python
    {"result": result.tolist()}
    ```

1. Zkopírujte kód pod nadpisy "Příprava dat" a "data skóre" do funkce `run`.

    Funkce `run` by měla vypadat jako následující kód (Nezapomeňte odebrat příkazy, které nastavily proměnné `raw_data` a `request_headers`, které budou použity později při volání funkce `run`):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po vytvoření funkce `run` nahraďte veškerý kód pod nadpisem "Příprava dat" a "data skóre" následujícím kódem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
Předchozí sady kódu proměnné `raw_data` a `request_header`zavolá funkci `run` s `raw_data` a `request_header`a vytiskne předpovědi.

Po refaktoringu by `experimentation/Diabetes Ridge Regression Scoring.ipynb` měla vypadat jako následující kód bez Markdownu:

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
- Školicí notebook diabetes Ridge regrese (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Poznámkový blok pro bodování diabetes Ridge regrese (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Vytvořit soubor Pythonu pro školicí Poznámkový blok diabetes Ridge regrese
Převeďte svůj Poznámkový blok na spustitelný skript spuštěním následujícího příkazu v příkazovém řádku, který používá balíček nbconvert a cestu `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po převedení poznámkového bloku na `train.py`odeberte všechny komentáře. Soubor `train.py` by měl vypadat jako v následujícím kódu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

`train.py` soubor, který se nachází v adresáři `diabetes_regression/training` v úložišti MLOpsPython, podporuje argumenty příkazového řádku (konkrétně `build_id`, `model_name`a `alpha`). Podporu argumentů příkazového řádku lze přidat do souboru `train.py` pro podporu názvů dynamických modelů a hodnot `alpha`, ale není nutné, aby byl kód úspěšně spuštěn.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Vytvořit soubor Pythonu pro vyhodnocovací Poznámkový blok diabetes Ridge regrese
Přetajně Poznámkový blok na spustitelný skript spuštěním následujícího příkazu na příkazovém řádku, který používá balíček nbconvert a cestu `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po převedení poznámkového bloku na `score.py`odeberte všechny komentáře. Soubor `score.py` by měl vypadat jako v následujícím kódu:

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

Funkce `train_model` vyžaduje úpravu pro vytvoření instance globálního variabilního modelu, aby byla viditelná v celém skriptu. Na začátek funkce `init` přidejte následující příkaz:

```python
global model
```

Po přidání předchozího příkazu by měla funkce `init` vypadat jako v následujícím kódu:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Vytvořit testy jednotek pro každý soubor Pythonu
Čtvrté testy jednotek je potřeba vytvořit pro každý soubor Pythonu, což zjednodušuje správu a údržbu kódu. V této části vytvoříte testování částí pro jednu z funkcí v `train.py`.

`train.py` obsahuje dvě funkce: `train_model` a `main`. Každá funkce potřebuje test jednotek, ale v tomto kurzu vytvoříme jenom jeden test jednotky pro funkci `train_model` pomocí rozhraní Pytest Framework.  Pytest není jedinou architekturou testování jednotek Pythonu, ale je to jedna z nejčastěji používaných. Další informace najdete na webu [Pytest](https://pytest.org).

Test jednotek obvykle obsahuje tři hlavní akce:
- Uspořádat objekt – vytváření a nastavování nezbytných objektů
- Pracovat na objektu
- Vyhodnocení očekávání

Běžnou podmínkou pro `train_model` je `data` a předává se hodnota `alpha`. Očekávaným výsledkem je, že by měly být volány funkce `Ridge.train` a `Ridge.predict`. Vzhledem k tomu, že metody školení strojového učení často nejsou spouštěny, volání `Ridge.train` bude napsaný. Vzhledem k tomu, že návratová hodnota `Ridge.train` je objektem, vytvoříme také `Ridge.predict`. Test jednotky pro `train_model` testování předání `data` a `alpha` hodnotu s očekávaným výsledkem `Ridge.train` a `Ridge.predict`ch funkcí, které jsou volány pomocí napodobování a Pytest Framework by měl vypadat jako následující kód:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Použití vlastního modelu se šablonou MLOpsPython kódu
Pokud jste pomohli postupovat podle kroků v tomto průvodci, budete mít sadu skriptů, které se budou připojovat ke skriptům vlak/skore/test, které jsou dostupné v úložišti MLOpsPython.  Podle struktury zmíněné výše vás následující kroky provedou, co je potřeba k použití těchto souborů pro vlastní projekt Machine Learning:  

1.  Dodržujte Průvodce Začínáme
2.  Výměna kódu školení
3.  Nahraďte kód skóre.
4.  Aktualizovat zkušební kód

### <a name="follow-the-getting-started-guide"></a>Dodržujte Průvodce Začínáme
Po provedení Úvodní příručky je potřeba, aby se podpora infrastruktury a kanálů spustila MLOpsPython.  Před vložením vlastního kódu doporučujeme nasazovat kód MLOpsPython, aby se zajistilo správné fungování struktury a kanálu.  Je také vhodné se seznámit se strukturou kódu úložiště.

### <a name="replace-training-code"></a>Výměna kódu školení
Nahrazení kódu, který se používá pro výuku modelu a odebrání nebo nahrazení odpovídajících testů jednotek, je nutné, aby řešení fungovalo s vlastním kódem.  Proveďte tyto kroky konkrétně:

1. Nahraďte `diabetes_regression\training\train.py`. Tento skript nahlásí model místně nebo na výpočetní úrovni Azure ML.
1. Odebrat nebo nahradit testy školicích jednotek nalezené v `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Nahradit kód skóre
Aby model poskytoval možnosti odvození v reálném čase, je nutné nahradit kód skóre. Šablona MLOpsPython používá kód skóre k nasazení modelu za účelem bodování v reálném čase na ACI, AKS nebo Web Apps.  Pokud chcete zachovat bodování, nahraďte `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Aktualizovat zkušební kód
Šablona MLOpsPython používá skript evaluate_model k porovnání výkonu nově vyučeného modelu a aktuálního výrobního modelu založeného na střednímu čtverci chyby. Pokud je výkon nově vyučeného modelu lepší než aktuální produkční model, kanály budou pokračovat. V opačném případě se kanály zastaví. Pokud chcete zachovat vyhodnocení, nahraďte všechny instance `mse` v `diabetes_regression/evaluate/evaluate_model.py` požadovanou metrikou. 

Pokud se chcete zbavit vyhodnocení, nastavte proměnnou kanálu DevOps `RUN_EVALUATION` v `.pipelines\diabetes_regression-variables` na `false`.

## <a name="next-steps"></a>Další kroky

Teď, když rozumíte tomu, jak převést z experimentu do produkčního kódu, použijte následující odkazy, kde se naučíte monitorovat spuštění experimentů a modely nasazené jako webové služby:

> [!div class="nextstepaction"]
> [Monitorujte běh a metriky Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [monitorování a shromažďování dat z koncových bodů webové služby ml](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insight) .
