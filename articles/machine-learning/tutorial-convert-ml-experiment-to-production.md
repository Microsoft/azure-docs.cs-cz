---
title: Převést kód experimentu strojového učení na produkční kód
titleSuffix: Azure Machine Learning
description: Zjistěte, jak převést experimentální kód strojového učení na produkční kód pomocí šablony kódu MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/13/2020
ms.openlocfilehash: f40c2b5f7134458b3f8cb492652bebf14388634c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477132"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Kurz: Převod experimentálního kódu ML na produkční kód

Projekt strojového učení vyžaduje experimentování, kde jsou hypotézy testovány pomocí agilních nástrojů, jako je Jupyter Notebook pomocí reálných datových sad. Jakmile je model připraven k výrobě, kód modelu by měl být umístěn do úložiště kódu výroby. V některých případech musí být kód modelu převeden na skripty Pythonu, které mají být umístěny v úložišti produkčního kódu. Tento kurz popisuje doporučený přístup k exportu kódu experimentování do skriptů Pythonu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vyčistit nepotřebný kód
> * Refaktorování kódu notebooku Jupyter na funkce
> * Vytvoření skriptů Pythonu pro související úkoly
> * Create unit tests
(Vytvořit testy jednotek)

## <a name="prerequisites"></a>Požadavky

