---
title: Automaticky – train model prognózy časových řad
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak pomocí služby Azure Machine Learning algoritmu time series prognóz regresního modelu pomocí automatizované strojového učení.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: c4f94dd2730dd302951b4476a292b006041b7ee8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820042"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automaticky – train model prognózy časových řad

V tomto článku se dozvíte, jak pro trénování časových řad prognóz regresního modelu pomocí automatizovaných strojového učení ve službě Azure Machine Learning. Konfigurace modelu prognózy je podobné nastavení standardní regresního modelu pomocí automatizovaných strojového učení, ale existují určité kroky předběžného zpracování a možnosti konfigurace pro práci s daty časových řad. Následující příklady ukazují, jak do:

* Příprava dat pro čas řady modelování
* Konfigurovat parametry pro konkrétní časových řad v [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) objektu
* Spustit předpovědi s daty časových řad

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, najdete v článku [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md).
* Tento článek předpokládá základní znalost nastavení automatizované experimentu strojového učení. Postupujte podle [kurzu](tutorial-auto-train-models.md) nebo [postupy](how-to-configure-auto-train.md) zobrazíte základní automatizované strojového učení vzory návrhu v experimentu.

## <a name="preparing-data"></a>Příprava dat

Nejdůležitější rozdíl mezi Prognózování úloh typ regrese a regresní typ úkolu v rámci automatizovaného machine learning je včetně funkce ve vašich datech, která představuje platné časové řady. Pravidelné časové řady má frekvenci dobře definovaný a konzistentní vzhledem k aplikacím a má hodnotu v každém bodě ukázka spojitý časový rozsah. Vezměte v úvahu z následujícího snímku souboru `sample.csv`.

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

