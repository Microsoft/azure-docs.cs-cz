---
title: Spouštění experimentů pomocí automatizovaného ML Azure
description: Spouštění experimentů machine learningu pomocí Apache Spark a Azure Automated ML
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: d4df4ea96f8dafa2f0eb26e27fcc08ab4ec89003
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033570"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Spouštění experimentů pomocí automatizovaného ML a Apache Spark

Azure Machine Learning je cloudové prostředí, které umožňuje výukové, nasazování, automatizaci, správu a sledování modelů strojového učení. 

V tomto kurzu pomocí [automatizovaného strojového učení](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) v Azure Machine Learning vytvoříte regresní model, který předpovídá ceny NYC taxislužby jízdné. Tento proces přijímá školicí data a nastavení konfigurace a automaticky iterovat kombinace různých metod normalizace/normalizace funkcí, modelů a nastavení parametrů pro dosažení nejlepšího modelu.

V tomto kurzu se seznámíte s následujícími úlohami:
- Stažení dat pomocí Apache Spark a otevřených datových sad Azure
- Transformace a vyčištění dat pomocí Apache Sparkch datových rámců
- Výuka automatizovaného modelu Machine Learning
- Vypočítat přesnost modelu

### <a name="before-you-begin"></a>Než začnete
- Pomocí [kurzu Vytvoření fondu Apache Spark](../quickstart-create-apache-spark-pool-studio.md)vytvořte fond Apache Spark.
- Pokud nemáte existující pracovní prostor Azure Machine Learning, dokončete [kurz nastavení Azure Machine Learning pracovního prostoru](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) . 

### <a name="understand-regression-models"></a>Pochopení modelů regrese
*Regresní modely* předpovídá číselné výstupní hodnoty na základě nezávislých předpovídat. V regresi je cílem přispět k navázání vztahu mezi těmito nezávislými proměnnými proměnných odhadem toho, jak jedna proměnná má vliv na ostatní.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Příklad regresní analýzy v datech taxislužby NYC
V tomto příkladu budete pomocí Sparku provádět určitou analýzu dat taxislužby Trips z New York. Data jsou dostupná prostřednictvím [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, počátečním a koncovém času a umístěních, nákladech a dalších zajímavých atributů.

> [!IMPORTANT]
> 
> Při přijímání těchto dat z umístění úložiště se můžou účtovat další poplatky. V následujících krocích vytvoříte model pro předpověď cen NYC taxislužby jízdné. 
> 

##  <a name="download-and-prepare-the-data"></a>Stažení a Příprava dat

1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.) .
   
   > [!Note]
   > 
   > Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontext Spark je automaticky vytvořen za vás při spuštění první buňky kódu.
   >

2. Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor do paměti jako datový rámec přímo. Vytvořte datový rámec Spark, a to tak, že načtěte data prostřednictvím rozhraní API Open DataSet. Tady použijeme schéma dataframe Sparku *pro čtení* vlastností k odvození typů DataTypes a Schema. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. V závislosti na velikosti vašeho fondu Spark (Preview) mohou být nezpracovaná data příliš velká nebo mohou trvat příliš dlouho, než budou fungovat. Tato data můžete filtrovat dolů na menší hodnotu pomocí ```start_date``` ```end_date``` filtrů a. Tím se aplikuje filtr, který vrací měsíc dat. Jakmile máme filtrovaný datový rámec, spustíme také ```describe()``` funkci na novém dataframe pro zobrazení souhrnných statistik pro každé pole. 

   Na základě souhrnných statistik jsme zjistili, že v datech jsou nějaké nesrovnalosti a odlehlé hodnoty. Statistiky například ukazují, že minimální vzdálenost pro cestu je menší než 0. Budeme muset odfiltrovat tyto nepravidelné datové body.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Teď vygenerujeme funkce z datové sady tak, že vyberete sadu sloupců a v poli Datum vyzvednutí se vytvoří různé funkce založené na čase. Vyfiltrujeme také odlehlé hodnoty, které byly zjištěny v předchozím kroku, a pak odebereme poslední několik sloupců, které nejsou potřebné pro školení.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
Jak vidíte, vytvoří se nový datový rámec s dalšími sloupci pro den v měsíci, hodinu vyzvednutí, den v týdnu a celkovou dobu odezvy. 

