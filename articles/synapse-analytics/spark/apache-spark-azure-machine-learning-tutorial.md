---
title: 'Kurz: výuka modelu v Pythonu pomocí automatizovaného strojového učení'
description: Kurz o tom, jak pomocí Apache Spark a automatizovaného strojového učení naučit model strojového učení v Pythonu
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: fc9909614a9d557c19a22e215b7513a038f88c33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942345"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Kurz: výuka modelu v Pythonu pomocí automatizovaného strojového učení

Azure Machine Learning je cloudové prostředí, které umožňuje výukové, nasazování, automatizaci, správu a sledování modelů strojového učení. 

V tomto kurzu pomocí [automatizovaného strojového učení](../../machine-learning/concept-automated-ml.md) v Azure Machine Learning vytvoříte regresní model pro předpověď cen taxislužby jízdné. Tento proces dorazí na nejlepší model tím, že přijme školicí data a nastavení konfigurace a automaticky provede iteraci pomocí kombinací různých metod, modelů a nastavení parametrů.

V tomto kurzu se naučíte:
- Stáhněte si data pomocí Apache Spark a otevřených datových sad Azure.
- Transformujte a čistě data pomocí Apache Sparkch datových rámců.
- V automatizovaném strojovém učení se naučíte regresní model.
- Vypočítá přesnost modelu.

## <a name="before-you-begin"></a>Než začnete

- Vytvořte fond Apache Spark bez serveru, a to pomocí rychlého startu [Vytvoření fondu Apache Spark bez serveru](../quickstart-create-apache-spark-pool-studio.md) .
- Pokud nemáte existující pracovní prostor Azure Machine Learning, dokončete kurz [nastavení Azure Machine Learning pracovního prostoru](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) . 

## <a name="understand-regression-models"></a>Pochopení modelů regrese

*Regresní modely* předpovídá číselné výstupní hodnoty na základě nezávislých předpovídat. V regresi je cílem přispět k navázání vztahu mezi těmito nezávislými proměnnými proměnných, a to odhadem toho, jak jedna proměnná ovlivňuje ostatní.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Příklad založený na datech v New Yorku City taxislužby

