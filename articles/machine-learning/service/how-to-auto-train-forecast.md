---
title: Automatické učení modelu prognózy časových řad
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning ke studiu odhadu regresního modelu časových řad pomocí automatizovaného strojového učení.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: eb13e6d279ffd8efc0cdb5ce675b77aac5be9c18
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436630"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatické učení modelu prognózy časových řad

V tomto článku se naučíte, jak pomocí automatizovaného strojového učení v Azure Machine Learning naučit regresní model předpovědi časových řad. Konfigurace modelu prognózy je podobná nastavení standardního regresního modelu pomocí automatizovaného strojového učení, ale pro práci s daty časových řad existují některé možnosti konfigurace a postup předběžného zpracování. Následující příklady vám ukážou, jak:

* Příprava dat pro modelování časových řad
* Konfigurace konkrétních parametrů časové řady v objektu [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)
* Spuštění předpovědi s daty časových řad

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Pomocí automatizovaného ML můžete kombinovat techniky a přístupy a získat doporučenou a vysoce kvalitní předpověď časových řad. Automatický experiment s časovou řadou se považuje za problém lineární regrese. Hodnoty za časovou řadou jsou "pivoted" a stanou se dalšími dimenzemi pro regresor společně s jinými koproměnnými.

Tento přístup, na rozdíl od metod klasických časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vzájemný vztah během školení. V aplikacích pro předpověď reálného světa může předpověď ovlivnit více faktorů. Například při prognózování prodeje, interakcí s historickými trendy, směnného kurzu a ceny budou všechny společně řídit výsledek prodeje. Další výhodou je, že všechny nedávné inovace v regresních modelech se okamžitě použijí na prognózy.

Můžete [nakonfigurovat](#config) , jak daleko do budoucna má být prognóza rozšířena (horizont předpovědi), a také prodlevy a další. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady.

Funkce extrahované ze školicích dat hrají důležitou roli. Automatizované ML a vychází ze standardních kroků předběžného zpracování a generuje další funkce časových řad, které zaznamenávají sezónní účinky a maximalizují prediktivní přesnost.

## <a name="prerequisites"></a>Předpoklady

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).
* Tento článek předpokládá základní znalost s nastavením automatizovaného experimentu strojového učení. Pomocí [kurzu](tutorial-auto-train-models.md) nebo [postupu](how-to-configure-auto-train.md) si můžete prohlédnout základní modely návrhu experimentů pro strojové učení.

## <a name="preparing-data"></a>Příprava dat

