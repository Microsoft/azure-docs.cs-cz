---
title: 'Regresní model kurzu: Příprava dat'
titleSuffix: Azure Machine Learning service
description: V první části tohoto kurzu dozvíte, jak pro přípravu dat v jazyce Python pro modelování regrese pomocí sady SDK služby Azure ML.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d20ff1fabfb73c899153cf42bb6f2d7a8f233e21
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314682"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Kurz: Příprava dat pro regresní modelování

V tomto kurzu se dozvíte, jak pro přípravu dat pomocí sady SDK pro Azure Machine Learning Data Prep modelování regrese. Proveďte různé transformace k filtrování a kombinovat dvěma různým sadám dat taxislužby NYC. Konečným cílem této sérii kurzů je odhadnout náklady na cesty taxíkem díky trénování modelu na data funkce včetně vyzvednutí hodinu, den v týdnu, počet cestujících a souřadnice. Tento kurz je první částí z dvou částí série.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení prostředí Pythonu a importovat balíčky
> * Načíst dvě datové sady s názvy jiného pole
> * Čistí data odebrat anomálií
> * Transformace dat pomocí inteligentních transformace k vytvoření nové funkce
> * Uložte objekt toku dat pro použití v regresní model

Můžete připravit vaše data v Pythonu pomocí [sady SDK služby Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk).

## <a name="get-the-notebook"></a>Získání poznámkového bloku

V zájmu usnadnění práce je tento kurz dostupný jako [poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part1-data-prep.ipynb). Spusťte poznámkový blok `regression-part1-data-prep.ipynb` ve službě Azure Notebooks nebo na vlastním serveru poznámkového bloku Jupyter.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Import balíčků

Začněte tím, že import sady SDK.


```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Načtení dat

Stáhněte si dvě různé sady dat taxislužby NYC do toku dat objektů.  Tyto datové sady obsahují pole mírně liší. Metoda `auto_read_file()` automaticky rozpozná typ vstupního souboru.


```python
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file will automatically identify and parse the file type, and is useful if you don't know the file type
yellow_df = dprep.auto_read_file(path=yellow_path)

