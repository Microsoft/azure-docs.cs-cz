---
title: 'Recept: prediktivní údržba s Cognitive Services pro velké objemy dat'
titleSuffix: Azure Cognitive Services
description: Tento rychlý Start ukazuje, jak provést distribuovanou detekci anomálií s Cognitive Services pro velké objemy dat.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: aa650fe9cb7df64a6a7a948224be225ecfad9057
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324610"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Recept: prediktivní údržba s Cognitive Services pro velké objemy dat

Tento recept ukazuje, jak můžete používat Azure synapse Analytics a Cognitive Services v Apache Spark pro prediktivní údržbu zařízení IoT. Budeme postupovat spolu s ukázkou [odkazů CosmosDB a synapse](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) . Aby se v tomto receptu zachovaly věci jednoduché, přečtěte si data přímo ze souboru CSV, ale nebudete moct data streamovat prostřednictvím odkazu CosmosDB a synapse. Důrazně doporučujeme, abyste si prohledali ukázku odkazu na synapse.

## <a name="hypothetical-scenario"></a>Hypotetický scénář

Hypotetickým scénářem je napájecí závod, ve kterém zařízení IoT monitorují [parní turbíny](https://en.wikipedia.org/wiki/Steam_turbine). Kolekce IoTSignals obsahuje data o revoluce za minutu (ot./min.) a Megawatts (MW) pro každou turbínu. Signály z parních turbín se analyzují a zjišťují se neobvyklé signály.

V datech v náhodných frekvencích by se mohlo jednat o odlehlé hodnoty. V těchto situacích budou hodnoty ot./min. až do vypínání a pro ochranu okruhu až MW. Nápadem je zobrazit data ve stejnou dobu, ale s různými signály.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Pracovní prostor Azure synapse](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) nakonfigurovaný s [fondem Apache Spark bez serveru](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool)

## <a name="setup"></a>Nastavení

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro překladatele pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli). Můžete také:

- Zobrazení existujícího prostředku v  [Azure Portal](https://portal.azure.com/).

Poznamenejte si koncový bod a klíč tohoto prostředku, budete ho potřebovat v této příručce.

## <a name="enter-your-service-keys"></a>Zadejte klíče služby

Pojďme začít přidáním klíče a umístění.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Čtení dat do datového rámce

Nyní načtěte soubor IoTSignals do datového rámce. Otevřete nový Poznámkový blok v pracovním prostoru synapse a vytvořte ze souboru datový rámec.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Spuštění detekce anomálií pomocí Cognitive Services ve Sparku

Cílem je najít instance, u kterých signály ze zařízení IoT nacházely z neobvyklé hodnot, aby bylo možné zjistit, kdy se něco nepovede, a udělat prediktivní údržbu. K tomu je potřeba použít detektor anomálií ve Sparku:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Pojďme se podívat na data:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Tato buňka by měla vracet výsledek, který vypadá takto:

| časové razítko           |   hodnota | deviceId   | Anomálie   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | vývoj – 7      | Nepravda       |
| 2020-05-01 18:33:52 |    2976 | vývoj – 7      | Nepravda       |
| 2020-05-01 18:33:53 |    2714 | vývoj – 7      | Nepravda       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Vizualizace anomálií pro jedno ze zařízení

IoTSignals.csv má signály z více zařízení IoT. Zaměřte se na konkrétní zařízení a vizualizujte neobvyklé výstupy ze zařízení.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Teď, když jsme vytvořili datový rámec, který představuje anomálie konkrétního zařízení, můžeme tyto anomálie vizualizovat:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

V případě úspěchu bude výstup vypadat takto:

![Vykreslení detektoru anomálií](../media/anomaly-output.png)

## <a name="next-steps"></a>Další kroky

Naučte se provádět prediktivní údržbu ve velkém měřítku s využitím Azure Cognitive Services, Azure synapse Analytics a Azure CosmosDB. Další informace najdete v kompletní ukázce na [GitHubu](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).