Nejdůležitější rozdíl mezi typem úkolu regrese regrese a typem úlohy regrese v rámci automatizovaného strojového učení je zahrnutí funkce do vašich dat, která představuje platnou časovou řadu. Pravidelná časová řada má jasně definovanou a konzistentní frekvenci a má hodnotu pro každý vzorový bod v souvislém časovém intervalu. Vezměte v úvahu následující snímek souboru `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Tato datová sada je jednoduchý příklad každodenních prodejních dat pro společnost, která má dvě různá úložiště, a a B. Navíc je k dispozici funkce `week_of_year`, která umožňuje, aby model zjišťoval týdenní sezónnost. Pole `day_datetime` představuje čistou časovou řadu s denní frekvencí a pole `sales_quantity` je cílovým sloupcem pro spuštění předpovědi. Přečtěte si data do PANDAS dataframe a pomocí funkce `to_datetime` zajistěte, aby časová řada byla typu `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě se data již řadí vzestupně podle pole čas `day_datetime`. Při nastavování experimentu se ale ujistěte, že požadovaný sloupec čas je seřazen vzestupně, aby se vytvořila platná časová řada. Předpokládejme, že data obsahují 1 000 záznamů a vytvoří deterministické rozdělení dat pro vytváření školicích a testovacích sad dat. Identifikujte název sloupce popisku a nastavte jej na popisek. V tomto příkladu bude popisek `sales_quantity`. Pak pole popisku oddělte od `test_data`, aby se nastavila sada `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Při výuce modelu pro předpověď budoucích hodnot se ujistěte, že všechny funkce používané v rámci školení můžou být použité pro předpovědi pro zamýšlené horizonty. Například při vytváření prognózy poptávky, včetně funkce pro aktuální cenu akcií, se může zvýšit přesnost školení. Pokud máte v úmyslu předpovědi s dlouhým horizontem, možná nebudete schopni přesně předpovědět budoucí hodnoty v budoucích zásobách, které odpovídají budoucím bodům časových řad, a přesnost modelu by mohla být zhoršená.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurace a spuštění experimentu

Pro úlohy předpovědi používá automatizované strojové učení kroky předběžného zpracování a odhadu, které jsou specifické pro data časových řad. Spustí se následující kroky předběžného zpracování:

* Umožňuje detekovat četnost vzorků časové řady (např. každou hodinu, denně, týdně) a vytvářet nové záznamy pro nepřítomné časové body, aby se řada mohla spojit.
* Imputace chybějící hodnoty v cíli (prostřednictvím předávaného sloupce) a sloupců funkcí (pomocí hodnot sloupců mediánu)
* Vytvoření funkcí založených na zrnitosti, které umožní pevné efekty v různých řadách
* Vytváření funkcí založených na čase, které vám pomůžou při učení se sezónními vzory
* Kódovat proměnné kategorií na číselné množství

Objekt `AutoMLConfig` definuje nastavení a data potřebná pro úlohu automatizovaného strojového učení. Podobně jako u regresního problému definujete standardní parametry školení, jako je typ úkolu, počet iterací, školicích dat a počet křížových ověření. Pro úlohy prognózy existují další parametry, které musí být nastaveny, které mají vliv na experiment. Následující tabulka vysvětluje jednotlivé parametry a jejich použití.

| Bajty | Popis | Požaduje se |
|-------|-------|-------|
|`time_column_name`|Slouží k zadání sloupce data a času ve vstupních datech použitých k vytvoření časové řady a odvození frekvence.|✓|
|`grain_column_names`|Názvy definující jednotlivé skupiny řad ve vstupních datech. Pokud není sada zrn definována, předpokládá se, že datová sada bude jedna časová řada.||
|`max_horizon`|Definuje maximální požadovaný horizont prognózy v jednotkách časové řady. Jednotky jsou založené na časovém intervalu vašich školicích dat, třeba měsíčně, týdně, kdy by měl prognóza předpovědět.|✓|
|`target_lags`|Počet řádků pro prodlevu cílových hodnot na základě frekvence dat Tato hodnota je vyjádřena jako seznam nebo jedno celé číslo. Je nutné použít prodlevu v případě, že vztah mezi nezávislými proměnnými a závislými proměnnou se ve výchozím nastavení neshoduje nebo koreluje. Například při pokusu o Předpověď poptávky za produkt může být poptávka v jakémkoli měsíci závislá na ceně konkrétních komoditních 3 měsíců předem. V tomto příkladu můžete chtít, aby se cíl (poptávka) negativně zavedl 3 měsíce, aby model byl školením správného vztahu.||
|`target_rolling_window_size`|*n* historická období, která se mají použít ke generování předpokládaných hodnot, < = velikost sady školení Pokud tento parametr vynecháte, *n* je úplná velikost sady školení. Tento parametr zadejte, pokud chcete při výuce modelu vzít v úvahu jen určitou velikost historie.||

Další informace najdete v [referenční dokumentaci](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) .

Vytvořte nastavení časových řad jako objekt Dictionary. Nastavte `time_column_name` na pole `day_datetime` v sadě dat. Definujte parametr `grain_column_names`, aby se zajistilo, že se pro data vytvoří **dvě samostatné skupiny časových řad** . jednu pro Store a a B. Nakonec nastavte `max_horizon` na 50, aby se předpovídá celá sada testů. Nastavte okno prognózy na 10 teček s `target_rolling_window_size` a zadejte jednu prodlevu pro cílové hodnoty pro 2 tečky předem s parametrem `target_lags`.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

Definováním `grain_column_names` ve výše uvedeném fragmentu kódu AutoML vytvoří dvě samostatné skupiny časových řad, označované také jako více časových řad. Pokud není definován žádný zrnitý, AutoML bude předpokládat, že datová sada je jediná časová řada. Další informace o jednotlivých časových řadách najdete v [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Nyní vytvořte standardní objekt `AutoMLConfig`, který určuje typ úlohy `forecasting` a odešlete experiment. Po dokončení modelu načtěte nejlepší iteraci spuštění.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Podrobné příklady kódu pro pokročilou konfiguraci prognózování najdete v [poznámkovém bloku k energetickému vyžádání](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) , včetně:

* detekce svátků a featurization
* křížové ověření pro návratové zdroje
* konfigurovatelné prodlevy
* souhrnné funkce kumulovaných oken

### <a name="view-feature-engineering-summary"></a>Zobrazit souhrn metodologie funkcí

V případě typů úloh časových řad v automatizovaném strojovém učení můžete zobrazit podrobnosti z procesu technické analýzy funkcí. Následující kód ukazuje jednotlivé nezpracované funkce spolu s následujícími atributy:

* Nezpracovaný název funkce
* Počet vydaných funkcí, které byly vytvořeny z této nezpracované funkce
* Zjištěn typ
* Určuje, zda byla funkce vyřazena.
* Seznam transformací funkcí pro nezpracované funkce

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognózování s nejlepším modelem

Použijte nejlepší modelovou iteraci pro předpověď hodnot sady dat testu.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternativně můžete použít funkci `forecast()` místo `predict()`, což umožní specifikace, kdy se má předpovědi spustit. V následujícím příkladu nahradíte nejprve všechny hodnoty v `y_pred` s `NaN`. V takovém případě bude zdroj prognózy na konci školicích dat, protože by se normálně používal `predict()`. Pokud jste však nahradili pouze druhou polovinu `y_pred` s `NaN`, funkce by tyto číselné hodnoty v první polovině nezměnila, ale předpověď hodnoty `NaN` v druhé polovině. Funkce vrátí předpovězené hodnoty i zarovnané funkce.

Můžete také použít parametr `forecast_destination` ve funkci `forecast()` k předpovědi hodnot až do zadaného data.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Vypočítá RMSE (chyba čtvercového významu) mezi skutečnými hodnotami `actual_labels` a předpovězenými hodnotami v `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

