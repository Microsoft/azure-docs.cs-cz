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
ms.openlocfilehash: e1b584d38c4583e37b7c47535c836d1fa7d428f1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357250"
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

    week_starting,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/10/2018,A,2300,37
    9/10/2018,B,550,37
    9/17/2018,A,2100,38
    9/17/2018,B,650,38
    9/24/2018,A,2400,39
    9/24/2018,B,700,39
    10/1/2018,A,2450,40
    10/1/2018,B,650,40

Tato datová sada je jednoduchý příklad týdenní prodejní data pro společnosti, která má dvě různé úložiště, A a B. kromě, je funkce pro `week_of_year` , který vám umožní modelu k detekci týdenní sezónnosti. Pole `week_starting` představuje čisté časové řady s týdenní frekvenci a pole `sales_quantity` je cílový sloupec pro spouštěním predikcí. Načtení dat do struktury Pandas dataframe, a pak pomocí `to_datetime` funkce zkontrolujte časové řady `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["week_starting"] = pd.to_datetime(data["week_starting"])
```

V tomto případě je již řazeno vzestupně podle času pole `week_starting`. Ale při nastavování experiment, ujistěte se, že sloupec je požadovaný čas je seřazen vzestupně sestavit platný časové řady. Předpokládejme, že data obsahují 1 000 záznamů a deterministický rozdělení ve data vytvoření trénování a testování datových sad. Pak oddělit cílového pole `sales_quantity` vytvořte předpověď trénování a testování sad.

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

Vytvoření nastavení časových řad jako objekt slovníku. Nastavte `time_column_name` k `week_starting` pole v datové sadě. Definovat `grain_column_names` parametr zajistit, aby **dva samostatné skupiny časových řad** jsou vytvořené pro naše data, jeden pro úložiště A a b a konečně, nastavte `max_horizon` na 50, aby bylo možné předpovědět, pro celý test nastavení.

```python
time_series_settings = {
    "time_column_name": "week_starting",
    "grain_column_names": ["store"],
    "max_horizon": 50
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

Teď, když celkové určil přesnost modelu, většina realistické dalším krokem je použít model k předpovědi Neznámý budoucí hodnoty. Jednoduše zadat sadu dat ve stejném formátu jako testovací sada `X_test` ale budoucí data a času a výsledné předpovědi sada je předpokládaných hodnoty pro každý krok časových řad. Předpokládají poslední časové řady záznamy v sadě dat byly pro týden od 12/31. ledna 2018. Předpovídá poptávku pro příští týden (nebo libovolný počet období, kolik je potřeba dala udělat předpověď, < = `max_horizon`), vytvořit jeden čas řady záznam pro každé úložiště pro týden od 01/07/2019.

    week_starting,store,week_of_year
    01/07/2019,A,2
    01/07/2019,A,2

Opakováním kroků nezbytných k načtení této budoucí dat pro datový rámec a pak spusťte `best_run.predict(X_test)` předpovídat budoucí hodnoty.

> [!NOTE]
> Hodnoty nemůže být předpovězen pro počet období, které jsou větší než `max_horizon`. Model musí být znovu trénovaného s větší horizontu předpovídat budoucí hodnoty mimo aktuální časový horizont.

## <a name="next-steps"></a>Další postup

* Postupujte podle [kurzu](tutorial-auto-train-models.md) naučíte se vytvářet experimenty s automatických strojového učení.
* Zobrazení [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) referenční dokumentaci.
