---
title: Automatické trénování modelu prognózy časových řad
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí Azure Machine Learning trénovat regresní model prognóz časové řady pomocí automatizovaného strojového učení.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d4e36c0d3838af85768453496a51ecd295c22b93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081841"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatické trénování modelu prognózy časových řad
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak trénovat regresní model prognózy časových řad pomocí automatizovaného strojového učení v Azure Machine Learning. Konfigurace modelu prognózy je podobná nastavení standardního regresního modelu pomocí automatizovaného strojového učení, ale pro práci s daty časových řad existují určité možnosti konfigurace a kroky předběžného zpracování. Následující příklady ukazují, jak:

* Příprava dat pro modelování časových řad
* Konfigurace určitých parametrů časových [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) řad v objektu
* Spuštění předpovědí s daty časových řad

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Pomocí automatického ml můžete kombinovat techniky a přístupy a získat doporučenou, vysoce kvalitní prognózu časových řad. Automatizovaný experiment časových řad je považován za problém regrese s více odchylkami. Minulé hodnoty časových řad jsou "otočné", aby se staly dalšími dimenzemi regresoru spolu s dalšími prediktory.

Tento přístup, na rozdíl od klasických metod časových řad, má výhodu přirozeně zahrnující více kontextových proměnných a jejich vztah k sobě během tréninku. V reálných aplikacích prognostické prognózy může prognózu ovlivnit více faktorů. Například při předpovídání prodeje, interakce historických trendů, směnného kurzu a ceny společně řídit výsledek prodeje. Další výhodou je, že všechny nedávné inovace v regresním modelech se okamžitě vztahují na prognózování.