- Vygenerujte [šablonu MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) a použijte poznámkové bloky `experimentation/Diabetes Ridge Regression Training.ipynb` a. `experimentation/Diabetes Ridge Regression Scoring.ipynb` Tyto poznámkové bloky se používají jako příklad převodu z experimentování do výroby. Tyto poznámkové bloky [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation)najdete na adrese .
- Nainstalujte nbconvert. Postupujte pouze podle pokynů k instalaci v části __Instalace nbconvert__ na stránce [Instalace.](https://nbconvert.readthedocs.io/en/latest/install.html)

## <a name="remove-all-nonessential-code"></a>Odebrat veškerý nepotřebný kód

Některý kód napsaný během experimentování je určen pouze pro průzkumné účely. Proto prvním krokem k převodu experimentálního kódu do produkčního kódu je odebrání tohoto nepotřebného kódu. Odebrání nepotřebného kódu také způsobí, že kód bude více udržovatelný. V této části odeberete kód `experimentation/Diabetes Ridge Regression Training.ipynb` z poznámkového bloku. Příkazy tisk tvar `X` `y` a volání `features.describe` buňky jsou pouze pro zkoumání dat a lze odebrat. Po odebrání nepotřebný `experimentation/Diabetes Ridge Regression Training.ipynb` kód, by měl vypadat jako následující kód bez markdown:

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

## <a name="refactor-code-into-functions"></a>Refaktorovat kód do funkcí

Za druhé, kód Jupyter musí být refaktorován do funkcí. Refaktoring kód do funkcí usnadňuje testování částí a umožňuje kód více udržovatelné. V této části budete refaktorovat:

- Diabetes Ridge Regrese Školení`experimentation/Diabetes Ridge Regression Training.ipynb`notebook( )
- Diabetes Ridge Regrese Bodování`experimentation/Diabetes Ridge Regression Scoring.ipynb`notebook( )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktorovat Diabetes Ridge Regrese Školení notebook do funkcí

V `experimentation/Diabetes Ridge Regression Training.ipynb`, proveďte následující kroky:

1. Vytvořte funkci `train_model`s názvem , `data` `alpha` která přebírá parametry a vrací model.
1. Zkopírujte kód pod nadpisy "Model vlaku na trénovací sadě" `train_model` a "Ověřit model na ověřovací sadě" do funkce.

Funkce `train_model` by měla vypadat jako následující kód:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Po `train_model` vytvoření funkce nahraďte kód pod nadpisy "Model vlaku na trénovací sadě" a "Ověřit model na ověřovací sadě" následujícím příkazem:

```python
reg = train_model(data, alpha)
```

Předchozí příkaz volá `train_model` funkci `data` předávání `alpha` a parametry a vrátí model.

V `experimentation/Diabetes Ridge Regression Training.ipynb`, proveďte následující kroky:

1. Vytvořte novou `main`funkci s názvem , která nepřebírá žádné parametry a nic nevrátí.
1. Zkopírujte kód pod nadpisy "Načíst data", "Rozdělit data do trénovacích `main` a ověřovacích sad" a "Uložit model" do funkce.
1. Zkopírujte nově vytvořené `train_model` volání `main` do funkce.

Funkce `main` by měla vypadat jako následující kód:

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

Po `main` vytvoření funkce nahraďte veškerý kód pod nadpisy "Načíst data", "Rozdělit data do trénovacích a `train_model` ověřovacích sad" a "Uložit model" spolu s nově vytvořeným voláním s následujícím příkazem:

```python
main()
```

Po refaktoringu by `experimentation/Diabetes Ridge Regression Training.ipynb` měl vypadat jako následující kód bez markdownu:

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

Převeďte poznámkový blok na spustitelný skript spuštěním následujícího příkazu v příkazovém řádku, který používá balíček nbconvert a cestu `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Po převodu poznámkového `train.py`bloku na program odeberte všechny komentáře. Soubor `train.py` by měl vypadat takto:

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

Soubor `train.py` nalezený v `diabetes_regression/training` adresáři v úložišti MLOpsPython podporuje argumenty `build_id` `model_name`příkazového `alpha`řádku (konkrétně , a ). Podporu argumentů příkazového řádku lze `train.py` přidat do souboru `alpha` pro podporu názvů dynamických modelů a hodnot, ale není nutné, aby se kód úspěšně spouštěl.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Vytvoření souboru Pythonu pro poznámkový blok Diabetes Ridge Regrese Scoring

Skrytí poznámkového bloku do spustitelného `experimentation/Diabetes Ridge Regression Scoring.ipynb`skriptu spuštěním následujícího příkazu v příkazovém řádku, který používá balíček nbconvert a cestu :

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Po převodu poznámkového `score.py`bloku na program odeberte všechny komentáře. Soubor `score.py` by měl vypadat takto:

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

Funkce `train_model` potřebuje úpravu k vytvoření instance modelu globální proměnné tak, aby byl viditelný v celém skriptu. Na začátek funkce přidejte `init` následující příkaz:

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

Za čtvrté, testování částí je třeba vytvořit pro každý soubor Pythonu, což usnadňuje odolnost kódu a usnadňuje údržbu. V této části vytvoříte testování částí pro jednu `train.py`z funkcí v aplikaci .

`train.py`obsahuje dvě `train_model` funkce: a `main`. Každá funkce potřebuje testování částí, ale vytvoříme pouze jeden `train_model` test částí pro funkci pomocí pytest rámce v tomto kurzu. Pytest není jediný rámec testování částí Pythonu, ale je to jeden z nejčastěji používaných. Pro více informací navštivte [Pytest](https://pytest.org).

Jednotkový test obvykle obsahuje tři hlavní akce:

- Uspořádat objekt - vytváření a nastavování potřebných objektů
- Jednat o objektu
- Prosadit, co se očekává

Běžnou podmínkou `train_model` pro `data` je, když a `alpha` hodnota jsou předány. Očekávaný výsledek je, `Ridge.train` `Ridge.predict` že funkce a by měly být volány. Vzhledem k tomu, že metody školení strojového učení často nejsou rychlé, volání `Ridge.train` bude zesměšňováno. Vzhledem k `Ridge.train` tomu, že vrácená hodnota je `Ridge.predict`zesměšňovaný objekt, budeme také zesměšňovat . Testování částí `train_model` pro testování `data` předávání `alpha` a hodnotu s `Ridge.train` `Ridge.predict` očekávaným výsledkem a funkce jsou volány pomocí zesměšňovat a Pytest rozhraní by měl vypadat jako následující kód:

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
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Použití vlastního modelu se šablonou kódu MLOpsPython

Pokud jste byli podle kroků v této příručce, budete mít sadu skriptů, které korelují s train/score/test skripty k dispozici v úložišti MLOpsPython.  Podle výše uvedené struktury budou následující kroky procházet tím, co je potřeba k použití těchto souborů pro vlastní projekt strojového učení:

1. Postupujte podle průvodce [Začínáme](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) s MLOpsPython
2. Postupujte podle pokynů mlopspython [bootstrap](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) k vytvoření výchozího bodu projektu
3. Nahrazení školicího řádu
4. Nahrazení kódu skóre
5. Aktualizace hodnotícího kódu

### <a name="follow-the-getting-started-guide"></a>Postupujte podle průvodce Začínáme
Po [Začínáme](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) průvodce je nutné mít podpůrnou infrastrukturu a kanály pro spuštění MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Postupujte podle pokynů bootstrap

[Bootstrap z úložiště MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) vám pomůže rychle připravit úložiště pro váš projekt.

**Poznámka:** Vzhledem k tomu, že skript zaváděcí ho přejmenuje diabetes_regression složku `[project name]` na název projektu podle vašeho výběru, budeme odkazovat na váš projekt, jako když jsou zapojeny cesty.

### <a name="replace-training-code"></a>Nahradit školicí kód

Výměna kódu použitého k trénování modelu a odebrání nebo nahrazení odpovídajících testů částí je vyžadováno, aby řešení fungovalo s vlastním kódem. Postupujte konkrétně takto:

1. Nahradit `[project name]/training/train.py`. Tento skript trénuje váš model místně nebo na azure ml výpočetní.
1. Odstraňte nebo vyměňte testy tréninkových jednotek`[project name]/training/test_train.py`

### <a name="replace-score-code"></a>Nahradit kód skóre

Aby model poskytoval možnosti odvození v reálném čase, je třeba nahradit kód skóre. Šablona MLOpsPython používá kód skóre k nasazení modelu k vyhodnocování v reálném čase v aplikacích ACI, AKS nebo webových aplikacích. Pokud chcete pokračovat v `[project name]/scoring/score.py`bodování, vyměňte .

### <a name="update-evaluation-code"></a>Aktualizovat kód hodnocení

Šablona MLOpsPython používá skript evaluate_model k porovnání výkonu nově trénovaného modelu a aktuálního produkčního modelu založeného na střední kvadratických chybách. Pokud je výkon nově trénovaného modelu lepší než aktuální produkční model, budou kanály pokračovat. V opačném případě jsou kanály zrušeny. Chcete-li zachovat hodnocení, `mse` nahraďte všechny instance insadin `[project name]/evaluate/evaluate_model.py` s metrikou, kterou chcete.

Chcete-li se zbavit vyhodnocení, nastavte `RUN_EVALUATION` proměnnou `.pipelines/[project name]-variables-template.yml` `false`kanálu DevOps do .

## <a name="next-steps"></a>Další kroky

Nyní, když jste pochopili, jak převést z experimentu na produkční kód, použijte následující odkazy, které se dozvíte, jak sledovat spuštění experimentu a modely nasazené jako webové služby:

> [!div class="nextstepaction"]
> [Monitorování spuštění experimentu Azure ML a metrik monitorování](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [a shromažďování dat z koncových bodů webové služby ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