![Obrázek taxislužby dataframe.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Generování datových sad testů a ověření

Jakmile máme naši finální datovou sadu, můžeme rozdělit data na sady školení a testování pomocí ```random_ split ``` funkce Spark. Pomocí poskytnutých vah Tato funkce náhodně rozdělí data do školicí datové sady pro školení modelů a datovou sadu ověřování pro testování.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Tento krok zajistí, že se budou v datových bodech testovat dokončený model, který se nepoužil pro výuku modelu. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Připojení k pracovní prostor Azure Machine Learning
V Azure Machine Learning  **pracovní prostor** je třída, která přijímá vaše předplatné a informace o prostředcích Azure. Vytvoří také cloudový prostředek pro monitorování a sledování spuštění modelu. V tomto kroku vytvoříme objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Převod datového rámce na Azure Machine Learning datovou sadu
Abychom mohli odeslat vzdálený experiment, bude nutné převést naši datovou sadu na Azure Machine Learning ```TabularDatset``` . [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu tím, že analyzuje zadané soubory.

Následující kód získá existující pracovní prostor a výchozí Azure Machine Learning výchozí úložiště dat. Pak předá do parametru Path úložiště dat a umístění souborů a vytvoří nový ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Obrázek nahrané datové sady](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Odeslat experiment s automatickým ML

#### <a name="define-training-settings"></a>Definování nastavení školení

1. Chcete-li odeslat experiment, je nutné definovat parametr experiment a nastavení modelu pro školení. Úplný seznam nastavení můžete zobrazit [tady](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. Nyní předáte definovaná nastavení školení jako \* \* parametr Kwargs objektu AutoMLConfig. Vzhledem k tomu, že jsme provedli školení ve Sparku, budeme také muset předat kontext Spark, který je automaticky přístupný ```sc``` proměnnou. Kromě toho zadáme školicí údaje a typ modelu, což je v tomto případě regrese.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> Automatické kroky před zpracováním strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselnou atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracováním během školení.

#### <a name="train-the-automatic-regression-model"></a>Analýza automatického regresního modelu 
Nyní vytvoříme objekt experimentu v pracovním prostoru Azure Machine Learning. Experiment funguje jako kontejner pro vaše jednotlivá spuštění. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Po dokončení experimentu bude výstup vracet podrobnosti o dokončených iteracích. Pro každou iteraci vidíte typ modelu, dobu trvání běhu a přesnost školení. Toto pole nejlépe sleduje nejlepší průběžné školení na základě typu metriky.

![Snímek výstupu modelu](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Po odeslání bude experiment AutoML spouštět různé iterace a typy modelů. Tento běh obvykle trvá 1 – 1,5 hodiny. 

#### <a name="retrieve-the-best-model"></a>Načtení nejlepšího modelu
K výběru nejlepšího modelu z vašich iterací použijeme tuto ```get_output``` funkci k vrácení nejlepšího běhu a namontovaného modelu. Následující kód načte nejlepší běh a namontovaný model pro všechny protokolované metriky nebo konkrétní iterace.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Přesnost modelu testu

1. K otestování přesnosti modelu použijeme nejlepší model pro spuštění taxislužby tarif předpovědi v sadě testů dat. ```predict```Funkce používá nejlepší model a předpovídá hodnoty y (množství tarifů) z datové sady ověření. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. Chyba root-střed_hodn-Square (RMSE) je často používaná míra rozdílů mezi ukázkovými hodnotami předpokládanými modelem a zjištěnými hodnotami. Vypočítáme základní průměrnou chybu na základě porovnání y_test datového rámce s hodnotami, které model předpovídá. 

   Funkce ```mean_squared_error``` přebírá dvě pole a vypočítá průměrnou kvadratickou chybu mezi nimi. Pak povezmeme druhou odmocninu výsledku. Tato metrika udává zhruba, jak daleko se taxislužby tarif předpovědi ze skutečných hodnot tarifů.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   Chyba root-střed_hodn-Square je dobrým rozměrem, jak přesně model předpovídá odpověď. Z výsledků se zobrazí, že model je poměrně dobrý při předvídání taxislužby tarifů z funkcí datové sady, obvykle mezi +-$2,00.

3. Spusťte následující kód, který vypočítá průměrnou absolutní procentuální chybu (MAPE). Tato metrika vyjadřuje přesnost v procentech chyby. Provede to tak, že vypočítá absolutní rozdíl mezi každou předpovězenou a skutečnou hodnotou a potom sečte všechny rozdíly. Pak vyjadřuje, že součet jako procento z celkového počtu skutečných hodnot.

   ```python
   # Calculate MAPE and Model Accuracy 
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

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Ze dvou metrik přesnosti předpovědi vidíte, že model je poměrně dobrý pro předpověď taxislužby tarifů z funkcí datové sady. 

4. Po přizpůsobení modelu lineární regrese teď budete muset určit, jak dobře model odpovídá datům. Za tímto účelem vykreslíme skutečné hodnoty jízdného s předpokládaným výstupem. Kromě toho také vypočítáme míru na základě R-čtverce, abyste pochopili, jak se data zavřou na pevně danou regresní čáru.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Snímek obrazovky regresní parcely](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Z výsledků můžeme vidět, že účty měr R-kvadrát pro 95% naší odchylky. Tato funkce je ověřena také skutečnými vyhodnocenými vykreslením. Větší variance, na který je účtován regresní model, se blíží datovým bodům k namontované regresní čáře.  

## <a name="register-model-to-azure-machine-learning"></a>Registrovat model pro Azure Machine Learning
Po ověření našeho nejlepšího modelu můžeme model zaregistrovat pro Azure Machine Learning. Po registraci modelu můžete zaregistrovaný model stáhnout nebo nasadit a získat všechny soubory, které jste zaregistrovali.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Zobrazit výsledky v Azure Machine Learning
Nakonec můžete k výsledkům iterací přistupovat tak, že přejdete do experimentu v pracovní prostor Azure Machine Learning. Tady budete moct dig do dalších podrobností o stavu spuštění, pokusůch o modelech a dalších metrikách modelu. 

![Snímek obrazovky pracovního prostoru AML](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Další kroky
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Kurz Apache Spark MLlib](./apache-spark-machine-learning-mllib-notebook.md)
