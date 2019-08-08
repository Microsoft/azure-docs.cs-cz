---
title: 'Kurz: Obohacení automatizovaného modelu machine learningu'
titleSuffix: Azure Open Datasets
description: Naučte se využívat pohodlí otevřených datových sad Azure spolu s výkonem služby Azure Machine Learning k vytvoření regresního modelu, který předpovídá ceny za taxislužby jízdné.
services: open-datasets
ms.service: open-datasets
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 05/02/2019
ms.openlocfilehash: 6f72daa4a601df0e3592910645c2f9b35ab64431
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845811"
---
# <a name="tutorial-build-a-regression-model-with-automated-machine-learning-and-open-datasets"></a>Kurz: Sestavujte regresní model pomocí automatizovaného strojového učení a otevřených datových sad.

V tomto kurzu využijete pohodlí otevřených datových sad Azure spolu s výkonem služby Azure Machine Learning k vytvoření regresního modelu, který předpovídá ceny za taxislužby jízdné. Snadno si stáhněte veřejně dostupná data taxislužby, svátek a počasí a nakonfigurujeme automatizované experimenty ve strojovém učení pomocí Azure Machine Learning služby. Tento proces přijímá školicí data a nastavení konfigurace a automaticky iterovat kombinace různých metod normalizace/normalizace funkcí, modelů a nastavení parametrů pro dosažení nejlepšího modelu.

V tomto kurzu se seznámíte s následujícími úlohami:

- Konfigurovat pracovní prostor služby Azure Machine Learning service
- Nastavení místního prostředí Pythonu
- Přístup, transformace a spojení dat pomocí otevřených datových sad Azure
- Výuka automatizovaného modelu Machine Learning
- Vypočítat přesnost modelu

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje splnění následujících požadavků.

* Pracovní prostor služby Azure Machine Learning
* Prostředí Python 3,6

### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Podle [pokynů](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) vytvořte pracovní prostor prostřednictvím Azure Portal, pokud ho ještě nemáte. Po vytvoření si poznamenejte název svého pracovního prostoru, název skupiny prostředků a ID předplatného.

### <a name="create-a-python-environment"></a>Vytvoření prostředí Pythonu

V tomto příkladu se používá prostředí Anaconda s poznámkovým blokem Jupyter, ale tento kód můžete spustit v jakémkoli prostředí 3.6. x a v libovolném textovém editoru nebo integrovaném vývojovém prostředí (IDE). Pomocí následujících kroků můžete vytvořit nové vývojové prostředí.

