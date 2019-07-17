---
title: 'Kurz regresního modelu: Příprava dat'
titleSuffix: Azure Machine Learning service
description: V první části tohoto kurzu se naučíte, jak připravit data v Pythonu pro regresní modelování pomocí sady Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: MayMSFT
ms.author: sihhu
ms.reviewer: trbye
ms.date: 07/16/2019
ms.custom: seodec18
ms.openlocfilehash: 55bece47ad2a9965e5137ad720631d9b5f5add48
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297885"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Kurz: Příprava dat pro regresní modelování

V tomto kurzu se naučíte, jak připravit data pro regresní modelování pomocí balíčku pro [přípravu dat](https://aka.ms/data-prep-sdk) ze [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). K filtrování a kombinování dvou různých datových sad taxislužby NYC můžete použít různé transformace.

Tento kurz je **první částí z dvoudílné série kurzů**. Po dokončení série kurzů můžete odhadnout náklady na taxislužbyou cestu školením modelu pro datové funkce. Mezi tyto funkce patří den a čas vyzvednutí, počet cestujících a umístění vyzvednutí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte prostředí Pythonu a importujte balíčky.
> * Načtěte dvě datové sady s různými názvy polí.
> * Vyčistit data a odebrat anomálie.
> * Umožňuje transformovat data pomocí inteligentních transformací k vytváření nových funkcí.
> * Uložte objekt Dataflow pro použití v regresním modelu.

## <a name="prerequisites"></a>Požadavky

Přejděte k [Nastavení vývojového prostředí](#start) pro čtení v rámci kroků poznámkového bloku nebo použijte následující pokyny k získání poznámkového bloku a jeho spuštění na Azure Notebooks nebo na vlastním serveru poznámkového bloku. Pokud chcete spustit Poznámkový blok, budete potřebovat:

* Server Python 3,6 Poznámkový blok s následujícím nainstalovaným:
    * `azureml-dataprep` Balíček z Azure Machine Learning SDK
* Poznámkový blok kurzu

* Použití [serveru cloudového poznámkového bloku v pracovním prostoru](#azure) 
* Použití [vlastního serveru poznámkového bloku](#server)

### <a name="azure"></a>Použití serveru cloudového poznámkového bloku v pracovním prostoru

Začněte s vlastním cloudovým notebookovým serverem. Sada SDK Azure Machine Learning pro Python je už po vytvoření tohoto cloudového prostředku nainstalovaná a nakonfigurovaná.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Po spuštění webové stránky poznámkového bloku spusťte Poznámkový blok **kurzy/Regression-part1-data-PREP. ipynb** .

### <a name="server"></a>Použití vlastního serveru Jupyter notebook

Pomocí těchto kroků můžete vytvořit místní aplikace Jupyter Notebook server ve vašem počítači.  Po dokončení kroků spusťte Poznámkový blok **kurzy/Regression-part1-data-PREP. ipynb** .

1. Dokončete kroky instalace v [Azure Machine Learning rychlý Start Pythonu](setup-create-workspace.md#sdk) pro vytvoření prostředí Miniconda a instalaci sady SDK.  Klidně přeskočte oddíl **vytvořit pracovní prostor** , pokud chcete, ale budete ho potřebovat pro [část 2](tutorial-auto-train-models.md) této série kurzů.
1. `azureml-dataprep` Balíček je automaticky nainstalován při instalaci sady SDK.
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Nastavení vývojového prostředí

Veškeré nastavení pro vaši vývojovou práci se dá provést v poznámkovém bloku Pythonu. Instalační program zahrnuje následující akce:

* Instalace sady SDK
* Import balíčků Pythonu

### <a name="install-and-import-packages"></a>Instalace a import balíčků

K instalaci nezbytných balíčků použijte následující, pokud je ještě nemáte.

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importujte balíček.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Ujistěte se, že jste nainstalovali nejnovější verzi balíčku AzureML. dataprep. Tento kurz nebude fungovat s číslem verze nižším než 1.1.0.

## <a name="load-data"></a>Načtení dat

Stažení dvou různých NYC datových sad taxislužby do objektů Dataflow. Datové sady mají mírně odlišná pole. `auto_read_file()` Metoda automaticky rozpoznává vstupní typ souboru.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

> [!Note]
> Adresa URL v tomto stejném příkladu není úplná adresa URL. Místo toho odkazuje na ukázkovou složku v objektu BLOB. Úplná adresa URL k částem dat je https://dprepdata.blob.core.windows.net/demo/green-small/green_tripdata_2013-08.csv

`Dataflow` Objekt je podobný jako datový rámec a představuje řadu laxně vytvářenách neproměnlivých operací s daty. Operace lze přidat voláním různých metod transformace a filtrování, které jsou k dispozici. Výsledkem přidání operace do `Dataflow` je vždy nový `Dataflow` objekt.

## <a name="cleanse-data"></a>Vyčistit data

Nyní naplníte některé proměnné pomocí klávesových zkratek, které se použijí na všechny toky dat. `drop_if_all_null` Proměnná slouží k odstranění záznamů, kde všechna pole mají hodnotu null. `useful_columns` Proměnná obsahuje pole popisů sloupců, která jsou udržována v každém toku dat.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Nejprve pracujete se zelenými taxislužby daty, abyste je získali na platný tvar, který lze kombinovat se žlutými taxislužby daty. Zavolejte funkce `replace_na()`, `drop_nulls()` a`keep_columns()` pomocí proměnných transformace zástupce, které jste vytvořili. Kromě toho přejmenujte všechny sloupce v rámci datového rámce tak, aby odpovídaly `useful_columns` názvům v proměnné.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
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
      <th>Dodavatele</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>cestujících</th>
      <th>vzdálenost</th>
      <th>náklad</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>Ne</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21,25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>Ne</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>Ne</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>Ne</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45.</td>
      <td>2013-08-01 12:03:52</td>
      <td>Ne</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Spusťte stejné kroky transformace na žlutě taxislužby data. Tyto funkce zajistí, že ze sady dat se odeberou data s hodnotou null, která vám pomůžou zvýšit přesnost modelů strojového učení.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Voláním `append_rows()` funkce na zeleně taxislužby data přidejte žlutá data taxislužby. Vytvoří se nový kombinovaný datový rámec.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Převod typů a filtr

Prozkoumejte sbírat míčky a odkládací souřadnice souhrnné statistiky chcete zobrazit, jak se data distribuovat. Nejdřív definujte `TypeConverter` objekt, který změní pole Zeměpisná šířka a délka na typ Decimal. Dále zavolejte `keep_columns()` funkci, aby se omezil výstup jenom na pole Zeměpisná šířka a délka, a pak `get_profile()` zavolejte funkci. Tato volání funkce vytvoří zhuštěné zobrazení toku dat, aby bylo možné pouze zobrazit pole lat/Long, což usnadňuje vyhodnocování chybějících nebo mimo rozsah souřadnic.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Minimum</th>
      <th>Maximum</th>
      <th>Počet</th>
      <th>Chybí počet</th>
      <th>Nechybí počet</th>
      <th>Procento chybějících</th>
      <th>Počet chyb</th>
      <th>Prázdný počet</th>
      <th>0,1 % Quantile</th>
      <th>1 % Quantile</th>
      <th>5 % Quantile</th>
      <th>25 % Quantile</th>
      <th>50 % Quantile</th>
      <th>75 % Quantile</th>
      <th>95 % Quantile</th>
      <th>99 % Quantile</th>
      <th>99,9 % Quantile</th>
      <th>Směrodatná odchylka</th>
      <th>střední hodnotu</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



Z výstupu souhrnné statistiky vidíte chybějící souřadnice a souřadnice, které nejsou v New York City (to je určeno z subjektivní analýzy). Odfiltrujte souřadnice pro umístění, která jsou mimo hranici města. Řetězte příkazy filtru sloupců v rámci `filter()` funkce a definujte minimální a maximální hranice pro každé pole. Poté zavolejte `get_profile()` funkci znovu a ověřte transformaci.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Minimum</th>
      <th>Maximum</th>
      <th>Počet</th>
      <th>Chybí počet</th>
      <th>Nechybí počet</th>
      <th>Procento chybějících</th>
      <th>Počet chyb</th>
      <th>Prázdný počet</th>
      <th>0,1 % Quantile</th>
      <th>1 % Quantile</th>
      <th>5 % Quantile</th>
      <th>25 % Quantile</th>
      <th>50 % Quantile</th>
      <th>75 % Quantile</th>
      <th>95 % Quantile</th>
      <th>99 % Quantile</th>
      <th>99,9 % Quantile</th>
      <th>Směrodatná odchylka</th>
      <th>střední hodnotu</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Rozdělení a přejmenování sloupců

Podívejte se na data profilu `store_forward` sloupce. Toto pole je logický příznak, který je `Y` v případě, že taxislužby neměl připojení k serveru po této cestě, a proto musel ukládat data na cestách do paměti a později je přepošle na server, pokud je připojený.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Minimum</th>
      <th>Maximum</th>
      <th>Počet</th>
      <th>Chybí počet</th>
      <th>Nechybí počet</th>
      <th>Procento chybějících</th>
      <th>Počet chyb</th>
      <th>Prázdný počet</th>
      <th>0,1 % Quantile</th>
      <th>1 % Quantile</th>
      <th>5 % Quantile</th>
      <th>25 % Quantile</th>
      <th>50 % Quantile</th>
      <th>75 % Quantile</th>
      <th>95 % Quantile</th>
      <th>99 % Quantile</th>
      <th>99,9 % Quantile</th>
      <th>Směrodatná odchylka</th>
      <th>střední hodnotu</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Ne</td>
      <td>Ano</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Všimněte si, že výstup profilu dat ve `store_forward` sloupci ukazuje, že data jsou nekonzistentní a chybí hodnoty nebo hodnoty null. Pomocí funkcí `fill_nulls()` a nahraďte tyto hodnoty řetězcem "N": `replace()`


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

`replace` Spusťte funkci`distance` v poli. Funkce přeformátuje hodnoty na dálku, které jsou nesprávně označeny `.00`jako a vyplní všechny hodnoty null s nulami. Převést `distance` pole na číselný formát. Tyto nesprávné datové body jsou nejspíš anomálie v systému sběru dat na taxislužby kabinách.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Rozdělte hodnoty data a času vyzvednutí a dropoff do příslušných sloupců data a času. `split_column_by_example()` Použijte funkci k provedení rozdělení. V tomto případě je vynechán volitelný `example` parametr `split_column_by_example()` funkce. Proto funkce automaticky určí, kde rozdělit na základě dat.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
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
      <th>Dodavatele</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>cestujících</th>
      <th>vzdálenost</th>
      <th>náklad</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>Ne</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 25:17:00</td>
      <td>2013-08-01</td>
      <td>25:17:00</td>
      <td>Ne</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>Ne</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>Ne</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>Ne</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Přejmenujte sloupce vygenerované `split_column_by_example()` funkcí tak, aby používaly smysluplné názvy.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Voláním `get_profile()` funkce zobrazíte úplnou souhrnnou statistiku po všech krocích čištění.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformace dat

Rozdělte Datum vyzvednutí a dropoff do dne v týdnu, dne v měsíci a hodnotách měsíců. K získání hodnoty dne v týdnu použijte `derive_column_by_example()` funkci. Funkce přebírá parametr pole ukázkových objektů, které definují vstupní data, a preferovaný výstup. Funkce automaticky určí upřednostňovanou transformaci. Pro sloupce vyzvednutí a dropoff času rozdělte čas do hodin, minut a sekund pomocí `split_column_by_example()` funkce bez ukázkového parametru.

Po vygenerování nových funkcí použijte `drop_columns()` funkci k odstranění původních polí, protože jsou upřednostňovány nově vygenerované funkce. Přejmenujte zbývající pole tak, aby používala smysluplné popisy.

Transformaci dat tímto způsobem, aby se vytvořily nové funkce založené na čase, vylepšit přesnost modelů strojového učení. Například vygenerování nové funkce pro den v týdnu pomůže vytvořit vztah mezi dnem v týdnu a cenou taxislužby jízdné, která je často dražší v určité dny v týdnu kvůli vysokému vyžádání.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
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
      <th>Dodavatele</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>cestujících</th>
      <th>vzdálenost</th>
      <th>náklad</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Čtvrtek</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Čtvrtek</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>Ne</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Čtvrtek</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 25:17:00</td>
      <td>Čtvrtek</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>Ne</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Úterý</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Úterý</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>Ne</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Úterý</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Úterý</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>Ne</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Úterý</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Úterý</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>Ne</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Všimněte si, že data ukazují, že komponenty data a času pro vyzvednutí a dropoffy vytvořené z odvozených transformací jsou správné. Přetáhněte sloupce `dropoff_datetime` a, protože už nejsou potřebné (podrobné funkce jako Hour, minuta a sekunda jsou užitečnější pro školení modelu). `pickup_datetime`


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Pomocí funkce odvození typu automaticky zkontrolujte datový typ jednotlivých polí a zobrazit výsledky odvození.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Výsledný výstup `type_infer` je následující.

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Výsledky odvození vypadají na základě dat správně. Nyní použijte převody typu na tok dat.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Než zabalíte datový tok, spusťte dva konečné filtry na datové sadě. Chcete-li eliminovat nesprávně zachycené datové body, vyfiltrujte tok dat `cost` u `distance` záznamů, kde hodnoty proměnných a jsou větší než nula. Tento krok významně vylepšit přesnost modelu strojového učení, protože datové body s nulovými náklady nebo vzdáleností reprezentují hlavní odlehlé hodnoty, které vyvolají přesnost předpovědi.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Teď máte plně transformovaný a připravený objekt Dataflow, který se má použít v modelu strojového učení. Sada SDK obsahuje funkci serializace objektů, která je použita, jak je znázorněno v následujícím kódu.

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Aby bylo možné pokračovat v druhé části kurzu, budete potřebovat soubor **dflows. dprep** v aktuálním adresáři.

Pokud nechcete pokračovat v části 2, odstraňte v aktuálním adresáři soubor **dflows. dprep** . Tento soubor odstraňte bez ohledu na to, jestli spouštíte v místním prostředí nebo v [Azure Notebooks](https://notebooks.azure.com/).

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu můžete:

> [!div class="checklist"]
> * Nastavte vývojové prostředí.
> * Načetly a vyčištěny datové sady.
> * Pomocí inteligentních transformací můžete předpověď logiky na základě příkladu.
> * Sloučené a zabalené datové sady pro školení ve strojovém učení.

Budete připraveni použít školicí data v části 2 tohoto kurzu:

> [!div class="nextstepaction"]
> [Kurz (část 2): Výuka regresního modelu](tutorial-auto-train-models.md)
