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
ms.date: 03/09/2020
ms.openlocfilehash: bfb53893031300926944ca97a760aec199f699c0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266436"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatické učení modelu prognózy časových řad
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak pomocí automatizovaného strojového učení v [sadě Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)nakonfigurovat a naučit regresní model předpovědi časových řad. 

Pro používání s nízkým kódem si přečtěte [kurz: Předpověď poptávky pomocí automatizovaného strojového učení s](tutorial-automated-ml-forecast.md) využitím automatizovaného strojového učení v [Azure Machine Learning Studiu](https://ml.azure.com/).

Konfigurace modelu prognózy je podobná nastavení standardního regresního modelu pomocí automatizovaného strojového učení, ale pro práci s daty časových řad existují některé možnosti konfigurace a postup předběžného zpracování. 

Můžete například [nakonfigurovat](#config) , jak daleko do budoucna má být prognóza rozšířena (horizont předpovědi), a také prodlevy a další. Automatizovaná ML seznámí s jedním, ale často interně rozvětveným modelem pro všechny položky v datové sadě a horizontech předpovědi. K dispozici jsou proto další data k odhadování parametrů modelu a generalizace na nedostupné řady.

Následující příklady vám ukážou, jak:

* Příprava dat pro modelování časových řad
* Konfigurace určitých parametrů časových řad v [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) objektu
* Spuštění předpovědi s daty časových řad

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Na rozdíl od metod klasických časových řad jsou hodnoty "pivoted" v hodnotách časových řad automatizované ML po "pivoted", aby se do regresory staly další dimenze a další předpovědi. Tento přístup zahrnuje během školení více kontextových proměnných a jejich vztah mezi sebou. Vzhledem k tomu, že předpověď může ovlivnit několik faktorů, tato metoda se dobře zarovnává s scénáři reálného vývoje. Například při prognózování prodeje, interakcí s historickými trendy, směnného kurzu a ceny budou všechny společně řídit výsledek prodeje. 

Funkce extrahované ze školicích dat hrají důležitou roli. Automatizované ML a vychází ze standardních kroků předběžného zpracování a generuje další funkce časových řad, které zaznamenávají sezónní účinky a maximalizují prediktivní přesnost.

## <a name="time-series-and-deep-learning-models"></a>Modely časových řad a hloubkového učení

Obsáhlý Learning v automatizovaném ML umožňuje prognózování dat univariate a lineární časových řad.

Modely hloubkového učení mají tři vnitřní možnosti:
1. Můžou se učit z libovolného mapování ze vstupů na výstupy.
1. Podporují několik vstupů a výstupů.
1. Můžou automaticky extrahovat vzory ve vstupních datech, která jsou rozložená přes dlouhé sekvence.

Kvalitní modely pro hloubkové učení, jako je ForecastTCN Microsoftu, můžou zlepšit skóre výsledného modelu. Naučte se [Konfigurovat experiment pro obsáhlý Learning](#configure-a-dnn-enable-forecasting-experiment).

Automatizované ML poskytuje uživatelům v rámci systému doporučení jak nativní modely časových řad, tak i obsáhlé učení. 

Modely| Popis | Výhody
----|----|---
Prophet (Preview)|Prophet funguje nejlépe s časovou řadou, která má silné sezónní účinky a několik období historických dat. Pokud chcete tento model využít, nainstalujte ho místně pomocí `pip install fbprophet` . | Přesná & rychlá, robustní k vydaným hodnotám, chybějící data a výrazné změny v časové řadě.
Auto-ARIMA (Preview)|V případě, že jsou data stacionární, provede autoregresivní integrovaný klouzavý průměr (ARIMA). To znamená, že jeho statistické vlastnosti, jako je střední hodnota a rozptyl, jsou v celé sadě konstantní. Pokud například překlopete mince, pravděpodobnost, že se vám povede, je 50%, bez ohledu na překlopení dnes, zítra nebo příštího roku.| Skvělé pro univariate Series, protože minulé hodnoty se používají k předpovědi budoucích hodnot.
ForecastTCN (Preview)| ForecastTCN je neuronové síťový model navržený tak, aby se vypořádat s nejnáročnějšími úkoly prognózování, zachytávání nelineárních místních a globálních trendů ve vašich datech a také vztahů mezi časovými řadami.|Umožňuje využití složitých trendů ve vašich datech a umožňuje se snadno škálovat na největší z datových sad.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Pokud chcete vytvořit pracovní prostor, přečtěte si téma [vytvoření Azure Machine Learningho pracovního prostoru](how-to-manage-workspace.md).
* Tento článek předpokládá základní znalost s nastavením automatizovaného experimentu strojového učení. Pomocí [kurzu](tutorial-auto-train-models.md) nebo [postupu](how-to-configure-auto-train.md) si můžete prohlédnout základní modely návrhu experimentů pro strojové učení.

## <a name="preparing-data"></a> Příprava dat

Nejdůležitější rozdíl mezi typem úkolu regrese regrese a typem úlohy regrese v rámci automatizovaného strojového učení je zahrnutí funkce do vašich dat, která představuje platnou časovou řadu. Pravidelná časová řada má jasně definovanou a konzistentní frekvenci a má hodnotu pro každý vzorový bod v souvislém časovém intervalu. Vezměte v úvahu následující snímek souboru `sample.csv` .

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

Tato datová sada je jednoduchý příklad každodenních prodejních dat pro společnost, která má dvě různá úložiště, a a B. Navíc je k dispozici funkce `week_of_year` , která umožňuje, aby model zjišťoval týdenní sezónnost. Pole `day_datetime` představuje čistou časovou řadu s denní frekvencí a pole `sales_quantity` je cílovým sloupcem pro spuštění předpovědi. Přečtěte si data do PANDAS dataframe a pak použijte `to_datetime` funkci, abyste zajistili, že časová řada je `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

V tomto případě jsou data již seřazena vzestupně podle pole čas `day_datetime` . Při nastavování experimentu se ale ujistěte, že požadovaný sloupec čas je seřazen vzestupně, aby se vytvořila platná časová řada. Předpokládejme, že data obsahují 1 000 záznamů a vytvoří deterministické rozdělení dat pro vytváření školicích a testovacích sad dat. Identifikujte název sloupce popisku a nastavte jej na popisek. V tomto příkladu bude popisek `sales_quantity` . Pak pole label oddělte od `test_data` pro vytvoření `test_target` sady.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Při výuce modelu pro předpověď budoucích hodnot se ujistěte, že všechny funkce používané v rámci školení můžou být použité pro předpovědi pro zamýšlené horizonty. Například při vytváření prognózy poptávky, včetně funkce pro aktuální cenu akcií, se může zvýšit přesnost školení. Pokud máte v úmyslu předpovědi s dlouhým horizontem, možná nebudete schopni přesně předpovědět budoucí hodnoty v budoucích zásobách, které odpovídají budoucím bodům časových řad, a přesnost modelu by mohla být zhoršená.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Data o školeních a ověřováních
V konstruktoru můžete určit samostatné sady vlaků a ověřovacích sad přímo `AutoMLConfig` .

### <a name="rolling-origin-cross-validation"></a>Překročení počátečního ověřování
Pro časovou osu, která provádí průběžné ověřování (ROCV), slouží k rozdělení časových řad do dočasného konzistentního způsobu. ROCV rozdělí řadu na data o školení a ověření pomocí počátečního časového bodu. Posunutí zdroje v čase generuje skládání křížového ověření.  

![alternativní text](./media/how-to-auto-train-forecast/ROCV.svg)

Tato strategie zachovává integritu dat časové řady a eliminuje riziko úniku dat. ROCV se automaticky používá pro prognózování úkolů předáním dat školení a ověření společně a nastavením počtu skládání pro vzájemné ověřování pomocí `n_cross_validations` . Přečtěte si další informace o tom, jak auto ML používá křížové ověřování, aby se [předešlo navýšení modelů](concept-manage-ml-pitfalls.md#prevent-over-fitting)

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Přečtěte si další informace o [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Konfigurace a spuštění experimentu

Pro úlohy předpovědi používá automatizované strojové učení kroky předběžného zpracování a odhadu, které jsou specifické pro data časových řad. Spustí se následující kroky předběžného zpracování:

* Detekuje četnost vzorkování časové řady (například každou hodinu, denně, týdně) a vytvoří nové záznamy pro nepřítomné časové body, aby se řada souvislá.
* Imputace chybějící hodnoty v cíli (prostřednictvím předávaného sloupce) a sloupců funkcí (pomocí hodnot sloupců mediánu)
* Vytvoření funkcí založených na zrnitosti, které umožní pevné efekty v různých řadách
* Vytváření funkcí založených na čase, které vám pomůžou při učení se sezónními vzory
* Kódovat proměnné kategorií na číselné množství

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)Objekt definuje nastavení a data potřebná pro úkol automatizovaného strojového učení. Podobně jako u regresního problému definujete standardní parametry školení, jako je typ úkolu, počet iterací, školicích dat a počet křížových ověření. Pro úlohy prognózy existují další parametry, které musí být nastaveny, které mají vliv na experiment. Následující tabulka vysvětluje jednotlivé parametry a jejich použití.

| &nbsp;Název parametru | Popis | Vyžadováno |
|-------|-------|-------|
|`time_column_name`|Slouží k zadání sloupce data a času ve vstupních datech použitých k vytvoření časové řady a odvození frekvence.|✓|
|`grain_column_names`|Názvy definující jednotlivé skupiny řad ve vstupních datech. Pokud není sada zrn definována, předpokládá se, že datová sada bude jedna časová řada.||
|`max_horizon`|Definuje maximální požadovaný horizont prognózy v jednotkách časové řady. Jednotky jsou založené na časovém intervalu vašich školicích dat, například měsíčně, týdně, kdy by měl prognóza předpovědět.|✓|
|`target_lags`|Počet řádků pro prodlevu cílových hodnot na základě frekvence dat Prodleva je vyjádřena jako seznam nebo jedno celé číslo. Je nutné použít prodlevu v případě, že vztah mezi nezávislými proměnnými a závislou proměnnou se ve výchozím nastavení neshoduje nebo koreluje. Například při pokusu o Předpověď poptávky za produkt může být poptávka v jakémkoli měsíci závislá na ceně konkrétních komoditních 3 měsíců předem. V tomto příkladu můžete chtít, aby se cíl (poptávka) negativně zavedl 3 měsíce, aby model byl školením správného vztahu.||
|`target_rolling_window_size`|*n* historická období, která se mají použít ke generování předpokládaných hodnot, <= velikost sady školení Pokud tento parametr vynecháte, *n* je úplná velikost sady školení. Tento parametr zadejte, pokud chcete při výuce modelu vzít v úvahu jen určitou velikost historie.||
|`enable_dnn`|Povolte prognózování hluboké.||

Další informace najdete v [referenční dokumentaci](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Vytvořte nastavení časových řad jako objekt Dictionary. Nastavte na `time_column_name` `day_datetime` pole v datové sadě. Definujte `grain_column_names` parametr, aby se zajistilo, že se pro data vytvoří **dvě samostatné skupiny časových řad** . jednu pro úložiště a a B. nakonec nastavte na 50, aby `max_horizon` bylo možné předpovědět celou sadu testů. Nastavte okno prognózy na 10 teček s `target_rolling_window_size` a zadejte jednu prodlevu pro cílové hodnoty pro dvě tečky předem s `target_lags` parametrem. Doporučuje se nastavit `max_horizon` `target_rolling_window_size` `target_lags` možnost automaticky, což vám tyto hodnoty automaticky detekuje. V následujícím příkladu se pro tyto parametry používala nastavení "auto". 

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
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

Definováním `grain_column_names` ve výše uvedeném fragmentu kódu AutoML vytvoří dvě samostatné skupiny časových řad, označované také jako více časových řad. Pokud není definován žádný zrnitý, AutoML bude předpokládat, že datová sada je jediná časová řada. Další informace o jednotlivých časových řadách najdete v [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Nyní vytvořte standardní `AutoMLConfig` objekt, zadáním `forecasting` typu úkolu a experiment odešlete. Po dokončení modelu načtěte nejlepší iteraci spuštění.

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

Podrobné příklady kódu pro pokročilou konfiguraci prognózování najdete v [poznámkových blocích ukázkových poznámkách k prognózám](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , včetně:

* [detekce svátků a featurization](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [křížové ověření pro návratové zdroje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurovatelné prodlevy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [souhrnné funkce kumulovaných oken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurace experimentu s DNN povolení prognózování

> [!NOTE]
> Podpora DNN pro prognózování v automatizovaných Machine Learning je ve verzi Preview a není podporovaná pro místní běhy.

Aby bylo možné využít hluboké pro prognózování, budete muset nastavit `enable_dnn` parametr v AutoMLConfig na hodnotu true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Přečtěte si další informace o [AutoMLConfig](#configure-and-run-experiment).

Alternativně můžete vybrat `Enable deep learning` možnost v nástroji Studio.
![alternativní text](./media/how-to-auto-train-forecast/enable_dnn.png)

Doporučujeme použít výpočetní cluster AML s SKU GPU a alespoň dva uzly jako cíl výpočtů. Aby bylo umožněno dostatek času na dokončení školení DNN, doporučujeme nastavit časový limit experimentu na minimálně několik hodin.
Další informace o AML výpočetních a virtuálních počítačích, které zahrnují GPU, najdete v dokumentaci ke [výpočetním dokumentům AML](how-to-set-up-training-targets.md#amlcompute) a na [velikost virtuálních počítačů optimalizovaných pro GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Podrobný příklad kódu, který využívá hluboké, najdete v [poznámkovém bloku pro vytváření předpovědí pro produkci nápojů](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) .

### <a name="target-rolling-window-aggregation"></a>Cílová agregace návratového okna
Nejlepší informace, které může vytvořit předpověď, jsou často poslední hodnotou cíle. Vytváření kumulativních statistik cíle může zvýšit přesnost vašich předpovědi. Cílová agregace kumulovaných oken vám umožní přidat do funkcí hromadnou agregaci hodnot dat. Chcete-li povolit cílovému systému Windows nastavit požadovanou `target_rolling_window_size` velikost okna na celé číslo. 

Příkladem toho lze zobrazit při předvídání poptávky energie. Pro tepelné změny v zahřívanách prostorech můžete přidat funkci posuvných oken po dobu tří dní. V následujícím příkladu jsme vytvořili toto okno o velikosti tři nastavením `target_rolling_window_size=3` v `AutoMLConfig` konstruktoru. V tabulce se zobrazuje inženýr funkcí, který nastane při použití agregace okna. Sloupce pro minimální, maximální a součet se generují na posuvné okno tři na základě definovaných nastavení. Každý řádek obsahuje novou vypočítanou funkci v případě časového razítka pro 8. září 2017:10:00 hodnoty maxima, minima a suma se počítají pomocí hodnot požadavků pro 8. září 2017 1:10:00-3:10:00. V tomto okně se třemi posunutími naplní data pro zbývající řádky.

![alternativní text](./media/how-to-auto-train-forecast/target-roll.svg)

Vytváření a používání těchto dalších funkcí jako dodatečných kontextových dat pomáhá s přesností modelu vlaku.

Podívejte se na příklad kódu Pythonu s využitím [agregované agregační funkce](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)pro souhrnné okno.

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

`forecast()`Funkce by měla být použita místo `predict()` , a to umožní specifikace, pokud by se měla spustit předpovědi. V následujícím příkladu je třeba nejprve nahradit všechny hodnoty v `y_pred` `NaN` . V takovém případě bude zdroj prognózy na konci školicích dat, jako by to bylo normálně při použití `predict()` . Pokud jste však nahradili pouze druhou polovinu z `y_pred` s `NaN` , funkce by v první polovině nezměněných hodnot nechala tyto číselné hodnoty, ale předpověď `NaN` hodnot v druhé polovině. Funkce vrátí předpovězené hodnoty i zarovnané funkce.

Můžete také použít `forecast_destination` parametr ve `forecast()` funkci k předpovědi hodnot až do zadaného data.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Vypočítá RMSE (znak "root střed_hodn" Error) mezi `actual_labels` skutečnými hodnotami a předpovězené hodnoty v `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Teď, když je zjištěná přesnost celkového modelu, je nejrealističtějším dalším krokem použití modelu k předpovědi neznámých budoucích hodnot. Poskytněte datovou sadu ve stejném formátu jako sadu testů `test_data` , ale s budoucími DateTime a výslednou předpokládanou sadou je předpověď hodnot pro každý krok časové řady. Předpokládejte, že poslední záznamy časových řad v datové sadě byly pro 12/31/2018. Chcete-li odhadnout poptávku pro následující den (nebo tolik období, kolik potřebujete pro předpověď <= `max_horizon` ), vytvořte jeden záznam časových řad pro každé úložiště pro 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Zopakováním potřebných kroků načtěte tato budoucí data do datového rámce a potom spusťte příkaz `best_run.predict(test_data)` pro předpověď budoucích hodnot.

> [!NOTE]
> Hodnoty nelze předpovědět pro počet období, který je větší než `max_horizon` . Model musí být znovu vyškolen s větším horizontem, aby bylo možné předpovědět budoucí hodnoty nad rámec aktuálního horizontu.

## <a name="next-steps"></a>Další kroky

* V tomto [kurzu](tutorial-auto-train-models.md) se naučíte vytvářet experimenty pomocí automatizovaného strojového učení.
* Podívejte se na referenční dokumentaci k [sadě Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