1. Pokud ho ještě nemáte, [Stáhněte](https://www.anaconda.com/distribution/) a nainstalujte Anaconda a vyberte **verzi Python 3,7**.
1. Otevřete příkazový řádek Anaconda a vytvořte nové prostředí. Vytvoření prostředí během stahování komponent a balíčků bude trvat několik minut.
    ```
    conda create -n tutorialenv python=3.6.5
    ```
1. Aktivujte prostředí.
    ```
    conda activate tutorialenv
    ```
1. Povolte jádra IPython pro konkrétní prostředí.
    ```
    conda install notebook ipykernel
    ```
1. Vytvořte jádro.
    ```
    ipython kernel install --user
    ```
1. Nainstalujte balíčky, které pro tento kurz potřebujete. Tyto balíčky jsou velké a instalace bude trvat 5-10 minut.
    ```
    pip install azureml-sdk[automl] azureml-opendatasets
    ```
1. Spusťte jádro poznámkového bloku z vašeho prostředí.
    ```
    jupyter notebook
    ```

Po dokončení těchto kroků naklonujte [úložiště poznámkového bloku Open DataSets](https://github.com/Azure/OpenDatasetsNotebooks) a otevřete Poznámkový blok s výukou **/taxi-automl/01-tutorial-opendatasets-automl. ipynb** a spusťte ho.

## <a name="download-and-prepare-data"></a>Stažení a Příprava dat

Importujte potřebné balíčky. Balíček Open DataSets obsahuje třídu reprezentující jednotlivé zdroje dat (`NycTlcGreen` například), aby bylo možné před stažením snadno filtrovat parametry data.


```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Začněte tím, že vytvoříte datový rámec, který bude uchovávat data taxislužby. Při práci v prostředí, které není Spark, mohou otevřené datové sady stahovat pouze jeden měsíc dat s určitými třídami, aby se předešlo `MemoryError` velkým datovým sadám. Pokud si chcete stáhnout rok taxislužby dat, iterativní načtení po jednom měsíci a před jeho připojením k `green_taxi_df` náhodnému vzorkování 2000 záznamů z každého měsíce, aby se zabránilo bloatingí datového rámce. Pak zobrazte náhled dat.

>[!NOTE]
> Otevřené datové sady obsahují třídy zrcadlení pro práci v prostředích Spark, kde velikost dat a paměť nejsou obavy.

```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>extra</th>
      <th>mtaTax</th>
      <th>improvementSurcharge</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Žádné</td>
      <td>Žádný</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>...</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>2.0</td>
      <td>11,5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>2.0</td>
      <td>12.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>1.0</td>
      <td>12.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>...</td>
      <td>2.0</td>
      <td>4.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Žádné</td>
      <td>Žádný</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>2.0</td>
      <td>10.5</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73.927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.5</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Žádné</td>
      <td>Žádný</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>...</td>
      <td>2.0</td>
      <td>6.5</td>
      <td>0.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>1.0</td>
      <td>13.0</td>
      <td>0,5</td>
      <td>0,5</td>
      <td>0,3</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10 řádků × 23 sloupců</p>
</div>



Po načtení počátečních dat definujte funkci pro vytvoření různých funkcí založených na čase z pole Datum vyzvednutí. Tím se vytvoří nová pole pro číslo měsíce, den v měsíci, den v týdnu a hodina dne a v modelu bude umožněno, aby model platil jako sezónnost založený na čase. Funkce také přidá statickou funkci pro kód země, aby se připojila k prázdninovým datům. Použijte funkci na datovém snímku pro iterativní `build_time_features()` použití funkce na každý řádek taxislužby dat. `apply()`


```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour
    country_code = "US"

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day, country_code))


green_taxi_df[["month_num", "day_of_month", "day_of_week", "hour_of_day", "country_code"]
              ] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>...</th>
      <th>tipAmount</th>
      <th>tollsAmount</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20 17:38:28</td>
      <td>2016-01-20 17:46:33</td>
      <td>1</td>
      <td>0,98</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>8.8</td>
      <td>1.0</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01 21:53:28</td>
      <td>2016-01-02 00:00:00</td>
      <td>1</td>
      <td>3,08</td>
      <td>Žádné</td>
      <td>Žádný</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01 00:50:23</td>
      <td>2016-01-01 01:05:37</td>
      <td>1</td>
      <td>2.44</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04 17:50:03</td>
      <td>2016-01-04 18:03:43</td>
      <td>1</td>
      <td>2,87</td>
      <td>Žádné</td>
      <td>Žádný</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>13,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13 08:48:20</td>
      <td>2016-01-13 08:52:16</td>
      <td>1</td>
      <td>0.50</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>5.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1049431</th>
      <td>2</td>
      <td>2016-01-29 17:16:18</td>
      <td>2016-01-29 17:27:52</td>
      <td>1</td>
      <td>2.25</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,830894</td>
      <td>40,759434</td>
      <td>-73,842422</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>12,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>29</td>
      <td>4</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>646563</th>
      <td>2</td>
      <td>2016-01-14 00:45:30</td>
      <td>2016-01-14 00:54:16</td>
      <td>1</td>
      <td>1.93</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73.927109</td>
      <td>40,762848</td>
      <td>-73,909302</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>9,8</td>
      <td>1.0</td>
      <td>1</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>438204</th>
      <td>1</td>
      <td>2016-01-09 14:25:02</td>
      <td>2016-01-09 14:32:48</td>
      <td>2</td>
      <td>0,80</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73,881195</td>
      <td>40,741779</td>
      <td>-73,872086</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>7.3</td>
      <td>1.0</td>
      <td>1</td>
      <td>9</td>
      <td>5</td>
      <td>14</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>303784</th>
      <td>2</td>
      <td>2016-01-25 18:13:47</td>
      <td>2016-01-25 18:23:50</td>
      <td>1</td>
      <td>1,04</td>
      <td>Žádné</td>
      <td>Žádné</td>
      <td>-73,954376</td>
      <td>40,805729</td>
      <td>-73.939117</td>
      <td>...</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>11,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>25</td>
      <td>0</td>
      <td>18</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>269105</th>
      <td>2</td>
      <td>2016-01-24 20:46:50</td>
      <td>2016-01-24 21:04:03</td>
      <td>6</td>
      <td>2,82</td>
      <td>Žádný</td>
      <td>Žádné</td>
      <td>-73,845200</td>
      <td>40,722134</td>
      <td>-73,810638</td>
      <td>...</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>NaN</td>
      <td>16,3</td>
      <td>1.0</td>
      <td>1</td>
      <td>24</td>
      <td>6</td>
      <td>20</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
<p>10 řádků × 28 sloupců</p>
</div>

Odeberte některé sloupce, které nebudete potřebovat pro modelování, nebo pro vytváření dalších funkcí. Přejmenujte pole čas pro dobu vyzvednutí a dále převeďte dobu na půlnoc `pandas.Series.dt.normalize`pomocí. To provedete na všechny časové funkce, aby komponenta DateTime mohla být později použita jako klíč při spojování datových sad na denní úrovni členitosti.

```python
columns_to_remove = ["lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                     ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df = green_taxi_df.rename(
    columns={"lpepPickupDatetime": "datetime"})
green_taxi_df["datetime"] = green_taxi_df["datetime"].dt.normalize()
green_taxi_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>117695</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1221794</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1165078</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>1345223</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
    </tr>
    <tr>
      <th>608125</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-holiday-data"></a>Obohacení o data svátků

Teď, když máte taxislužby data stažená a přibližně připravená, přidejte data o svátcích jako další funkce. Funkce specifické pro svátky budou pomáhat s přesností modelu, protože hlavní svátky jsou časy, ve kterých se výrazně zvyšuje poptávka taxislužby a nabídka je omezená. Datová sada svátků je poměrně malá, takže načte úplnou sadu pomocí `PublicHolidays` konstruktoru třídy bez parametrů pro filtrování. Zobrazte náhled dat a ověřte formát.

```python
from azureml.opendatasets import PublicHolidays
# call default constructor to download full dataset
holidays_df = PublicHolidays().to_pandas_dataframe()
holidays_df.head(5)
```

    ActivityStarted, to_pandas_dataframe
    Looking for parquet files...
    Reading them into Pandas dataframe...
    Reading Processed/part-00000-tid-1353805596865908763-9ee4e95b-0d55-4292-addd-a0e19d7c32cb-3559-c000.snappy.parquet under container holidaydatacontainer
    Done.
    ActivityCompleted: Activity=to_pandas_dataframe, HowEnded=Success, Duration=1799.89 [ms]

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>countryOrRegion</th>
      <th>holidayName</th>
      <th>isPaidTimeOff</th>
      <th>countryRegionCode</th>
      <th>normalizeHolidayName</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>40688</th>
      <td>Albánie</td>
      <td>Den nového roku</td>
      <td>Žádné</td>
      <td>AL</td>
      <td>Den nového roku</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40689</th>
      <td>Alžírsko</td>
      <td>Den nového roku</td>
      <td>Žádný</td>
      <td>DZ</td>
      <td>Den nového roku</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40690</th>
      <td>Andorra</td>
      <td>Den nového roku</td>
      <td>Žádné</td>
      <td>AD</td>
      <td>Den nového roku</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40691</th>
      <td>Angola</td>
      <td>Den nového roku</td>
      <td>Žádné</td>
      <td>AO</td>
      <td>Den nového roku</td>
      <td>2008-01-01</td>
    </tr>
    <tr>
      <th>40692</th>
      <td>Argentina</td>
      <td>Den nového roku</td>
      <td>Žádné</td>
      <td>AR</td>
      <td>Den nového roku</td>
      <td>2008-01-01</td>
    </tr>
  </tbody>
</table>
</div>



Přejmenujte `date` sloupce atak,abyodpovídalynázvůmpříslušnýchpolíztaxislužbydat,atakéNormalizujtečastak,abysemohlpoužítjakoklíč.`countryRegionCode` Dále připojte data o svátcích k datům taxislužby a provedete to tak, že pomocí funkce PANDAS `merge()` naspojíte levé spojení. Tato akce zachová všechny `green_taxi_df`záznamy z, ale přidají se do dat o svátcích, `datetime` kde `country_code`existuje pro odpovídající a, což je `"US"`v tomto případě vždycky. Zobrazte náhled dat a ověřte, zda byly správně sloučeny.

```python
holidays_df = holidays_df.rename(
    columns={"countryRegionCode": "country_code", "date": "datetime"})
holidays_df["datetime"] = holidays_df["datetime"].dt.normalize()
holidays_df.pop("countryOrRegion")
holidays_df.pop("holidayName")

taxi_holidays_df = pd.merge(green_taxi_df, holidays_df, how="left", on=[
                            "datetime", "country_code"])
taxi_holidays_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>datetime</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>country_code</th>
      <th>isPaidTimeOff</th>
      <th>normalizeHolidayName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2016-01-20</td>
      <td>1</td>
      <td>0,98</td>
      <td>-73,921715</td>
      <td>40,766682</td>
      <td>-73,916908</td>
      <td>40,761257</td>
      <td>8.8</td>
      <td>1</td>
      <td>20</td>
      <td>2</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>3,08</td>
      <td>-73,979973</td>
      <td>40,677071</td>
      <td>-73,934349</td>
      <td>40,671654</td>
      <td>12,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>21</td>
      <td>USA</td>
      <td>Pravda</td>
      <td>Den nového roku</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2016-01-01</td>
      <td>1</td>
      <td>2.44</td>
      <td>-73,863045</td>
      <td>40,882923</td>
      <td>-73,839836</td>
      <td>40,868336</td>
      <td>13,8</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>USA</td>
      <td>Pravda</td>
      <td>Den nového roku</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2016-01-04</td>
      <td>1</td>
      <td>2,87</td>
      <td>-73,977730</td>
      <td>40,684647</td>
      <td>-73,931259</td>
      <td>40,694248</td>
      <td>13,8</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>17</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2016-01-13</td>
      <td>1</td>
      <td>0.50</td>
      <td>-73,942589</td>
      <td>40,841423</td>
      <td>-73,943672</td>
      <td>40,834396</td>
      <td>5.3</td>
      <td>1</td>
      <td>13</td>
      <td>2</td>
      <td>8</td>
      <td>USA</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

### <a name="enrich-with-weather-data"></a>Obohacení daty o počasí

Teď k datům taxislužby a svátků připojíte data o počasí NOAA Surface. Použijte podobný přístup k načtení dat o počasí stažením jednoho měsíce v čase po iteraci. Kromě toho zadejte `cols` parametr s polem řetězců pro filtrování sloupců, které chcete stáhnout. Jedná se o velmi velkou datovou sadu, která obsahuje data z klimatické oblasti z celého světa, takže před tím, než každý měsíc připojíte, filtrujte pole lat/ `query()` Long na téměř NYC pomocí funkce na datovém snímku. Tím se zajistí, `weather_df` že nezíská příliš velkou velikost.

```python
from azureml.opendatasets import NoaaIsdWeather

weather_df = pd.DataFrame([])
start = datetime.strptime("1/1/2016", "%m/%d/%Y")
end = datetime.strptime("1/31/2016", "%m/%d/%Y")

for sample_month in range(12):
    tmp_df = NoaaIsdWeather(cols=["temperature", "precipTime", "precipDepth", "snowDepth"], start_date=start + relativedelta(months=sample_month), end_date=end + relativedelta(months=sample_month))\
        .to_pandas_dataframe()
    print("--weather downloaded--")

    # filter out coordinates not in NYC to conserve memory
    tmp_df = tmp_df.query("latitude>=40.53 and latitude<=40.88")
    tmp_df = tmp_df.query("longitude>=-74.09 and longitude<=-73.72")
    print("--filtered coordinates--")
    weather_df = weather_df.append(tmp_df)

weather_df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>wban</th>
      <th>precipTime</th>
      <th>snowDepth</th>
      <th>teplota</th>
      <th>Zeměpisná šířka</th>
      <th>precipDepth</th>
      <th>Zeměpisná délka</th>
      <th>datetime</th>
      <th>usaf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1754979</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>7.2</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 00:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754980</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 01:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754981</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.7</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 02:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754982</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>6.1</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 03:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754983</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754984</th>
      <td>94741</td>
      <td>24,0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>5.0</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754985</th>
      <td>94741</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>40,85</td>
      <td>NaN</td>
      <td>-74,061</td>
      <td>2016-01-01 04:59:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754986</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.6</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 05:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754987</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 06:51:00</td>
      <td>725025</td>
    </tr>
    <tr>
      <th>1754988</th>
      <td>94741</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>40,85</td>
      <td>0.0</td>
      <td>-74,061</td>
      <td>2016-01-01 07:51:00</td>
      <td>725025</td>
    </tr>
  </tbody>
</table>
</div>

Znovu zavolejte `pandas.Series.dt.normalize` `datetime` na pole v datech počasí, aby odpovídala časovému klíči v `taxi_holidays_df`. Odstraňte nepotřebné sloupce a vyfiltrujte záznamy, u kterých je `NaN`teplota.

Dále seskupte data o počasí, aby bylo možné denní agregované hodnoty počasí. Definujte Dict – `aggregations` k definování, jak agregovat každé pole na denní úrovni. Pro `snowDepth` `precipTime` `precipDepth` a `temperature` vybere střední hodnotu a pro a denní maximum. `groupby()` Použijte funkci spolu s agregacemi k seskupení dat. Zobrazte náhled dat a ujistěte se, že je jeden záznam denně.

```python
weather_df["datetime"] = weather_df["datetime"].dt.normalize()
weather_df.pop("usaf")
weather_df.pop("wban")
weather_df.pop("longitude")
weather_df.pop("latitude")

# filter out NaN
weather_df = weather_df.query("temperature==temperature")

# group by datetime
aggregations = {"snowDepth": "mean", "precipTime": "max",
                "temperature": "mean", "precipDepth": "max"}
weather_df_grouped = weather_df.groupby("datetime").agg(aggregations)
weather_df_grouped.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>teplota</th>
      <th>precipDepth</th>
    </tr>
    <tr>
      <th>datetime</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-01-01</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>5,197345</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-02</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>2,567857</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-03</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>3,846429</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-04</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>0,123894</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-05</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-7,206250</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-06</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>-0,896396</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-07</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>3,180645</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-08</th>
      <td>NaN</td>
      <td>1.0</td>
      <td>4,384091</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2016-01-09</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>6,710274</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2016-01-10</th>
      <td>NaN</td>
      <td>24,0</td>
      <td>10,943655</td>
      <td>254,0</td>
    </tr>
  </tbody>
</table>
</div>

> [!NOTE]
> Příklady v tomto kurzu sloučí data pomocí funkcí PANDAS a vlastních agregací, ale sada SDK Open DataSet obsahuje třídy navržené pro snadné sloučení a obohacení datových sad. Příklady příkladů kódu těchto vzorů návrhu naleznete v poznámkovém [bloku](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) .

### <a name="cleanse-data"></a>Vyčistit data

Slučte data o taxislužby a svátkech, která jste připravili s novými daty o počasí. Tentokrát potřebujete `datetime` jenom klíč a znovu provést připojení k datům. `describe()` Spuštěním funkce na novém dataframe zobrazíte souhrnnou statistiku pro každé pole.

```python
taxi_holidays_weather_df = pd.merge(
    taxi_holidays_df, weather_df_grouped, how="left", on=["datetime"])
taxi_holidays_weather_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>pickupLongitude</th>
      <th>pickupLatitude</th>
      <th>dropoffLongitude</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>teplota</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>1671,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
      <td>24000,000000</td>
    </tr>
    <tr>
      <th>střední hodnotu</th>
      <td>1,786583</td>
      <td>6,576208</td>
      <td>1,582588</td>
      <td>20,505491</td>
      <td>84,936413</td>
      <td>-36,232825</td>
      <td>21,723144</td>
      <td>7,863018</td>
      <td>6,500000</td>
      <td>15,113708</td>
      <td>3,240250</td>
      <td>13,664125</td>
      <td>11,764141</td>
      <td>13,258875</td>
      <td>13,903524</td>
      <td>1056,644458</td>
    </tr>
    <tr>
      <th>STD</th>
      <td>0,409728</td>
      <td>9,086857</td>
      <td>2,418177</td>
      <td>108,847821</td>
      <td>70,678506</td>
      <td>37,650276</td>
      <td>19,104384</td>
      <td>10,648766</td>
      <td>3,452124</td>
      <td>8,485155</td>
      <td>1,956895</td>
      <td>6,650676</td>
      <td>15,651884</td>
      <td>10,339720</td>
      <td>9,474396</td>
      <td>2815,592754</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1,000000</td>
      <td>-60,000000</td>
      <td>-1,000000</td>
      <td>-74,179482</td>
      <td>0.000000</td>
      <td>-74,190704</td>
      <td>0.000000</td>
      <td>-52,800000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>0,330000</td>
      <td>-73,946680</td>
      <td>40,717712</td>
      <td>-73,945429</td>
      <td>1,770000</td>
      <td>1,000000</td>
      <td>3,750000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>6,620773</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>4,000000</td>
      <td>0,830000</td>
      <td>1.500000</td>
      <td>40,814129</td>
      <td>0,500000</td>
      <td>21,495000</td>
      <td>2,000000</td>
      <td>6,500000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,428571</td>
      <td>6,000000</td>
      <td>13,090753</td>
      <td>10,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>1,870000</td>
      <td>89,000000</td>
      <td>129,000000</td>
      <td>1,000000</td>
      <td>40,746146</td>
      <td>11,300000</td>
      <td>9,250000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>12,722222</td>
      <td>24,000000</td>
      <td>22,944737</td>
      <td>132,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>460,000000</td>
      <td>51,950000</td>
      <td>265,000000</td>
      <td>265,000000</td>
      <td>6,000000</td>
      <td>58,600000</td>
      <td>498,000000</td>
      <td>12,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>31,303665</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

V souhrnných statistikách vidíte, že existuje několik polí, která mají mimo jiné pole nebo hodnoty, které budou snižovat přesnost modelu. Nejdřív vyfiltrujte pole lat/Long tak, aby byla v rámci stejných mezí, jako jste použili pro filtrování dat počasí. `tripDistance` Pole obsahuje některá chybná data, protože minimální hodnota je záporná. `passengerCount` Pole obsahuje také chybná data s maximální hodnotou 210 cestujících. Nakonec má `totalAmount` pole záporné hodnoty, které nesmyslují v kontextu našeho modelu.

Vyfiltrujte tyto anomálie pomocí funkcí dotazů a pak odeberte poslední sloupce, které nejsou potřebné pro školení.

```python
final_df = taxi_holidays_weather_df.query(
    "pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query(
    "pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>0 and tripDistance<75")
final_df = final_df.query("passengerCount>0 and passengerCount<100")
final_df = final_df.query("totalAmount>0")

columns_to_remove_for_training = ["datetime", "pickupLongitude",
                                  "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "country_code"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Zavolejte `describe()` znovu na data, abyste zajistili, že čištění bude pracovat podle očekávání. Teď máte připravenou a vyčištěnou sadu dat taxislužby, svátků a počasí, která se použijí pro školení modelů ve strojovém učení.

```python
final_df.describe()
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendorID</th>
      <th>passengerCount</th>
      <th>tripDistance</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
      <th>snowDepth</th>
      <th>precipTime</th>
      <th>teplota</th>
      <th>precipDepth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>1490,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
      <td>11765,000000</td>
    </tr>
    <tr>
      <th>střední hodnotu</th>
      <td>1,786910</td>
      <td>1,343476</td>
      <td>2,848488</td>
      <td>14,689039</td>
      <td>3,499788</td>
      <td>14,948916</td>
      <td>3,234254</td>
      <td>13,647344</td>
      <td>12,508581</td>
      <td>11,855929</td>
      <td>10,301433</td>
      <td>208,432384</td>
    </tr>
    <tr>
      <th>STD</th>
      <td>0,409508</td>
      <td>1,001232</td>
      <td>2,895960</td>
      <td>10,289832</td>
      <td>1,707865</td>
      <td>8,442438</td>
      <td>1,958477</td>
      <td>6,640280</td>
      <td>16,203195</td>
      <td>10,125701</td>
      <td>8,553512</td>
      <td>1284,892832</td>
    </tr>
    <tr>
      <th>min.</th>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0,010000</td>
      <td>3,300000</td>
      <td>1,000000</td>
      <td>1,000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>-13,379464</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,070000</td>
      <td>8,160000</td>
      <td>2,000000</td>
      <td>8,000000</td>
      <td>2,000000</td>
      <td>9,000000</td>
      <td>3,000000</td>
      <td>1,000000</td>
      <td>3,504580</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>1,900000</td>
      <td>11,300000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>3,000000</td>
      <td>15,000000</td>
      <td>4,250000</td>
      <td>6,000000</td>
      <td>10,168182</td>
      <td>3,000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2,000000</td>
      <td>1,000000</td>
      <td>3,550000</td>
      <td>17,800000</td>
      <td>5,000000</td>
      <td>22,000000</td>
      <td>5,000000</td>
      <td>19,000000</td>
      <td>15,647059</td>
      <td>24,000000</td>
      <td>16,966923</td>
      <td>41,000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,000000</td>
      <td>6,000000</td>
      <td>51,950000</td>
      <td>150,300000</td>
      <td>6,000000</td>
      <td>30,000000</td>
      <td>6,000000</td>
      <td>23,000000</td>
      <td>67,090909</td>
      <td>24,000000</td>
      <td>26,524107</td>
      <td>9999,000000</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="train-a-model"></a>Učení modelu

Nyní použijete připravená data k výuce automatizovaného modelu strojového učení. Začněte rozdělením `final_df` na funkce (hodnoty X) a popisky (hodnota y), které jsou pro tento model taxislužby náklady.

```python
y_df = final_df.pop("totalAmount")
x_df = final_df
```

Nyní rozdělíte data do školicích a testovacích sad pomocí `train_test_split()` funkce `scikit-learn` v knihovně. `test_size` Parametr určuje procento dat pro přidělení k testování. `random_state` Parametr nastaví počáteční hodnotu pro generátor náhodných čísel, aby vaše výukové testování bylo deterministické.


```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=222)
```

### <a name="load-workspace-and-configure-experiment"></a>Načíst pracovní prostor a nakonfigurovat experiment

Pomocí `get()` funkce s vaším předplatným a informacemi o pracovním prostoru načtěte svůj pracovní prostor služby Azure Machine Learning. Vytvořte experiment v pracovním prostoru, abyste mohli ukládat a monitorovat spuštění vašeho modelu.


```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment

workspace = Workspace.get(subscription_id="<your-subscription-id>",
                          name="<your-workspace-name>", resource_group="<your-resource-group>")
experiment = Experiment(workspace, "opendatasets-ml")
```

Vytvořte objekt konfigurace pro experiment pomocí `AutoMLConfig` třídy. Budete připojeni k datům školení a také zadáte nastavení a parametry, které řídí proces školení. Parametry mají následující účely:

* `task`: typ experimentu, který se má spustit.
* `X`: školicí funkce.
* `y`: školicí popisky.
* `iterations`: počet iterací, které se mají spustit. Každá iterace pokusy o kombinace různých metod normalizace/standardizace a různé modely s použitím více nastavení parametrů.
* `primary_metric`: primární metrika, která se má optimalizovat během školení modelu. Model nejlépe přizpůsoben se vybere na základě této metriky.
* `preprocess`: Určuje, zda může experiment předzpracovat vstupní data (zpracování chybějících dat, převod textu na číselnou atd.)
* `n_cross_validations`: Počet rozdělení křížového ověření, které se mají provést, pokud nejsou zadaná ověřovací data.


```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task="regression",
                             X=X_train.values,
                             y=y_train.values.flatten(),
                             iterations=20,
                             primary_metric="spearman_correlation",
                             preprocess=True,
                             n_cross_validations=5
                             )
```

### <a name="submit-experiment"></a>Odeslání experimentu

Odešlete experiment pro školení. Po odeslání experimentu se proces prochází pomocí různých algoritmů strojového učení a nastavení vlastních parametrů, které dodržuje vaše definovaná omezení. Vybírá model nejlépe vyhovující optimalizacim definované metriky přesnosti. Předejte `automl_config` objekt do experimentu. Nastavte výstup na `True` , aby se zobrazil průběh během experimentu.

Po odeslání experimentu uvidíte živý výstup pro školicí proces. Pro každou iteraci vidíte typ modelu a normalizaci a standardizaci funkcí, dobu trvání běhu a přesnost školení. Pole `BEST` sleduje nejlepší průběžné školení na základě typu metriky.

```python
training_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_5c35f2a7-e479-4e7f-a131-ed4cb51e29d1
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: DatasetFeaturization. Featurizing the dataset.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler RandomForest                      0:00:07       0.9081    0.9081
             1   StandardScalerWrapper DecisionTree             0:00:05       0.9121    0.9121
             2   StandardScalerWrapper LightGBM                 0:00:04       0.9318    0.9318
             3   StandardScalerWrapper LightGBM                 0:00:07       0.9286    0.9318
             4   MaxAbsScaler LightGBM                          0:00:05       0.9246    0.9318
             5   MaxAbsScaler LightGBM                          0:00:05       0.9199    0.9318
             6   MaxAbsScaler RandomForest                      0:00:07       0.9327    0.9327
             7   StandardScalerWrapper ElasticNet               0:00:04       0.9371    0.9371
             8   MaxAbsScaler LightGBM                          0:00:05       0.9327    0.9371
             9   MaxAbsScaler SGD                               0:00:04       0.9077    0.9371
            10   MaxAbsScaler LightGBM                          0:00:04       0.9340    0.9371
            11   StandardScalerWrapper LightGBM                 0:00:04       0.8301    0.9371
            12   MaxAbsScaler DecisionTree                      0:00:05       0.9214    0.9371
            13   StandardScalerWrapper DecisionTree             0:00:04       0.9201    0.9371
            14   MaxAbsScaler DecisionTree                      0:00:05       0.9179    0.9371
            15   MaxAbsScaler ExtremeRandomTrees                0:00:05       0.9052    0.9371
            16   StandardScalerWrapper DecisionTree             0:00:04       0.9282    0.9371
            17   StandardScalerWrapper ElasticNet               0:00:04       0.9319    0.9371
            18   VotingEnsemble                                 0:00:16       0.9380    0.9380
            19   StackEnsemble                                  0:00:17       0.9376    0.9380

### <a name="retrieve-the-fitted-model"></a>Načtení namontovaného modelu

Na konci všech cvičení školení vytvoří proces automatizovaného strojového učení algoritmus kompletu ze všech jednotlivých spuštění, a to buď pomocí překrývání nebo skládání. Načte namontovaný komplet do proměnné `fitted_model`a nejlepšího jednotlivého spuštění do proměnné. `best_run`

```python
best_run, fitted_model = training_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-model-accuracy"></a>Přesnost modelu testu

Pomocí namontovaného modelu kompletu můžete spustit předpovědi na testovací sadě pro předpověď taxislužby tarifů. Funkce `predict()` používá namontovaný model a předpovídá hodnoty nákladů y a taxislužby jízdné `X_test` za datovou sadu.


```python
y_predict = fitted_model.predict(X_test.values)
```

Vypočítat hlavní střední hodnotu chyby na čtverci výsledků. Použijte datový rámec a převeďte jej na seznam `y_actual` , aby bylo možné porovnat hodnoty předpokládaných hodnot. `y_test` Funkce `mean_squared_error` přebírá dvě pole hodnot a vypočítá průměrnou kvadratickou chybu mezi nimi. Výsledkem druhé odmocniny výsledku je chyba ve stejných jednotkách jako proměnná y, náklady. Uvádí zhruba, jak daleko se taxislužby tarif předpovědi ze skutečné tarify, a přitom silně vážení velkých chyb.


```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```




    4.178568987067901



Spusťte následující kód, který vypočítá průměrnou absolutní procentuální chybu (mape) pomocí úplného `y_actual` a `y_predict` datových sad. Tato metrika vypočítá absolutní rozdíl mezi každou předpovězenou a skutečnou hodnotou a sečte všechny rozdíly. Pak vyjadřuje, že součet je procentuální podíl celkového počtu skutečných hodnot.


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

    Model MAPE:
    0.14923619644924357

    Model Accuracy:
    0.8507638035507564

Vzhledem k tom, že jste použili poměrně malý vzorek dat vzhledem k plné datové sadě (n = 11748), přesnost modelu je poměrně vysoká v 85%, s $4,00 RMSE při předpověď ceny taxislužby tarifů při. Jako potenciální další krok pro zlepšení přesnosti se vraťte do druhé buňky tohoto poznámkového bloku a zvětšete velikost vzorku z 2 000 záznamů za měsíc a znovu spusťte celý experiment, abyste mohli model znovu vytvořit s více daty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, takže se vám neúčtují žádné poplatky.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

* Další příklady kódu najdete v poznámkových [blocích](https://github.com/Azure/OpenDatasetsNotebooks) Open DataSets pro Azure.
* Pokud [Chcete](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train) získat další informace o automatizovaném strojovém učení ve službě Azure Machine Learning, postupujte podle pokynů.
