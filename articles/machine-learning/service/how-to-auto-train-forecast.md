---
title: Automatické učení modelu prognózy časových řad
titleSuffix: Azure Machine Learning service
description: Naučte se používat službu Azure Machine Learning ke studiu předpovědi regresního modelu časových řad pomocí automatizovaného strojového učení.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 34902aa23339b62920f918ae19b410a99e226a0e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358808"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatické učení modelu prognózy časových řad

V tomto článku se naučíte, jak pomocí automatizovaného strojového učení ve službě Azure Machine Learning proškolit regresní model předpovědi časových řad. Konfigurace modelu prognózy je podobná nastavení standardního regresního modelu pomocí automatizovaného strojového učení, ale pro práci s daty časových řad existují některé možnosti konfigurace a postup předběžného zpracování. Následující příklady vám ukážou, jak:

* Příprava dat pro modelování časových řad
* Konfigurace určitých parametrů časových řad v [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objektu
* Spuštění předpovědi s daty časových řad

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Pomocí automatizovaného ML můžete kombinovat techniky a přístupy a získat doporučenou a vysoce kvalitní předpověď časových řad. Automatický experiment s časovou řadou se považuje za problém lineární regrese. Hodnoty za časovou řadou jsou "pivoted" a stanou se dalšími dimenzemi pro regresor společně s jinými koproměnnými. 

Tento přístup, na rozdíl od metod klasických časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vzájemný vztah během školení. V aplikacích pro předpověď reálného světa může předpověď ovlivnit více faktorů. Například při prognózování prodeje, interakcí s historickými trendy, směnného kurzu a ceny budou všechny společně řídit výsledek prodeje. Další výhodou je, že všechny nedávné inovace v regresních modelech se okamžitě použijí na prognózy.

Můžete [nakonfigurovat](#config) , jak daleko do budoucna má být prognóza rozšířena (horizont předpovědi), a také prodlevy a další. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady. 

Funkce extrahované ze školicích dat hrají důležitou roli. Automatizované ML a vychází ze standardních kroků předběžného zpracování a generuje další funkce časových řad, které zaznamenávají sezónní účinky a maximalizují prediktivní přesnost. 

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [Vytvoření pracovního prostoru služby Azure Machine Learning](setup-create-workspace.md).
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

Tato datová sada je jednoduchý příklad každodenních prodejních dat pro společnost, která má dvě různá úložiště, a a B. Navíc je k dispozici funkce `week_of_year` , která umožňuje, aby model zjišťoval týdenní sezónnost. Pole `day_datetime` představuje čistou časovou řadu s denní frekvencí a pole `sales_quantity` je cílovým sloupcem pro spuštění předpovědi. Přečtěte si data do PANDAS dataframe a pak použijte `to_datetime` funkci, abyste zajistili, že časová řada `datetime` je typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě jsou data již seřazena vzestupně podle pole `day_datetime`čas. Při nastavování experimentu se ale ujistěte, že požadovaný sloupec čas je seřazen vzestupně, aby se vytvořila platná časová řada. Předpokládejme, že data obsahují 1 000 záznamů a vytvoří deterministické rozdělení dat pro vytváření školicích a testovacích sad dat. Pak rozdělte cílové pole `sales_quantity` , aby se vytvořila předpověď předpovědi a sady testů.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
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

`AutoMLConfig` Objekt definuje nastavení a data potřebná pro úkol automatizovaného strojového učení. Podobně jako u regresního problému definujete standardní parametry školení, jako je typ úkolu, počet iterací, školicích dat a počet křížových ověření. Pro úlohy prognózy existují další parametry, které musí být nastaveny, které mají vliv na experiment. Následující tabulka vysvětluje jednotlivé parametry a jejich použití.

| Param | Popis | Požadováno |
|-------|-------|-------|
|`time_column_name`|Slouží k zadání sloupce data a času ve vstupních datech použitých k vytvoření časové řady a odvození frekvence.|✓|
|`grain_column_names`|Názvy definující jednotlivé skupiny řad ve vstupních datech. Pokud není sada zrn definována, předpokládá se, že datová sada bude jedna časová řada.||
|`max_horizon`|Definuje maximální požadovaný horizont prognózy v jednotkách časové řady. Jednotky jsou založené na časovém intervalu vašich školicích dat, třeba měsíčně, týdně, kdy by měl prognóza předpovědět.|✓|
|`target_lags`|*n* období pro přeposílání cílových hodnot před školením modelu||
|`target_rolling_window_size`|*n* historická období, která se mají použít ke generování předpokládaných hodnot, < = velikost sady školení Pokud tento parametr vynecháte, *n* je úplná velikost sady školení.||

Vytvořte nastavení časových řad jako objekt Dictionary. `time_column_name` Nastavtena`day_datetime` pole v datové sadě. Definujte parametr, aby se zajistilo, že se pro data vytvoří **dvě samostatné skupiny časových řad** . jednu pro úložiště a a B. Nakonec nastavte `max_horizon` na 50, aby bylo možné předpovědět celou sadu testů. `grain_column_names` Nastavte okno prognózy na 10 teček `target_rolling_window_size`a prodlevu cílových hodnot 2 teček `target_lags` s parametrem.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Nyní vytvořte standardní `AutoMLConfig` objekt, `forecasting` zadáním typu úkolu a experiment odešlete. Po dokončení modelu načtěte nejlepší iteraci spuštění.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> V případě postupu křížového ověření (CV) můžou data časových řad porušovat základní statistické předpoklady pro křížové ověřování, takže automatizované Machine Learning implementuje postup pro ověření zavedení za provozu. skládání křížového ověřování pro data časových řad. Chcete-li použít tento postup, `n_cross_validations` zadejte parametr `AutoMLConfig` v objektu. Ověřování můžete obejít a použít vlastní ověřovací sady s `X_valid` parametry a. `y_valid`

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
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Alternativně můžete použít `forecast()` funkci `predict()`namísto, což umožní specifikace, kdy by měl předpovědi začít. V následujícím příkladu je třeba nejprve nahradit všechny hodnoty v `y_pred`. `NaN` V takovém případě bude zdroj prognózy na konci školicích dat, jako by to bylo normálně při použití `predict()`. Pokud jste však nahradili pouze druhou polovinu z `y_pred` s `NaN`, funkce by v první polovině `NaN` nezměněných hodnot nechala tyto číselné hodnoty, ale předpověď hodnot v druhé polovině. Funkce vrátí předpovězené hodnoty i zarovnané funkce.

Můžete také použít `forecast_destination` parametr `forecast()` ve funkci k předpovědi hodnot až do zadaného data.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Vypočítá RMSE (znak "root střed_hodn" Error) mezi `y_test` skutečnými hodnotami a předpovězené hodnoty v `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Teď, když je zjištěná přesnost celkového modelu, je nejrealističtějším dalším krokem použití modelu k předpovědi neznámých budoucích hodnot. Jednoduše poskytněte sadu dat ve stejném formátu jako sadu `X_test` testů, ale s budoucími DateTime a výslednou předpokládanou sadou je předpověď hodnot pro každý krok Time-Series. Předpokládejte, že poslední záznamy časových řad v datové sadě byly pro 12/31/2018. Chcete-li odhadnout poptávku pro následující den (nebo tolik období, kolik potřebujete pro předpověď < = `max_horizon`), vytvořte jeden záznam časových řad pro každé úložiště pro 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Zopakováním potřebných kroků načtěte tato budoucí data do datového rámce a potom spusťte příkaz `best_run.predict(X_test)` pro předpověď budoucích hodnot.

> [!NOTE]
> Hodnoty nelze předpovědět pro počet období, který je `max_horizon`větší než. Model musí být znovu vyškolen s větším horizontem, aby bylo možné předpovědět budoucí hodnoty nad rámec aktuálního horizontu.

## <a name="next-steps"></a>Další postup

* V tomto [kurzu](tutorial-auto-train-models.md) se naučíte vytvářet experimenty pomocí automatizovaného strojového učení.
* Podívejte se na referenční dokumentaci k [sadě Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) .