Můžete [nakonfigurovat,](#config) jak daleko do budoucna by se prognóza měla rozšířit (horizont prognózy), stejně jako zpoždění a další. Automatizované ML učí jeden, ale často interně rozvětvený model pro všechny položky v datové sadě a horizonty předpovědi. Je tedy k dispozici více dat pro odhad parametrů modelu a zobecnění na neviditelné řady bude možné.

Funkce extrahované z trénovacích dat hrají klíčovou roli. Automatizovaná technologie ML provádí standardní kroky předběžného zpracování a generuje další funkce časových řad, které zachycují sezónní efekty a maximalizují prediktivní přesnost.

## <a name="time-series-and-deep-learning-models"></a>Modely časových řad a hloubkového učení


Automatizované ML poskytuje uživatelům nativní časové řady a hluboké učení modely jako součást systému doporučení. Mezi tyto studenty patří:
+ Prorok
+ Auto-ARIMA
+ PrognózaTCN

Automatizované ml hluboké učení umožňuje předvídání univariate a multivariate časových řad dat.

Modely hlubokého učení mají tři vnitřní schopnosti:
1. Mohou se učit od libovolných mapování od vstupů až po výstupy
1. Podporují více vstupů a výstupů
1. Mohou automaticky extrahovat vzorky ve vstupních datech, která se rozprostírají po dlouhých sekvencích.

Vzhledem k větší data, hluboké učení modely, jako je například Microsoft ForecastTCN, můžete zlepšit skóre výsledného modelu. 

Nativní časových řad studentů jsou také poskytovány jako součást automatizované ML. Prorok funguje nejlépe s časovými řadami, které mají silné sezónní účinky a několik sezón historických dat. Prophet je přesná & rychlá, robustní až odlehlé hodnoty, chybějící data a dramatické změny ve vašich časových řadách. 

AutoRegressive Integrated Moving Average (ARIMA) je populární statistická metoda pro prognózování časových řad. Tato technika prognózy se běžně používá v krátkodobých předpovědních scénářích, kde data ukazují důkazy o trendech, jako jsou cykly, které mohou být nepředvídatelné a obtížně modelovatnebo předpovídat. Auto-ARIMA transformuje vaše data do stacionárních dat pro příjem konzistentní a spolehlivé výsledky.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte [si tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
* Tento článek předpokládá základní znalost nastavení automatizovaného experimentu strojového učení. Postupujte [podle kurzu](tutorial-auto-train-models.md) nebo návod [uvidíte](how-to-configure-auto-train.md) základní automatizované strojové učení experiment návrhové vzory.

## <a name="preparing-data"></a> Příprava dat

Nejdůležitější rozdíl mezi typ emisi regresní úlohy prognózy a regresní typ úlohy v rámci automatizovaného strojového učení je zahrnutí funkce v datech, která představuje platné časové řady. Běžná časová řada má dobře definovanou a konzistentní frekvenci a má hodnotu v každém bodovém vzorku v nepřetržitém časovém rozpětí. Zvažte následující snímek `sample.csv`souboru .

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

Tato sada dat je jednoduchým příkladem denních dat o prodeji pro společnost, která má `week_of_year` dva různé obchody, A a B. Navíc existuje funkce, která umožní modelu detekovat týdenní sezónnost. Pole `day_datetime` představuje čistou časovou řadu s `sales_quantity` denní frekvencí a pole je cílovým sloupcem pro spouštění předpovědí. Přečtěte si data do datového rámce `to_datetime` Pandas a pak `datetime` pomocí funkce ověřte, zda je časová řada typem.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě jsou data již seřazena vzestupně podle časového pole `day_datetime`. Při nastavování experimentu však zajistěte, aby byl požadovaný sloupec času seřazen vzestupně a vytvořil tak platnou časovou řadu. Předpokládejme, že data obsahují 1 000 záznamů a proveďte deterministické rozdělení dat k vytvoření trénovacích a testovacích datových sad. Identifikujte název sloupce popisku a nastavte jej na popisek. V tomto příkladu bude `sales_quantity`popisek . Potom oddělte `test_data` pole popisku a vytvořte `test_target` sadu.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Při trénování modelu pro prognózování budoucích hodnot, ujistěte se, že všechny funkce používané v trénování lze použít při spuštění předpovědi pro zamýšlený horizont. Například při vytváření prognózy poptávky, včetně funkce pro aktuální cenu akcií může masivně zvýšit přesnost školení. Pokud však máte v úmyslu předpovídat s dlouhým horizontem, nemusí být možné přesně předpovědět budoucí hodnoty zásob odpovídající budoucím bodům časových řad a přesnost modelu může utrpět.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurace a spuštění experimentu

Pro prognostické úlohy používá automatizované strojové učení kroky předběžného zpracování a odhadu, které jsou specifické pro data časových řad. Budou provedeny následující kroky předběžného zpracování:

* Detekujte frekvenci vzorkování časových řad (například každou hodinu, denně, týdně) a vytvořte nové záznamy pro chybějící časové body, aby se řada spojit.
* Impute chybějící hodnoty v cíli (přes dopředné-výplň) a funkce sloupce (pomocí střední hodnoty sloupců)
* Vytvoření prvků založených na zrnitosti, které umožní pevné efekty v různých řadách
* Vytvářejte funkce založené na čase, které vám pomohou při učení sezónních vzorců
* Zakódovat kategorické proměnné do číselných veličin

Objekt [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definuje nastavení a data potřebná pro úlohu automatizovaného strojového učení. Podobně jako regresní problém definujete standardní trénovací parametry, jako je typ úlohy, počet iterací, trénovací data a počet křížových ověření. Pro prognostické úkoly existují další parametry, které musí být nastaveny, které ovlivňují experiment. Následující tabulka vysvětluje každý parametr a jeho použití.

| Název&nbsp;parametru | Popis | Požaduje se |
|-------|-------|-------|
|`time_column_name`|Slouží k určení sloupce datetime ve vstupních datech použitých pro vytváření časových řad a odvození její frekvence.|✓|
|`grain_column_names`|Názvy definující jednotlivé skupiny řad ve vstupních datech. Pokud zrnitost není definována, předpokládá se, že sada dat je jedna časová řada.||
|`max_horizon`|Definuje maximální požadovaný horizont prognózy v jednotkách frekvence časových řad. Jednotky jsou založeny na časovém intervalu vašich trénovacích dat, například měsíčně, týdně, který by měl prognostik předpovědět.|✓|
|`target_lags`|Počet řádků zpoždění cílové hodnoty na základě frekvence dat. Zpoždění je reprezentováno jako seznam nebo jedno celé číslo. Lag by měl být použit v případě, že vztah mezi nezávislými proměnnými a závislou proměnnou neodpovídá nebo ve výchozím nastavení koreluje. Například při pokusu o prognózu poptávky po produktu může poptávka v libovolném měsíci záviset na ceně konkrétních komodit před 3 měsíci. V tomto příkladu můžete chtít zaostávat cíl (poptávka) negativně o 3 měsíce tak, aby model je školení na správný vztah.||
|`target_rolling_window_size`|*n* historická období pro generování předpovídaných hodnot, <= velikost trénovací sady. Pokud je vynechán, *n* je plná velikost tréninkové sady. Zadejte tento parametr, pokud chcete při trénování modelu vzít v úvahu pouze určité množství historie.||
|`enable_dnn`|Povolit prognózování dns.||

Další informace naleznete v [referenční dokumentaci.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Vytvořte nastavení časových řad jako objekt slovníku. Nastavte `time_column_name` `day_datetime` pole v datové sadě. Definujte `grain_column_names` parametr, abyste zajistili, že pro data budou vytvořeny **dvě samostatné skupiny časových řad;** jeden pro obchod A a B. `max_horizon` Nakonec nastavte na 50, aby bylo možné předpovědět pro celou testovací sadu. Nastavte okno prognózy na 10 období s `target_rolling_window_size`, a zadejte jednu prodlevu na cílové hodnoty pro dvě období dopředu s parametrem. `target_lags` Doporučuje se nastavit `max_horizon` `target_rolling_window_size` a `target_lags` "auto", který bude automaticky detekovat tyto hodnoty pro vás. V níže uvedeném příkladu byla pro tyto parametry použita nastavení "auto". 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Automatizované kroky předběžného zpracování strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselné atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi, stejné kroky předběžného zpracování použít během trénování se použijí na vstupní data automaticky.

Definováním výše `grain_column_names` uvedeného fragmentu kódu vytvoří funkce AutoML dvě samostatné skupiny časových řad, označované také jako více časových řad. Pokud není definováno žádné zrnitosti, bude automatická ml předpokládat, že datová sada je jedna časová řada. Další informace o jednotlivých časových řadech najdete v [tématu energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Nyní vytvořte `AutoMLConfig` standardní objekt `forecasting` určující typ úkolu a odešlete experiment. Po dokončení modelu načíst nejlepší spustit iteraci.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
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

Podrobné příklady pokročilé konfigurace prognóz y naleznete v [ukázkových poznámkových blocích prognózy,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) včetně:

* [detekce a featurizace dovolené](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [křížové ověření valivého původu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurovatelné zpoždění](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [agregační funkce převádících oken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurace experimentu povolení dnn

> [!NOTE]
> Podpora DNN pro prognózování v automatizovaném strojovém učení je ve verzi Preview a není podporována pro místní spuštění.

Chcete-li využít názvy DN pro prognózování, `enable_dnn` budete muset nastavit parametr v automatickém použití konfigurace protokolu AutoMLConfig na hodnotu true. 

Doporučujeme používat výpočetní cluster AML s virtuálními zařízeními GPU a alespoň dvěma uzly jako výpočetní cíl. Chcete-li poskytnout dostatek času pro školení DNN k dokončení, doporučujeme nastavit časový rozsah experimentu na minimálně několik hodin.
Další informace o výpočetních formátech AML a velikostech virtuálních zařízení, které zahrnují gpu, najdete v [dokumentaci k výpočetním prostředkům AML](how-to-set-up-training-targets.md#amlcompute) a [o velikosti virtuálních strojů optimalizovaných pro GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Prohlédněte [si poznámkový blok Prognózy výroby nápojů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) pro podrobný příklad kódu využívající dnn.

### <a name="view-feature-engineering-summary"></a>Zobrazit souhrn technických prvků

U typů úloh časových řad v automatizovaném strojovém učení můžete zobrazit podrobnosti z procesu inženýringu funkcí. Následující kód zobrazuje jednotlivé nezpracované funkce spolu s následujícími atributy:

* Nezpracovaný název funkce
* Počet navržených funkcí vytvořených z této nezpracované funkce
* Byl zjištěn typ.
* Zda byla funkce vynechána
* Seznam transformací prvků pro nezpracovaný prvek

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Předpovídání s nejlepším modelem

Použijte nejlepší iteraci modelu k prognóze hodnot pro testovací datovou sadu.

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

Alternativně můžete použít `forecast()` funkci namísto `predict()`, která umožní specifikace, kdy by měly být spouštěny předpovědi. V následujícím příkladu nejprve nahradíte všechny hodnoty v `y_pred` s `NaN`. Předpokládaný původ bude v tomto případě na konci údajů o školení, `predict()`jako by tomu bylo obvykle při použití . Pokud jste však nahradili pouze `y_pred` `NaN`druhou polovinu s , funkce by ponechala číselné `NaN` hodnoty v první polovině beze změny, ale předpověděla hodnoty v druhé polovině. Funkce vrátí předpokládané hodnoty i zarovnané prvky.

Parametr ve `forecast_destination` `forecast()` funkci můžete také použít k prognóze hodnot až do zadaného data.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Vypočítat RMSE (kořenová střední `actual_labels` kvadratická chyba) mezi `predict_labels`skutečnými hodnotami a prognózovanými hodnotami v .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Nyní, když byla určena celková přesnost modelu, je nejrealističtějším dalším krokem použití modelu k předvídaní neznámých budoucích hodnot. Zadej sadu dat ve stejném formátu `test_data` jako testovací sada, ale s budoucímdatem, a výsledná sada předpovědí je předpokládané hodnoty pro každý krok časové řady. Předpokládejme, že poslední záznamy časových řad v sadě dat byly pro 12/ 31 / 2018. Chcete-li předpovědět poptávku pro další den (nebo tolik období, kolik potřebujete předpovědět, <= `max_horizon`), vytvořte pro každý obchod pro 01/01/2019 jeden záznam časových řad.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Opakujte nezbytné kroky k načtení těchto budoucích `best_run.predict(test_data)` dat do datového rámce a potom spustit předpovědět budoucí hodnoty.

> [!NOTE]
> Hodnoty nelze předpovědět pro počet období větší `max_horizon`než . Model musí být přetrénován s větším horizontem, aby bylo možné předpovědět budoucí hodnoty za aktuálním horizontem.

## <a name="next-steps"></a>Další kroky

* Postupujte [podle kurzu](tutorial-auto-train-models.md) a dozvíte se, jak vytvářet experimenty s automatizovaným strojovým učením.
* Podívejte se na referenční dokumentaci azure [machine learningu pro Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
