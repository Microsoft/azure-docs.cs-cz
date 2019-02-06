---
title: 'Regresní model kurzu: Příprava dat'
titleSuffix: Azure Machine Learning service
description: V první části tohoto kurzu se dozvíte, jak k přípravě dat v jazyce Python pro regresní modelování pomocí sady SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cforbe
ms.author: cforbe
ms.reviewer: trbye
ms.date: 02/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7be1bf8c003315fc4dbed449283f7c92850edced
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752037"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Kurz: Příprava dat pro regresní modelování

V tomto kurzu se dozvíte, jak připravit data pro modelování s použitím sady SDK pro Azure Machine Learning Data Prep regrese. Spuštění různých transformací pro filtrování a kombinovat dvěma různým sadám dat taxislužby NYC.

Tento kurz je **první částí z dvoudílné série kurzů**. Po dokončení série kurzů, lze odhadnout náklady na cesty taxíkem díky trénování modelu na datových funkcích. Tyto funkce patří vyzvednutí den a čas, počet cestujících a výstupní umístění.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení prostředí Pythonu a importovat balíčky.
> * Načtěte dvě datové sady s názvy jiného pole.
> * Čistí data odebrat anomálie.
> * Transformace dat pomocí inteligentních transformace k vytvoření nové funkce.
> * Uložte objekt toku dat pro použití v regresní model.

## <a name="prerequisites"></a>Požadavky