V tomto příkladu pomocí Sparku provedete určitou analýzu dat taxislužby s špičkou z New York City (NYC). Data jsou dostupná prostřednictvím [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, čase zahájení a ukončení a umístění a náklady.

> [!IMPORTANT]
> Při načítání těchto dat ze svého umístění úložiště se můžou účtovat další poplatky. V následujících krocích vyvíjíte model, který předpovídá ceny NYC taxislužby jízdné. 

##  <a name="download-and-prepare-the-data"></a>Stažení a Příprava dat

Jak na to:

1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontext Spark je automaticky vytvořen za vás při spuštění první buňky kódu.
  
2. Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor přímo do paměti jako datový rámec. Vytvořte datový rámec Spark, a to tak, že načtěte data prostřednictvím rozhraní API Open DataSet. Tady můžete pomocí vlastností Spark dataframe `schema on read` odvodit typy a schéma. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. V závislosti na velikosti vašeho fondu Spark může být nezpracovaná data příliš velká nebo mohou trvat příliš dlouho, než budou fungovat. Tato data můžete filtrovat dolů na méně, například za měsíc dat, pomocí ```start_date``` ```end_date``` filtrů a. Po filtrování datového rámce můžete také spustit ```describe()``` funkci na novém dataframe a zobrazit tak souhrnnou statistiku pro každé pole. 

   V závislosti na souhrnných statistikách vidíte, že data jsou v datech nedovolených. Statistiky například ukazují, že minimální vzdálenost pro cestu je menší než 0. Tyto nepravidelné datové body je potřeba odfiltrovat.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Vygenerujte funkce z datové sady výběrem sady sloupců a vytvořením různých funkcí založených na čase z pole Pickup `datetime` . Vyfiltrujte odlehlé hodnoty, které byly zjištěny v předchozím kroku, a pak odeberte poslední několik sloupců, protože nejsou potřebné pro školení.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
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

   ![Obrázek taxislužby dataframe.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Generování datových sad testů a ověření

Až budete mít konečnou datovou sadu, můžete data rozdělit na sady školení a testování pomocí ```random_ split ``` funkce ve Sparku. Pomocí poskytnutých vah Tato funkce náhodně rozdělí data do školicí datové sady pro školení modelů a datovou sadu ověřování pro testování.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Tento krok zajistí, že se pro výuku modelu nepoužily datové body pro otestování dokončeného modelu. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Připojení k pracovnímu prostoru Azure Machine Learning
V Azure Machine Learning pracovní prostor je třída, která přijímá vaše předplatné a informace o prostředcích Azure. Vytvoří také cloudový prostředek pro monitorování a sledování spuštění modelu. V tomto kroku vytvoříte objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning.
   
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
Chcete-li odeslat vzdálený experiment, převeďte datovou sadu na ```TabularDatset``` instanci Azure Machine Learning. [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) představuje data v tabulkovém formátu tím, že analyzuje zadané soubory.

Následující kód získá existující pracovní prostor a výchozí úložiště dat Azure Machine Learning. Poté předá do parametru Path úložiště dat a umístění souborů, aby bylo možné vytvořit novou ```TabularDataset``` instanci. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Obrázek nahrané datové sady](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Odeslání automatizovaného experimentu

V následujících částech se dozvíte, jak postupovat při automatickém experimentování strojového učení.

### <a name="define-training-settings"></a>Definování nastavení školení
1. Chcete-li odeslat experiment, je nutné definovat parametr experiment a nastavení modelu pro školení. Úplný seznam nastavení najdete v tématu [Konfigurace automatických experimentů strojového učení v Pythonu](../../machine-learning/how-to-configure-auto-train.md).

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

1. Předejte definované nastavení školení jako `kwargs` parametr `AutoMLConfig` objektu. Vzhledem k tomu, že používáte Spark, musíte také předat kontext Spark, který je automaticky přístupný pro ```sc``` proměnnou. Navíc zadáte školicí údaje a typ modelu, což je v tomto případě regrese.

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
>Automatické kroky předběžného zpracování Machine Learning se stanou součástí základního modelu. Tyto kroky zahrnují normalizaci funkcí, zpracování chybějících dat a převod textu na číselný. Při použití modelu pro předpovědi se na vstupní data automaticky aplikují stejné kroky před zpracování, které jsou použité během školení.

### <a name="train-the-automatic-regression-model"></a>Analýza automatického regresního modelu 
V dalším kroku vytvoříte objekt experimentu v pracovním prostoru Azure Machine Learning. Experiment funguje jako kontejner pro vaše jednotlivá spuštění. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Po dokončení experimentu vrátí výstup podrobnosti o dokončených iteracích. Pro každou iteraci vidíte typ modelu, dobu trvání běhu a přesnost školení. `BEST`Pole sleduje nejlepší průběžné školení na základě typu metriky.

![Snímek výstupu modelu](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Po odeslání automatizovaného experimentu strojového učení se spustí různé iterace a typy modelů. Tento běh obvykle trvá 60 až 90 minut. 

### <a name="retrieve-the-best-model"></a>Načtení nejlepšího modelu
Pokud chcete vybrat nejlepší model z vašich iterací, použijte ```get_output``` funkci k vrácení nejlepšího běhu a namontovaného modelu. Následující kód načte nejlepší běh a namontovaný model pro všechny protokolované metriky nebo konkrétní iterace.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Přesnost modelu testu
1. K otestování přesnosti modelu použijte nejlepší model pro spuštění taxislužby tarif předpovědi na testovací datové sadě. ```predict```Funkce používá nejlepší model a předpovídá hodnoty `y` (množství tarifů) z datové sady ověření. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. Chyba root-střed_hodn-Square je často používaná míra rozdílů mezi ukázkovými hodnotami předpokládanými modelem a zjištěnými hodnotami. Vypočítáte chybu root-střed_hodn-čtverce výsledků porovnáním datového `y_test` rámce s hodnotami, které model předpovídá. 

   Funkce ```mean_squared_error``` přebírá dvě pole a vypočítá průměrnou kvadratickou chybu mezi nimi. Pak vezmete druhou odmocninu výsledku. Tato metrika udává zhruba, jak daleko se taxislužby tarif předpovědi ze skutečných hodnot tarifů.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   Chyba root-střed_hodn-Square je dobrým rozměrem, jak přesně model předpovídá odpověď. Z výsledků uvidíte, že model je poměrně dobrý pro předpověď taxislužby tarifů z funkcí datové sady, obvykle v $2,00.

1. Spuštěním následujícího kódu Vypočítejte chybu střední-absolutní-procento. Tato metrika vyjadřuje přesnost v procentech chyby. Provede to tak, že vypočítá absolutní rozdíl mezi každou předpovězenou a skutečnou hodnotou a potom sečte všechny rozdíly. Pak vyjadřuje, že součet jako procento z celkového počtu skutečných hodnot.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
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

1. Po přizpůsobení modelu lineární regrese teď musíte určit, jak dobře model odpovídá datům. Chcete-li to provést, vykreslíte skutečné hodnoty jízdného s předpokládaným výstupem. Kromě toho je třeba vypočítat míru na čtverci, abyste pochopili, jak se data zavřou na pevně danou regresní čáru.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Snímek obrazovky regresní parcely](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Z výsledků vidíte, že účty měr R-kvadrát pro 95 procent odchylky. Tato hodnota je také ověřena skutečným sezobrazovaným v porovnání s pozorovaným vykreslením. Větší variance, pro které jsou účty regresního modelu pro, blíž k datovým bodům připadá na pevně danou regresní čáru.  

## <a name="register-the-model-to-azure-machine-learning"></a>Zaregistrujte model pro Azure Machine Learning
Po ověření nejlepšího modelu ho můžete zaregistrovat pro Azure Machine Learning. Pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Zobrazit výsledky v Azure Machine Learning
K výsledkům iterací můžete přistupovat také tak, že na experimentu v pracovním prostoru Azure Machine Learning. Tady můžete získat další podrobnosti o stavu spuštění, pokusu o modelech a dalších metrikách modelu. 

![Snímek obrazovky pracovního prostoru Azure Machine Learning](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Další kroky
- [Azure Synapse Analytics](../index.yml)
- [Kurz: Vytvoření aplikace Machine Learning pomocí Apache Spark MLlib a Azure synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)