Teď, když je zjištěná přesnost celkového modelu, je nejrealističtějším dalším krokem použití modelu k předpovědi neznámých budoucích hodnot. Jednoduše poskytněte sadu dat ve stejném formátu jako sadu testů `test_data`, ale s budoucími hodnotami DateTime a výslednou předpokládanou sadou je předpověď hodnot pro každý krok časové řady. Předpokládejte, že poslední záznamy časových řad v datové sadě byly pro 12/31/2018. Chcete-li předpovědět poptávku pro následující den (nebo tolik období, kolik potřebujete pro předpověď, < = `max_horizon`), vytvořte jeden záznam časových řad pro každé úložiště pro 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Opakujte potřebné kroky pro načtení těchto budoucích dat do datového rámce a potom spusťte `best_run.predict(test_data)` a předpovědět budoucí hodnoty.

> [!NOTE]
> Hodnoty nelze odhadnout pro počet období, který je větší než `max_horizon`. Model musí být znovu vyškolen s větším horizontem, aby bylo možné předpovědět budoucí hodnoty nad rámec aktuálního horizontu.

## <a name="next-steps"></a>Další kroky

* V tomto [kurzu](tutorial-auto-train-models.md) se naučíte vytvářet experimenty pomocí automatizovaného strojového učení.
* Podívejte se na referenční dokumentaci k [sadě Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