Přejděte k [nastavení vývojového prostředí](#start) číst kroky Poznámkový blok, nebo použijte níže uvedené pokyny k získání poznámkového bloku a spustit ho v poznámkových bloků Azure nebo vašeho vlastního serveru poznámkového bloku. Pokud chcete spustit Poznámkový blok, bude nutné:

* Server poznámkového bloku Python 3.6 s nainstalované tyto položky:
    * Azure Machine Learning přípravy dat sady SDK pro Python
* Výukový program Poznámkový blok

Získejte všechny tyto požadavky pomocí kteréhokoli z níže uvedených částech.

* Použití [poznámkových bloků Azure](#azure)
* Použití [serveru poznámkového bloku](#server)

### <a name="azure"></a>Použití poznámkových bloků Azure: Bezplatné poznámkové bloky Jupyter v cloudu

Je snadné začít s poznámkovými bloky Azure! [Aplikaci Azure Machine Learning Data Prep SDK pro Python](https://aka.ms/data-prep-sdk) je již nainstalován a nakonfigurován pro vás na [poznámkových bloků Azure](https://notebooks.azure.com/). Instalace a aktualizace budoucí jsou automaticky spravovány prostřednictvím služby Azure.

Po dokončení následujících kroků spustit **kurzy/regrese – část 1 data-prep.ipynb** Poznámkový blok v vaše **Začínáme** projektu.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Použít vlastní server poznámkového bloku Jupyter

Pomocí těchto kroků můžete vytvořit místní aplikace Jupyter Notebook server ve vašem počítači.  Po dokončení kroků, spusťte **kurzy/regrese – část 1 data-prep.ipynb** poznámkového bloku.

1. Dokončení [rychlý start Python pro Azure Machine Learning](quickstart-create-workspace-with-python.md) vytvoření Miniconda prostředí.  Můžete přejít **vytvořit pracovní prostor** části, ale je budete potřebovat pro [2. část](tutorial-auto-train-models.md) této série kurzů.
1. Instalace v prostředí pomocí sady SDK pro Data Prep `pip install azureml-dataprep`.
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook

## <a name="start"></a>Set up your development environment

All the setup for your development work can be accomplished in a Python notebook. Setup includes the following actions:

* Install the SDK
* Import Python packages

### Install and import packages

Use the following to install necessary packages if you don't already have them.

```shell
pip install azureml-dataprep
```

Importujte sady SDK.

```python
import azureml.dataprep as dprep
```

## <a name="load-data"></a>Načtení dat

Stáhněte si dvě různé sady dat taxislužby NYC do toku dat objektů. Datové sady mají mírně odlišné pole. `auto_read_file()` Metoda automaticky rozpozná typ vstupního souboru.

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

A `Dataflow` objektu je podobný datový rámec a reprezentuje řadu laxně vyhodnotit, neměnné operací s daty. Operace přidat vyvoláním různé transformace a filtrování dostupné metody. Výsledek operace přidání `Dataflow` je vždy nová `Dataflow` objektu.

## <a name="cleanse-data"></a>Vyčistit data

Nyní naplňte několik proměnných s místní transformace na platí pro všechny toky dat. `drop_if_all_null` Proměnná slouží k odstranění záznamů, kde jsou všechna pole hodnotu null. `useful_columns` Proměnná obsahuje celou řadu popisy sloupců, které jsou zachovány v každé datového toku.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Nejprve práci s daty zelené taxislužby můžete načíst platný tvar, který je možné kombinovat s dat taxislužby žlutou. Volání `replace_na()`, `drop_nulls()`, a `keep_columns()` funkce pomocí zástupce transformace proměnné, které jste vytvořili. Kromě toho přejmenovat všechny sloupce datového rámce tak, aby odpovídaly názvům v `useful_columns` proměnné.


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

Spusťte stejné kroky transformace dat žlutý taxislužby. Tyto funkce Ujistěte se, že null data se odeberou z datové sady, které vám pomůžou zvýšit machine learning model přesnost.

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

Volání `append_rows()` funkci pro data taxislužby zelená pro připojení dat taxislužby žlutou. Vytvoří se nové kombinované datového rámce.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Převod typů a filtr

Prozkoumejte sbírat míčky a odkládací souřadnice souhrnné statistiky chcete zobrazit, jak se data distribuovat. Nejprve definujte `TypeConverter` objektu a měnit tak pole zeměpisné šířky a délky na typ decimal. Pak zavolejte `keep_columns()` funkce omezit výstup pouze zeměpisné šířky a délky pole a následně zavolat `get_profile()` funkce. Tato volání funkce vytvořit zhuštěnému zobrazení toku dat jenom pro ukázku lat/dlouho polí, která usnadňuje vyhodnotit chybějící nebo souřadnice mimo rozsah.


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
      <th>Type</th>
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



Z výstupu souhrnné statistiky, uvidíte, jsou chybějící souřadnice a souřadnic, které nejsou v New Yorku (to je určen z subjektivní analýzy). Vyfiltrování souřadnic pro umístění, které jsou mimo hranice město. Řetězec sloupcový filtr příkazy v rámci `filter()` fungovat a definovat minimální a maximální mezí pro každé pole. Zavolejte `get_profile()` funkce znovu k ověření transformace.


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
      <th>Type</th>
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

Podívejte se na data profilu `store_forward` sloupce. Toto pole je logický příznak, který je `Y` při taxislužby nemá připojení k serveru po cesty a proto bylo nutné ukládat data o jízdách v paměti a předá je později na server při připojení.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
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



Všimněte si, že profil dat výstup v `store_forward` sloupci se zobrazuje, že data nekonzistentní a jsou chybějící nebo hodnoty null. Použití `replace()` a `fill_nulls()` funkce tyto hodnoty nahraďte řetězec "N":


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Spustit `replace` na fungovat `distance` pole. Funkce přeformátuje vzdálenost hodnoty, které jsou nesprávně označený jako `.00`a vyplní všechny hodnoty Null nulami. Převést `distance` pole na číselný formát. Tyto body nesprávná data jsou pravděpodobně anomolies v systému kolekce dat u souborů CAB taxislužby.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Hodnoty data a času sbírat míčky a dropoff rozdělte příslušného sloupce data a času. Použití `split_column_by_example()` funkci rozdělení. V tomto případě nepovinný `example` parametr `split_column_by_example()` funkce je vynechán. Proto se funkce automaticky určí, kde rozdělit podle data.


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

Přejmenování sloupců generovaných `split_column_by_example()` funkce použijte smysluplné názvy.

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

Volání `get_profile()` funkci zobrazíte úplné souhrnné statistiky koneckonců čištění.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformace dat

Rozdělte sbírat míčky a dropoff pozdější datum na den, týden, den v měsíci a hodnoty za měsíc. Den v týdnu hodnoty, použijte `derive_column_by_example()` funkce. Funkce přijímá parametr pole, které definují vstupní data a upřednostňovaného výstupního objektů příklad. Funkce automaticky určuje upřednostňovaný transformace. Pro čas sloupce sbírat míčky a dropoff rozdělení čas na hodinu, minutu a sekundu pomocí `split_column_by_example()` funkce s parametrem žádné příklad.

Po vytvoření nové funkce, použijte `drop_columns()` funkce odstranit původní pole jsou upřednostňované nově vygenerovaný funkce. Přejmenujte zbývající pole na smysluplný popis.

Transformace dat v tento způsob, jak vytvořit nové funkce založené na čase zlepší machine learning model přesnost. Například vygenerovat novou funkci pro den v týdnu vám pomůže vytvořit vztah mezi den v týdnu a cena taxislužby tarif, který je často drahé v určité dny v týdnu kvůli vysokému zatížení.


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

Všimněte si, že data odhalí sbírat míčky a dropoff součásti datum a čas vytvořenými odvozené transformace správné. Přetáhněte `pickup_datetime` a `dropoff_datetime` sloupce, protože jsou již potřeby (detailní čas funkcí, jako jsou hodinu, minutu a sekundu se další užitečné k tréninku modelu).


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Pomocí funkce odvození typu automaticky zkontrolujte datový typ jednotlivých polí a zobrazit výsledky odvození.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Výsledný výstup `type_infer` vypadá takto.

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

Odvození výsledky nevypadají správně založené na datech. Převody typu na toku dat teď použijte.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Předtím, než budete balíček toku dat, spusťte dvě poslední filtry na datové sadě. Chcete-li odstranit nesprávně zachycené datových bodů, filtrovat toku dat na záznamy kde i `cost` a `distance` hodnoty proměnných jsou větší než nula. Tento krok výrazně zlepší strojového učení přesnost modelu, protože náklady datových bodů s nulou nebo vzdálenost představují hlavní odlehlé hodnoty, které vyvolat vypnout přesnost předpovědi.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Teď máte objekt toku dat plně transformovaná a připravené k použití v modelu strojového učení. Sada SDK zahrnuje funkce serializace objektu, který se používá, jak je znázorněno v následujícím kódu.

```python
import os
file_path = os.path.join(os.getcwd(), "dflows.dprep")

package = dprep.Package([final_df])
package.save(file_path)
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V druhé části kurzu pokračovat, je nutné **dflows.dprep** soubor v aktuálním adresáři.

Pokud nemáte v úmyslu pokračovat v druhé části, odstraňte **dflows.dprep** soubor v aktuálním adresáři. Odstranit tento soubor, jestli používáte provádění místně nebo v [poznámkových bloků Azure](https://notebooks.azure.com/).

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu můžete:

> [!div class="checklist"]
> * Nastavení vývojového prostředí.
> * Načtené a ukládat očištěné datových sad.
> * Inteligentní transformací použít k předvídání logiky založené na příklad.
> * Sloučené a zabalené datové sady pro trénování machine learning.

Jste připraveni používat trénovací data ve druhé části kurzu:

> [!div class="nextstepaction"]
> [Kurz (část 2): Trénování regresní model](tutorial-auto-train-models.md)
