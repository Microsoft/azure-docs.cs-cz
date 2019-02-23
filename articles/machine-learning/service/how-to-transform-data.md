---
title: 'Transformace: Python SDK pro přípravu dat'
titleSuffix: Azure Machine Learning service
description: Další informace o transformaci a čištění dat pomocí sady SDK služby Azure Machine Learning Data Prep. Pomocí metody transformace přidat sloupce odfiltrovat nežádoucí řádků nebo sloupců a dává chybějící hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 730803a0d1dd71c5015ee07a52a766e86ac7f2f5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736348"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformace dat pomocí sady SDK pro Azure Machine Learning Data Prep

V tomto článku se naučíte různými způsoby načítání dat pomocí sady SDK pro Azure Machine Learning Data Prep. Sada SDK nabízí funkce, které se dají jednoduše přidat sloupce odfiltrovat nežádoucí řádků nebo sloupců a dává chybějící hodnoty. Referenční dokumentace pro SDK najdete v tématu [přehled](https://aka.ms/data-prep-sdk).

Tento návod ukazuje příklady pro následující úlohy:

- Přidání sloupce pomocí výrazu
- [Dává chybějící hodnoty](#impute-missing-values)
- [Odvození sloupce podle příkladu](#derive-column-by-example)
- [Filtrování](#filtering)
- [Vlastních transformací Pythonu](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Přidání sloupce pomocí výrazu

Obsahuje sadu SDK pro Azure Machine Learning Data Prep `substring` výrazy můžete použít k výpočtu hodnoty z existujících sloupců a vložte tuto hodnotu v novém sloupci. V tomto příkladu načtěte data a pokusu o přidání sloupců do vstupní data.

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|Domácí|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 23:50:00: 00|ULOŽIT NEWLAND 050XX N|0820|KRÁDEŽ|500 USD A V ČÁSTI|ULICE|false (nepravda)|false (nepravda)|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|ULOŽIT MORSE 011XX W|0460|BATERIE|JEDNODUCHÉ|ULICE|false (nepravda)|true (pravda)|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX LOŽIT FRONT|0486|BATERIE|JEDNODUCHÉ DOMÁCÍ BATERIE|ULICE|false (nepravda)|true (pravda)|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


Použití `substring(start, length)` výraz, který se extrahovat předponu ze sloupce číslo případ a vložit tento řetězec v novém sloupci `Case Category`. Předání `substring_expression` proměnnou `expression` parametr vytvoří nový počítaný sloupec, který provádí výraz u jednotlivých záznamů.

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Číslo případu|Kategorie případu|Datum|Zablokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 23:50:00: 00|ULOŽIT NEWLAND 050XX N|0820|KRÁDEŽ|500 USD A V ČÁSTI|ULICE|false (nepravda)|false (nepravda)|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|ULOŽIT MORSE 011XX W|0460|BATERIE|JEDNODUCHÉ|ULICE|false (nepravda)|true (pravda)|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX LOŽIT FRONT|0486|BATERIE|JEDNODUCHÉ DOMÁCÍ BATERIE|ULICE|false (nepravda)|true (pravda)|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



Použití `substring(start)` výraz k extrahování pouze číslo z případ číslo sloupce a vytvoří nový sloupec. Převod na číselný datový typ pomocí `to_number()` fungovat a předat jako parametr řetězec název sloupce.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Dává chybějící hodnoty

Sady SDK můžete dává chybějící hodnoty v zadaných sloupcích. V tomto příkladu načíst hodnoty zeměpisné šířky a délky a potom se pokuste dává chybějící hodnoty ve vstupních datech.

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(3)
```

||ID|Zadržení|Zeměpisná šířka|Zeměpisná délka|
|-----|------|-----|------|-----|
|0|10140490|false (nepravda)|41.973309|-87.800175|
|1|10139776|false (nepravda)|42.008124|-87.659550|
|2|10140270|false (nepravda)|NaN|NaN|

Třetí záznamu chybí hodnoty zeměpisné šířky a délky. Dává tyto chybějící hodnoty, použijte [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) další výraz pevné. To dává sloupce s buď výpočtová `MIN`, `MAX`, `MEAN` hodnotu, nebo `CUSTOM` hodnotu. Když `group_by_columns` není zadána, chybějící hodnoty budou uložené ve skupině s `MIN`, `MAX`, a `MEAN` počítá na skupinu.

Zkontrolujte `MEAN` hodnotu pomocí zeměpisné šířky sloupce [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funkce. Tato funkce přijímá pole sloupců `group_by_columns` parametr k určení úrovně agregace. `summary_columns` Parametr přijímá `SummaryColumnsValue` volání. Toto volání funkce určuje aktuální název sloupce, nový název počítaného pole a `SummaryFunction` provádět.

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||Zadržení|Latitude_MEAN|
|-----|-----|----|
|0|false (nepravda)|41.878961|

`MEAN` Vypadá přesná hodnota meze, použijte `ImputeColumnArguments` funkce dává ji. Tato funkce přijme `column_id` řetězce a `ReplaceValueFunction` k určení typu impute. Pro chybějící hodnoty zeměpisné délky dává s 42 na základě externí znalostí.

Dává kroky je možné zřetězit do `ImputeMissingValuesBuilder` objektu pomocí funkce Tvůrce `impute_missing_values()`. `impute_columns` Parametr přijímá pole `ImputeColumnArguments` objekty. Volání `learn()` funkci pro uložení impute kroky a použijte na objektu toku dat pomocí `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
df_imputed = impute_builder.to_dataflow()
df_imputed.head(3)
```

||ID|Zadržení|Zeměpisná šířka|Zeměpisná délka|
|-----|------|-----|------|-----|
|0|10140490|false (nepravda)|41.973309|-87.800175|
|1|10139776|false (nepravda)|42.008124|-87.659550|
|2|10140270|false (nepravda)|41.878961|42.000000|

Jak je znázorněno výše výsledek, chybějící šířky byl splněn s `MEAN` hodnotu `Arrest=='false'` skupiny. Chybějící délky byl splněn s 42.

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Odvození sloupce podle příkladu

Jeden z pokročilejší nástroje v sadě SDK pro Azure Machine Learning Data Prep je schopnost odvodit sloupců pomocí příklady požadovaných výsledků. Díky tomu můžete poskytnout sadu SDK příklad tak může také generovat kód pro dosažení zamýšlený transformace.

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(4)
```

||DATE (Datum)|REPORTTYPE (Typ zprávy)|HOURLYDRYBULBTEMPF (Teplota suchého teploměru ve stupních Fahrenheita)|HOURLYRelativeHumidity (Hodinová relativní vlhkost)|HOURLYWindSpeed (Hodinová rychlost větru)|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Předpokládejme, že je potřeba připojit tento soubor s datovou sadou, kde jsou data a času ve formátu "10. března 2018 | 2: 00 – 4: 00 ".

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE (Datum)|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1. ledna 2015 12 AM - 2 AM|
|1|1/1/2015 1:00|1. ledna 2015 12 AM - 2 AM|
|2|1/1/2015 1:54|1. ledna 2015 12 AM - 2 AM|
|3|1/1/2015 2:54|1. ledna 2015 2 AM - 4 AM|
|4|1/1/2015 3:54|1. ledna 2015 2 AM - 4 AM|

Výše uvedený kód nejprve vytvoří tvůrce odvozené sloupce. Zadejte pole zdrojové sloupce, které byste měli zvážit (`DATE`) a název pro nový sloupec, který chcete přidat. Jako v prvním příkladu předejte ve druhém řádku (index 1) a poskytují očekávaná hodnota pro odvozených sloupců.

Nakonec proveďte volání `builder.preview(skip=30, count=5)` a lze zobrazit odvozené sloupce vedle zdrojového sloupce. Formát zdá se, že správný, ale hodnoty se zobrazí jenom pro stejné datum "1. ledna 2015".

Nyní, předejte počet řádků, které chcete `skip` z horní části zobrazte řádků níže.

> [!NOTE]
> Funkce preview() přeskočí řádky, ale není znovu číslo indexu výstup. V následujícím příkladu odpovídá index 0 v tabulce indexu 30 datového toku.

```python
builder.preview(skip=30, count=5)
```

||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1. ledna 2015 22: 00 - 00: 00|
|1|1/1/2015 23:54|1. ledna 2015 22: 00 - 00: 00|
|2|1/1/2015 23:59|1. ledna 2015 22: 00 - 00: 00|
|3|1/2/2015 0:54|1. února 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|1. února 2015 12 AM - 2 AM|

Tady najdete v článku o problém s generovaného programu. Založené výhradně na jeden příklad, který jste zadali výše, program odvodit zvolili analyzovat datum jako "Den/měsíc/rok", což je nechcete v tomto případě. Chcete-li vyřešit tento problém, cílit na konkrétní záznam indexu a zadejte jiný příklad použití `add_example()` na fungovat `builder` proměnné.

```python
builder.add_example(source_data=df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1. ledna 2015 22: 00 - 00: 00|
|1|1/1/2015 23:54|1. ledna 2015 22: 00 - 00: 00|
|2|1/1/2015 23:59|1. ledna 2015 22: 00 - 00: 00|
|3|1/2/2015 0:54|2. ledna 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|2. ledna 2015 12 AM - 2 AM|

Nyní správně ošetřit řádků "1/2/2015' jako"2 ledna 2015", ale když se podíváte nad rámec indexu 76 odvozených sloupců, uvidíte, že hodnoty na konci nesouvisí v odvozených sloupců.

```python
builder.preview(skip=75, count=5)
```


||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3. ledna 2015 6: 00 - 8: 00|
|1|1/3/2015 7:54|3. ledna 2015 6: 00 - 8: 00|
|2|1/29/2015 6:54|Žádný|
|3|1/29/2015 7:00|Žádný|
|4|1/29/2015 7:54|Žádný|

```python
builder.add_example(source_data=df.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```
||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|3. ledna 2015 6: 00 - 8: 00|
|1|1/3/2015 7:54|3. ledna 2015 6: 00 - 8: 00|
|2|1/29/2015 6:54|29. ledna 2015 6: 00 - 8: 00|
|3|1/29/2015 7:00|29. ledna 2015 6: 00 - 8: 00|
|4|1/29/2015 7:54|29. ledna 2015 6: 00 - 8: 00|

 Chcete zobrazit seznam aktuální odvození příkladu volání `list_examples()` tvůrce objektu.

```python
examples = builder.list_examples()
```

| |DATE (Datum)|Příklad|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1. ledna 2015 12 AM - 2 AM|-1|
|1|1/2/2015 0:54|2. ledna 2015 12 AM - 2 AM|-2|
|2|29/1/2015 20:54|29. ledna 2015 20: 00 – 22: 00|-3|


V některých případech, pokud chcete odstranit příklady, které jsou nesprávné, můžete předat buď `example_row` z pandas DataFrame, nebo `example_id` hodnotu. Například pokud spustíte `builder.delete_example(example_id=-1)`, odstraní se v prvním příkladu transformace.


Volání `to_dataflow()` na tvůrce, který vrátí toku dat s požadovanou odvozených sloupců, které jsou přidány.

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtrování

Sada SDK zahrnuje metody [ `Dataflow.drop_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) a [ `Dataflow.filter()` ]((https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#filter-expression--azureml-dataprep-api-expressions-expression-----azureml-dataprep-api-dataflow-dataflow)) umožňuje odfiltrovat sloupců nebo řádků.

### <a name="initial-setup"></a>Počáteční nastavení

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|Ne|1|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|Ne|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|Ne|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|Ne|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrování sloupce

Chcete-li filtrovat sloupce, použijte `Dataflow.drop_columns()`. Tato metoda přebírá seznam sloupců, vyřaďte nebo ji volat složitější argument [ `ColumnSelector` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrování sloupce s seznamu řetězců

V tomto příkladu `drop_columns` přebírá seznam řetězců. Každý řetězec by měl přesně odpovídat na požadovaný sloupec vyřadit.

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21,25|

#### <a name="filtering-columns-with-regex"></a>Filtrování sloupce s použitím regex

Můžete taky použít `ColumnSelector` výraz vyřazení sloupce, které odpovídají regulárnímu výrazu. V tomto příkladu je vyřadit všechny sloupce, které odpovídají výrazu `Column*|.*longitude|.*latitude`.

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21,25|

## <a name="filtering-rows"></a>Filtrování řádků

Chcete-li filtrovat řádky, použijte `DataFlow.filter()`. Tato metoda přebírá sady SDK služby Azure Machine Learning Data Prep výraz jako argument a vrací nový tok dat s řádky, které se výraz vyhodnotí jako True. Výrazy se vytvářejí pomocí Tvůrce výrazů (`col`, `f_not`, `f_and`, `f_or`) a pravidelné operátory (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrování řádků pomocí jednoduchých výrazů

Tvůrce výrazů `col`, zadejte název sloupce jako argument řetězec `col('column_name')`. Tento výraz použít v kombinaci s jedním z následujících standardní operátory >, <>, =, < =, ==,! = k sestavování, jako výraz `col('Tip_amount') > 0`. A konečně, předejte sestavené výraz do `Dataflow.filter` funkce.

V tomto příkladu `dataflow.filter(col('Tip_amount') > 0)` vrátí nového toku dat s řádky, ve kterém hodnotu `Tip_amount` je větší než 0.

> [!NOTE] 
> `Tip_amount` nejprve převeden na číselnou hodnotu, která umožňuje vytvářet výrazu porovnání oproti dalších číselných hodnot.

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrování řádků s složité výrazy

Filtrovat pomocí složité výrazy, jeden nebo více jednoduché výrazy s tvůrci výrazu kombinovat `f_not`, `f_and`, nebo `f_or`.

V tomto příkladu `Dataflow.filter()` vrátí nového toku dat s řádky kde `'Passenger_count'` je menší než 5 a `'Tolls_amount'` je větší než 0.

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Je také možné filtrovat řádky kombinování více než jeden Tvůrce výrazů a vytvořit vnořené výraz.

> [!NOTE]
> `lpep_pickup_datetime` a `Lpep_dropoff_datetime` jsou nejprve převeden na typ datetime, která umožňuje vytvářet výrazu porovnání oproti jiné hodnoty data a času.

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9,57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Vlastních transformací Pythonu

Bude vždy scénáře při nejjednodušší možnost pro provedení transformace je psaní vlastního skriptu. Sada SDK poskytuje tři Rozšiřovací body, které můžete použít pro vlastní skripty Python.

- Nový sloupec skriptu
- Nový filtr skriptu
- Transformace oddílu

Každé rozšíření je podporována v běhu vertikálně navýšit kapacitu a škálování na víc systémů. Hlavní výhodou používání těchto Rozšiřovací body je, že není nutné vyžádat všechna data, aby bylo možné vytvořit objekt frame, data. Vaše vlastní Python, který kód se spustí stejně, jako jsou dalších transformací ve velkém měřítku, oddílem a obvykle paralelně.

### <a name="initial-data-preparation"></a>Počáteční data přípravy

Začněte tím, že načítání nějaká data z objektů Blob v Azure.

```python
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale kraj|10171002158| |
|1|ALABAMA|1|101710|Hale kraj|10171002162| |

Trim dolů datové sady a provádět některé základní transformací, včetně odebrání sloupců, nahraďte hodnoty a převod typů.

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|1|ALABAMA|Hale kraj|1.017100e + 10|Žádný|

Vyhledejte pomocí filtru, který tyto hodnoty null.

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|1|ALABAMA|Hale kraj|1.017100e + 10|Žádný|

### <a name="transform-partition"></a>Transformace oddílu

Použití [ `transform_partition()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow) nahradit všechny hodnoty null na 0. Tento kód se spustí oddílu není na najednou celou datovou sadu. To znamená, že na velkých sadách dat, tento kód může spouštět paralelně jak modul runtime zpracovává data, oddílu pomocí oddílů.

Skript v jazyce Python musí definovat funkci s názvem `transform()` , který přebírá dva argumenty, `df` a `index`. `df` Argument bude pandas dataframe, obsahující data pro oddíl a `index` argument je jedinečný identifikátor oddílu. Funkce transformace může plně upravit předané datového rámce, ale musí vracet datový rámec. Všechny knihovny, které importuje skript v jazyce Python musí existovat v prostředí, ve kterém se spouští toku.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|0.0|
|1|ALABAMA|Hale kraj|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Nový sloupec skriptu

Vytvoří nový sloupec, který má název kraje a název stavu a také velké první písmeno názvu stavu, můžete použít skript Pythonu. Chcete-li to provést, použijte [ `new_script_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow) metodu na datový tok.

Skript v jazyce Python musí definovat funkci s názvem `newvalue()` , která přijímá jeden argument `row`. `row` Argument je dict (`key`: název sloupce `val`: aktuální hodnota) a předá pro tuto funkci pro každý řádek v datové sadě. Tato funkce musí vracet hodnotu pro použití v tomto novém sloupci. Všechny knihovny, které importuje skript v jazyce Python musí existovat v prostředí, ve kterém se spouští toku.

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Nový filtr skriptu

Sestavit výraz Pythonu pomocí [ `new_script_filter()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow) k filtrování sady dat pro pouze řádky, kde "Hale" není na novém `county_state` sloupce. Výraz vrací `True` Pokud my chceme zajistit řádku a `False` vyřadit řádku.

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|0.0|

## <a name="next-steps"></a>Další postup

* Sada SDK [přehled](https://aka.ms/data-prep-sdk) vzory návrhu a příklady použití
* Zobrazit sadu SDK pro Azure Machine Learning Data Prep [kurzu](tutorial-data-prep.md) příklad řešení konkrétní scénář