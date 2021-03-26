---
title: 'Kurz regrese: automatizované ML'
titleSuffix: Azure Machine Learning
description: Vytvořte automatizovaný experiment strojového učení, který pro vás vygeneruje regresní model na základě školicích dat a nastavení konfigurace, která zadáte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python, automl
ms.openlocfilehash: 7f3052905d7594d64be9455c16239ebabd219849
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565074"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Kurz: Využití automatizovaného strojového učení k predikci ceny jízdy taxíkem


V tomto kurzu pomocí automatizovaného strojového učení v Azure Machine Learning vytvoříte regresní model, který předpovídá ceny NYC taxislužby jízdné. Tento proces přijímá školicí data a nastavení konfigurace a automaticky iterovat kombinace různých metod normalizace/normalizace funkcí, modelů a nastavení parametrů pro dosažení nejlepšího modelu.

![Diagram toku](./media/tutorial-auto-train-models/flow2.png)

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Stažení, transformace a vyčištění dat pomocí otevřených datových sad Azure
> * Výuka automatizovaného modelu Machine Learning
> * Vypočítat přesnost modelu

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze](https://aka.ms/AMLFree) Azure Machine Learning dnes

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo notebookem, dokončete [kurz instalace](tutorial-1st-experiment-sdk-setup.md) .
* Po dokončení kurzu instalace otevřete Poznámkový blok *kurzy/Regression-automl-NYC-taxi-data/Regression-Automated-ml. ipynb* pomocí stejného serveru poznámkového bloku.

Tento kurz je také k dispozici na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , pokud ho chcete spustit ve vašem vlastním [místním prostředí](how-to-configure-environment.md#local). Chcete-li získat požadované balíčky, 
* [Nainstalujte úplného `automl` klienta](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment).
* Spusťte `pip install azureml-opendatasets azureml-widgets` , abyste získali požadované balíčky.

## <a name="download-and-prepare-data"></a>Stažení a Příprava dat

Importujte potřebné balíčky. Balíček Open DataSets obsahuje třídu reprezentující jednotlivé zdroje dat ( `NycTlcGreen` například), aby bylo možné před stažením snadno filtrovat parametry data.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Začněte tím, že vytvoříte datový rámec, který bude uchovávat data taxislužby. Při práci v prostředí, které není Spark, mohou otevřené datové sady stahovat pouze jeden měsíc dat s určitými třídami, aby se předešlo velkým datovým `MemoryError` sadám.

Aby bylo možné stahovat data taxislužby, iterativním načítání po jednom měsíci a před jejich připojením k `green_taxi_df` náhodnému vzorkování 2 000 záznamů z každého měsíce, aby se zabránilo bloatingí datového rámce. Pak zobrazte náhled dat.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType |fareAmount |velký| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Žádné|Žádné|-73,88|40,84|-73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|pak|16,30|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Žádné|Žádné|-73,96|40,81|-73,96|...|2|4,50|1.00|0,50|0.3|0,00|0,00|pak|6,30|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Žádné|Žádné|-73,92|40,76|-73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|pak|4,80|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Žádné|Žádné|-73,81|40,70|-73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|pak|13,80|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Žádné|Žádné|-73,92|40,76|-73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|pak|5,00|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Žádné|Žádné|-73,96|40,72|-73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|pak|7,80|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Žádné|Žádné|-73,88|40,76|-73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|pak|6,80|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Žádné|Žádné|-73,96|40,72|-73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|pak|13,80|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Žádné|Žádné|-73,94|40,71|-73,95|...|1|7,00|0,00|0,50|0.3|1.75|0,00|pak|9,55|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Žádné|Žádné|-73,94|40,71|-73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|pak|6,30|

Po načtení počátečních dat definujte funkci pro vytvoření různých funkcí založených na čase z pole Datum vyzvednutí. Tím se vytvoří nová pole pro číslo měsíce, den v měsíci, den v týdnu a hodina dne a v modelu bude umožněno, aby model platil jako sezónnost založený na čase. Použijte `apply()` funkci na datovém snímku pro iterativní použití `build_time_features()` funkce na každý řádek taxislužby dat.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType|fareAmount  |velký| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Žádné|Žádné|-73,88|40,84|-73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|pak|16,30|1.00|1|11|6|
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Žádné|Žádné|-73,96|40,81|-73,96|...|2|4,50|1.00|0,50|0.3|0,00|0,00|pak|6,30|1.00|1|20|1|
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Žádné|Žádné|-73,92|40,76|-73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|pak|4,80|1.00|1|1|3|
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Žádné|Žádné|-73,81|40,70|-73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|pak|13,80|1.00|1|17|5|
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Žádné|Žádné|-73,92|40,76|-73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|pak|5,00|1.00|1|1|3|
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Žádné|Žádné|-73,96|40,72|-73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|pak|7,80|1.00|1|4|6|
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Žádné|Žádné|-73,88|40,76|-73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|pak|6,80|1.00|1|3|5|
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Žádné|Žádné|-73,96|40,72|-73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|pak|13,80|1.00|1|9|4|
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Žádné|Žádné|-73,94|40,71|-73,95|...|1|7,00|0,00|0,50|0.3|1.75|0,00|pak|9,55|1.00|1|11|6|
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Žádné|Žádné|-73,94|40,71|-73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|pak|6,30|1.00|1|22|3|

Odeberte některé sloupce, které nebudete potřebovat pro školení, nebo pro vytváření dalších funkcí.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Vyčistit data

Spuštěním `describe()` funkce na novém dataframe zobrazíte souhrnnou statistiku pro každé pole.

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|
|count|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|
|mean|1,78|1,37|2,87|-73,83|40,69|-73,84|40,70|14,75|6,50|15,13|
|std|0,41|1,04|2.93|2,76|1,52|2,61|1,44|12,08|3,45|8,45|
|min|1.00|0,00|0,00|-74,66|0,00|-74,66|0,00|-300,00|1.00|1.00|
|25%|2,00|1.00|1.06|-73,96|40,70|-73,97|40,70|7,80|3,75|8,00|
|50%|2,00|1.00|1,90|-73,94|40,75|-73,94|40,75|11,30|6,50|15,00|
|75%|2,00|1.00|3,60|-73,92|40,80|-73,91|40,79|17,80|9,25|22,00|
|max|2,00|9,00|97,57|0,00|41,93|0,00|41,94|450,00|12,00|30,00|


V souhrnných statistikách vidíte, že existuje několik polí, která mají mimo jiné pole nebo hodnoty, které budou snižovat přesnost modelu. Nejdříve vyfiltrujte pole lat/Long tak, aby byla v mezích oblasti Manhattan. Tím se vyfiltruje déle taxislužby cest nebo cest, které jsou ve vztahu k ostatním funkcím.

Kromě toho vyfiltrujte `tripDistance` pole tak, aby bylo větší než nula, ale menší než 31 mil. (vzdálenost Haversine mezi dvěma páry lat/Long). Tím se eliminují dlouhé izolované cesty, které mají nekonzistentní náklady na služební cestu.

Nakonec `totalAmount` má pole záporné hodnoty pro taxislužby tarify, které nedávají smysl v kontextu našeho modelu, a `passengerCount` pole obsahuje chybná data s minimálními hodnotami nula.

Vyfiltrujte tyto anomálie pomocí funkcí dotazů a pak odeberte poslední sloupce, které nejsou potřebné pro školení.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Zavolejte `describe()` znovu na data, abyste zajistili, že čištění bude pracovat podle očekávání. Teď máte připravenou a vyčištěnou sadu dat taxislužby, svátků a počasí, která se použijí pro školení modelů ve strojovém učení.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurace pracovního prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. [Pracovní prostor](/python/api/azureml-core/azureml.core.workspace.workspace) je třída, která přijímá vaše předplatné a informace o prostředcích Azure. Vytvoří také cloudový prostředek pro monitorování a sledování spuštění modelu. `Workspace.from_config()` přečte soubor **config.jsv** a načte podrobnosti ověřování do objektu s názvem `ws` . `ws` se používá ve zbývající části kódu v tomto kurzu.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Rozdělení dat do vlakových a testovacích sad

Data rozdělte do školicích a testovacích sad pomocí `train_test_split` funkce v `scikit-learn` knihovně. Tato funkce oddělí data do sady dat x (**Features**) pro školení modelů a datovou sadu y (**hodnoty pro předpověď**) pro testování.

`test_size`Parametr určuje procentuální hodnotu dat, která se mají přidělit pro testování. `random_state`Parametr nastaví počáteční hodnotu pro náhodný generátor, aby vaše výukové testy byly deterministické.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Účelem tohoto kroku je, aby bylo možné otestovat dokončený model, který se nepoužil k analýze modelu, aby se měřila skutečná přesnost.

Jinými slovy, dobře vycvičený model by měl být schopný přesně předpovědi data z dat, která ještě nikdo neviděl. Teď máte připravená data pro automatické školení modelu strojového učení.

## <a name="automatically-train-a-model"></a>Automatické učení modelu

K automatickému učení modelu proveďte následující kroky:
1. Definujte nastavení pro spuštění experimentu. Připojte školicí data ke konfiguraci a upravte nastavení, které řídí proces školení.
1. Odešlete experiment pro vyladění modelu. Po odeslání experimentu se proces prochází pomocí různých algoritmů strojového učení a nastavení vlastních parametrů, které dodržuje vaše definovaná omezení. Vybírá model nejlépe vyhovující optimalizacim metriky přesnosti.

### <a name="define-training-settings"></a>Definování nastavení školení

Definujte parametr experimentu a nastavení modelu pro školení. Zobrazí úplný seznam [Nastavení](how-to-configure-auto-train.md). Odeslání experimentu s těmito výchozími nastaveními bude trvat přibližně 5-20 min, ale pokud budete chtít kratší dobu běhu, snižte `experiment_timeout_hours` parametr.

|Vlastnost| Hodnota v tomto kurzu |Description|
|----|----|---|
|**iteration_timeout_minutes**|10|Časový limit pro každou iteraci v minutách Zvyšte tuto hodnotu u větších datových sad, které pro každou iteraci vyžadují více času.|
|**experiment_timeout_hours**|0.3|Maximální doba v hodinách, po kterou můžou všechny iterace v kombinaci trvat, než se experiment ukončí.|
|**enable_early_stopping**|Ano|Příznak, který povolí předčasné ukončení, pokud se skóre v krátkém období nezlepšuje.|
|**primary_metric**| spearman_correlation | Metrika, kterou chcete optimalizovat Model nejlépe přizpůsoben se vybere na základě této metriky.|
|**featurization**| auto | Pomocí **auto** může experiment předzpracovat vstupní data (zpracování chybějících dat, převod textu na číslo atd.).|
|**podrobností**| logging.INFO | Řídí úroveň protokolování.|
|**n_cross_validations**|5|Počet rozdělení křížového ověření, které se mají provést, pokud nejsou zadaná ověřovací data.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Použijte vaše definovaná nastavení školení jako `**kwargs` parametr `AutoMLConfig` objektu. Dále určete vaše školicí údaje a typ modelu, který je `regression` v tomto případě.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

### <a name="train-the-automatic-regression-model"></a>Analýza automatického regresního modelu

Vytvořte v pracovním prostoru objekt experiment. Experiment funguje jako kontejner pro vaše jednotlivá spuštění. Předejte definovaný `automl_config` objekt experimentu a nastavte výstup na k `True` zobrazení průběhu během běhu.

Po zahájení experimentu se výstup v rámci spuštění experimentu zobrazí jako živý. Pro každou iteraci vidíte typ modelu, dobu trvání běhu a přesnost školení. Pole `BEST` sleduje nejlepší průběžné školení na základě typu metriky.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "Tutorial-NYCTaxi")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Kontrola výsledků

Prozkoumejte výsledky automatického školení pomocí [widgetu Jupyter](/python/api/azureml-widgets/azureml.widgets). Pomůcka vám umožní zobrazit graf a tabulku všech jednotlivých iterací spuštění spolu s metrikami přesnosti školení a metadaty. Kromě toho můžete filtrovat různé metriky přesnosti, než je vaše primární metrika, pomocí rozevíracího selektoru.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget detail Run Details ](./media/tutorial-auto-train-models/automl-dash-output.png)
 ![ Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Načtení nejlepšího modelu

Vyberte nejlepší model z vašich iterací. `get_output`Funkce vrátí nejlepší běh a namontovaný model pro poslední vyvolání. Pomocí přetížení `get_output` můžete načíst nejlepší běh a namontovaný model pro všechny protokolované metriky nebo konkrétní iterace.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testování nejlepší přesnosti modelu

Použijte nejlepší model pro spuštění předpovědi na testovacích datech sady pro předpověď taxislužby tarifů. Funkce `predict` využívá nejlepší model a předpovídá hodnoty y, **nákladů na cestu** ze `x_test` sady dat. Vytiskněte prvních 10 předpokládaných hodnot nákladů z `y_predict` .

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Vypočítá `root mean squared error` výsledky. Převeďte `y_test` datový rámec na seznam pro porovnání s předpokládanými hodnotami. Funkce `mean_squared_error` přebírá dvě pole hodnot a vypočítá průměrnou kvadratickou chybu mezi nimi. Výsledkem druhé odmocniny výsledku je chyba ve stejných jednotkách jako proměnná y, **náklady**. Uvádí zhruba, jak daleko se taxislužby tarif předpovědi ze skutečné tarify.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Spusťte následující kód, který vypočítá průměrnou absolutní procentuální chybu (MAPE) pomocí úplné `y_actual` a `y_predict` datové sady. Tato metrika vypočítá absolutní rozdíl mezi každou předpovězenou a skutečnou hodnotou a sečte všechny rozdíly. Pak vyjadřuje, že součet je procentuální podíl celkového počtu skutečných hodnot.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


Ze dvou metrik přesnosti předpovědi vidíte, že model je poměrně dobrý při předvídání taxislužby tarifů z funkcí datové sady, obvykle v rámci +-$4,00 a přibližně 15% chyby.

Tradiční proces vývoje modelu strojového učení je vysoce náročný na prostředky a vyžaduje, aby ke spuštění a porovnání výsledků desítek modelů byly významné znalosti v doméně a časové investice. Používání automatizovaného strojového učení je skvělým způsobem, jak rychle testovat spoustu různých modelů pro váš scénář.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část neprovádějte, pokud máte v plánu spouštět jiné kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, takže se vám neúčtují žádné poplatky.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu automatizovaného strojového učení jste provedli následující úlohy:

> [!div class="checklist"]
> * Nakonfigurovali jste pracovní prostor a připravené data pro experiment.
> * Vyškoleno pomocí automatizovaného regresního modelu místně s vlastními parametry.
> * Prozkoumání a přezkoumání výsledků školení.

[Nasaďte model](tutorial-deploy-models-with-aml.md) pomocí Azure Machine Learning.