Tato datová sada je jednoduchý příklad denní prodejní data pro společnosti, která má dvě různé úložiště, A a B. kromě, je funkce pro `week_of_year` , který vám umožní modelu k detekci týdenní sezónnosti. Pole `day_datetime` představuje čisté časové řady s denní frekvence a pole `sales_quantity` je cílový sloupec pro spouštěním predikcí. Načtení dat do struktury Pandas dataframe, a pak pomocí `to_datetime` funkce zkontrolujte časové řady `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě je již řazeno vzestupně podle času pole `day_datetime`. Ale při nastavování experiment, ujistěte se, že sloupec je požadovaný čas je seřazen vzestupně sestavit platný časové řady. Předpokládejme, že data obsahují 1 000 záznamů a deterministický rozdělení ve data vytvoření trénování a testování datových sad. Pak oddělit cílového pole `sales_quantity` vytvořte předpověď trénování a testování sad.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Když trénujete model pro předpověď budoucích hodnot, zajistěte všechno, co funkce použité v školení se dá použít při spouštěním predikcí pro určené horizont. Například při vytváření prognózy poptávky, včetně funkcí pro aktuální ceny akcie může široce zvýšit přesnost školení. Nicméně pokud máte v úmyslu Prognózování s dlouhý časový horizont, nemusí být schopni přesně předpovědět budoucí uložené hodnoty odpovídající budoucí body časových řad a přesnost modelu může být negativně.

## <a name="configure-experiment"></a>Konfigurace testu

Pro předpověď úkoly, používá automatizované strojového učení úkony předběžného zpracování a odhad kroky, které jsou specifické pro data časových řad. Provede následující kroky předběžného zpracování:

* Zjištění časových řad ukázkový frekvence (například hodinové, denní, týdenní) a vytvořit nové záznamy pro chybějící body v čase provádět řadu průběžné.
* Dává chybějících hodnot ve sloupcích funkce (s použitím hodnoty mediánu sloupce) a cíl (prostřednictvím výplně dopředu)
* Vytvoření funkce založené na intervalem povolit pevný efekty napříč různými řadami
* Vytvoření funkcí podle času, které pomáhají studium sezónní vzory
* Kódování zařazené do kategorií proměnné číselnou množství

`AutoMLConfig` Objekt definuje nastavení a data potřebná pro úlohu automatické machine learning. Podobně jako regresní problém, můžete definovat standardní školení parametrů, jako je typ úlohy, počet opakování je vyšší, Cvičná data a počet křížové ověření. Prognózování úloh, existují další parametry, které musí být nastavena, které mají vliv experiment. Následující tabulka popisuje jednotlivé parametry a jejich využití.

| Param | Popis | Požaduje se |
|-------|-------|-------|
|`time_column_name`|Slouží k zadání vstupních dat použít pro vytváření časové řady a jeho četnost odvození sloupce data a času.|✓|
|`grain_column_names`|Názvy, definování skupin jednotlivé řady ve vstupních datech. Pokud není definovaný interval, datová sada se považuje za jednu časových řad.||
|`max_horizon`|Maximální požadovaný prognózy horizontu v jednotkách frekvence časových řad.|✓|
|`target_lags`|*n* období vpřed prodleva cílových hodnot před cvičení modelu.||
|`target_rolling_window_size`|*n* historických období pro generování předpokládaných hodnoty, < = školení nastavení velikosti. Pokud tento parametr vynechán, *n* je nastavení úplné školení velikosti.||

Vytvoření nastavení časových řad jako objekt slovníku. Nastavte `time_column_name` k `day_datetime` pole v datové sadě. Definovat `grain_column_names` parametr zajistit, aby **dva samostatné skupiny časových řad** jsou vytvořené pro data, jeden pro úložiště A a b a konečně, nastavte `max_horizon` na 50, aby bylo možné předpovědět, pro celý test nastavení. Nastavit časové období prognózy do 10 období s `target_rolling_window_size`a prodlevu cílové hodnoty 2 období pokračovat s `target_lags` parametru.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

Teď vytvořte standardní `AutoMLConfig` objektu, určení `forecasting` typ úkolu a odeslání experimentu. Po dokončení modelu načte nejlépe spuštění iterace.

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
> Pro procedury křížového ověřování (CV) data časových řad porušit základní statistické předpoklady canonical strategie K přeložení křížového ověřování, takže automatizované machine learning implementuje postupné postup ověření původu a vytvořte přeložení křížového ověření pro data časových řad. Pomocí tohoto postupu, zadejte `n_cross_validations` parametr `AutoMLConfig` objektu. Můžete obejít ověření a použití vlastní ověřování, nastaví se `X_valid` a `y_valid` parametry.

## <a name="forecasting-with-best-model"></a>Prognózování s nejlepší model

Nejlepší model iterace použijte k předpovědi hodnot pro datovou sadu testů.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Vypočítat RMSE (střední hodnotu kořenové spolehlivosti chyba) mezi `y_test` skutečnými hodnotami a předpokládané hodnoty v `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Teď, když celkové určil přesnost modelu, většina realistické dalším krokem je použít model k předpovědi Neznámý budoucí hodnoty. Jednoduše zadat sadu dat ve stejném formátu jako testovací sada `X_test` ale budoucí data a času a výsledné předpovědi sada je předpokládaných hodnoty pro každý krok časových řad. Předpokládejme, že byly poslední záznamy časových řad v datové sadě pro 12/31. ledna 2018. Předpovídá poptávku za další den (nebo libovolný počet období, kolik je potřeba dala udělat předpověď, < = `max_horizon`), vytvořit jeden čas řady záznam pro každé úložiště pro 01, 01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Opakováním kroků nezbytných k načtení této budoucí dat pro datový rámec a pak spusťte `best_run.predict(X_test)` předpovídat budoucí hodnoty.

> [!NOTE]
> Hodnoty nemůže být předpovězen pro počet období, které jsou větší než `max_horizon`. Model musí být znovu trénovaného s větší horizontu předpovídat budoucí hodnoty mimo aktuální časový horizont.

## <a name="next-steps"></a>Další postup

* Postupujte podle [kurzu](tutorial-auto-train-models.md) naučíte se vytvářet experimenty s automatických strojového učení.
* Zobrazení [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) referenční dokumentaci.