display(green_df.head(5))
display(yellow_df.head(5))
```

## <a name="cleanse-data"></a>Vyčistit data

Nyní naplňte několik proměnných s místní transformace, které budou platit pro všechny toky dat. Proměnná `drop_if_all_null` se používat k odstranění záznamů, kde jsou všechna pole hodnotu null. Proměnná `useful_columns` obsahuje celou řadu popisy sloupců, které jsou zachovány v každé datového toku.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Nejprve pracovat data taxislužby zelené a get do platné obrazce, který je možné kombinovat s dat taxislužby žlutou. Vytvořit dočasný toku dat `tmp_df`. Volání `replace_na()`, `drop_nulls()`, a `keep_columns()` funkce pomocí zástupce transformace proměnné, které jste vytvořili. Kromě toho přejmenovat všechny sloupce datového rámce tak, aby odpovídaly názvům v `useful_columns`.


```python
tmp_df = (green_df
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
tmp_df.head(5)
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

Přepsat `green_df` proměnné s transformací, proveden na `tmp_df` v předchozím kroku.

```python
green_df = tmp_df
```

Proveďte stejné kroky transformace dat žlutý taxislužby.


```python
tmp_df = (yellow_df
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
tmp_df.head(5)
```

Znovu, přepsat `yellow_df` s `tmp_df`a následně zavolat `append_rows()` funkci pro data taxislužby zelená pro připojení dat taxislužby žlutý, vytvoření nové kombinované datového rámce.


```python
yellow_df = tmp_df
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Převod typů a filtr 

Prozkoumejte sbírat míčky a odkládací souřadnice souhrnné statistiky chcete zobrazit, jak se data distribuovat. Nejprve definovat `TypeConverter` objektu a měnit tak pole lat/dlouho na typ decimal. Pak zavolejte `keep_columns()` funkce výstup omezit pouze na pole lat/dlouho a následně zavolat `get_profile()`.


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
      <th>Typ</th>
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



Z výstupu souhrnné statistiky uvidíte, že existují souřadnic, které chybí a souřadnice, které nejsou v New Yorku. Vyfiltrovat ohraničení město souřadnic ve zřetězení příkazů filtru sloupce v rámci `filter()` fungovat a definování minimální a maximální mezí pro každé pole. Poté zavolejte `get_profile()` znovu k ověření transformace.


```python
tmp_df = (combined_df
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
tmp_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude", 
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
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



Přepsat `combined_df` s transformacemi provedené `tmp_df`.


```python
combined_df = tmp_df
```

### <a name="split-and-rename-columns"></a>Rozdělení a přejmenování sloupců

Podívejte se na data profilu `store_forward` sloupce.


```python
combined_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Typ</th>
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



Z výstupu profil dat `store_forward`, uvidíte, že je nekonzistentní data a hodnoty chybí nebo hodnota null. Nahraďte tyto hodnoty pomocí `replace()` a `fill_nulls()` funkce a v obou případech změnit na řetězec "N".


```python
combined_df = combined_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Provést jiné `replace` , tentokrát na fungovat `distance` pole. To přeformátuje vzdálenost hodnoty, které jsou nesprávně označený jako `.00`a vyplní všechny hodnoty Null nulami. Převést `distance` pole na číselný formát.


```python
combined_df = combined_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
combined_df = combined_df.to_number(["distance"])
```

Rozdělit vyskladnění a zhoršit datetimes do sloupce odpovídajících data a času. Použití `split_column_by_example()` provádět rozdělení. V tomto případě nepovinný `example` parametr `split_column_by_example()` je vynechán. Proto funkce se automaticky určit, kde rozdělit podle data.


```python
tmp_df = (combined_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
tmp_df.head(5)
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


Přejmenování sloupců generovaných `split_column_by_example()` do smysluplné názvy.


```python
tmp_df_renamed = (tmp_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
tmp_df_renamed.head(5)
```

Přepsat `combined_df` provedených transformací, a poté zavolejte `get_profile()` zobrazíte úplné souhrnnou statistiku po všechny transformace.


```python
combined_df = tmp_df_renamed
combined_df.get_profile()
```

## <a name="transform-data"></a>Transformace dat

Pozdější datum vyzvednutí a odkládací rozdělení na den týdne, dne v měsíci a měsíc. Chcete-li získat den v týdnu, použijte `derive_column_by_example()` funkce. Tato funkce přebírá jako parametr pole příklad objekty, které definují vstupních dat a požadovaný výstup. Funkce pak automaticky určuje požadovanou transformaci. Pro sbírat míčky a pro odložení času sloupce rozdělit do hodiny, minuty a sekundy pomocí `split_column_by_example()` funkce s parametrem žádné příklad.

Po vygenerování tyto nové funkce odstranit původní pole ve prospěch nově vygenerovaný funkcí pomocí `drop_columns()`. Přejmenujte všechny zbývající pole na přesné popisy.


```python
tmp_df = (combined_df
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
    # the following two split_column_by_example calls reference the generated column names from the above two calls
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

tmp_df.head(5)
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

Z výše uvedených dat zobrazí správnost sbírat míčky a odkládací datum a čas komponenty vytvořenými odvozené transformace. Přetáhněte `pickup_datetime` a `dropoff_datetime` sloupce jsou už potřeba.


```python
tmp_df = tmp_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Pomocí funkce odvození typu automaticky zkontrolujte datový typ jednotlivých polí a zobrazit výsledky odvození.


```python
type_infer = tmp_df.builders.set_column_types()
type_infer.learn()
type_infer
```

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

Převody typu na toku dat odvození výsledky nevypadají správně na základě dat, nyní použijte.


```python
tmp_df = type_infer.to_dataflow()
tmp_df.get_profile()
```

Než připravíte balíček toku dat, proveďte dvě poslední filtry na datové sadě. Chcete-li odstranit nesprávné datové body, filtrovat toku dat na záznamy kde i `cost` a `distance` jsou větší než nula.

```python
tmp_df = tmp_df.filter(dprep.col("distance") > 0)
tmp_df = tmp_df.filter(dprep.col("cost") > 0)
```

V tomto okamžiku máte objekt toku dat plně transformovaná a připravené k použití v modelu strojového učení. Sada SDK zahrnuje funkce serializace objektu, který se používá takto.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = tmp_df
package = dprep.Package([dflow_prepared])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Stejný soubor odstranit také `dflows.dprep` (ať už spouštíte místně, nebo v poznámkových bloků Azure) v aktuálním adresáři Pokud nechcete pokračovat s druhou částí z tohoto kurzu. Pokud budete pokračovat k druhé části, musíte `dflows.dprep` soubor v aktuálním adresáři.

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu můžete:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Načtené a ukládat očištěné datových sad
> * Použít k předvídání svoji logiku podle příklad inteligentní transformace
> * Sloučené a zabalené datové sady pro trénování machine learning

Jste připraveni na základě těchto dat školení v další části této série kurzů:

> [!div class="nextstepaction"]
> [Kurz #2: Trénování regresní model](tutorial-auto-train-models.md)
