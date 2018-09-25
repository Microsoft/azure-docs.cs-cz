---
title: Transformace dat pomocí Azure Machine Learning Data Prep SDK – Python
description: Další informace o transformaci a čištění dat pomocí sady SDK služby Azure Machine Learning Data Prep. Pomocí metody transformace přidat sloupce odfiltrovat nežádoucí řádků nebo sloupců a dává chybějící hodnoty.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 388957fc5dac5cdab5bee34a4431eaa524e76a48
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999898"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformace dat pomocí sady SDK pro Azure Machine Learning Data Prep

[Sady SDK služby Azure Machine Learning Data Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) nabízí různé transformace metody můžete vyčistit svá data. Tyto metody usnadňují přidání sloupce, odfiltrovat nežádoucí řádků nebo sloupců a dává chybějící hodnoty.

Aktuálně jsou dostupné metody pro následující úlohy:
- [Přidání sloupce pomocí výrazu](#add-column-using-expression)
- [Dává chybějící hodnoty](#impute-missing-values)
- [Odvození sloupce podle příkladu](#derive-column-by-example)
- [Filtrování](#filtering)
- [Vlastních transformací Pythonu](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Přidání sloupce pomocí výrazu

Obsahuje sadu SDK pro Azure Machine Learning Data Prep `substring` výrazy můžete použít k výpočtu hodnoty z existujících sloupců a vložte tuto hodnotu v novém sloupci. V tomto příkladu jsme budete načtení dat a znovu zkusíte přidat sloupce pro vstupní data.

```
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Číslo případu|Datum|Blokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|Domácí|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 23:50:00: 00|ULOŽIT NEWLAND 050XX N|0820|KRÁDEŽ|500 USD A V ČÁSTI|ULICE|false (nepravda)|false (nepravda)|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|ULOŽIT MORSE 011XX W|0460|BATERIE|JEDNODUCHÉ|ULICE|false (nepravda)|true (pravda)|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX LOŽIT FRONT|0486|BATERIE|JEDNODUCHÉ DOMÁCÍ BATERIE|ULICE|false (nepravda)|true (pravda)|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



Použití `substring(start, length)` výraz, který se extrahovat předponu ze sloupce číslo případ a vložit tato data v novém sloupci: kategorie případu.

```
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Číslo případu|Kategorie případu|Datum|Blokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 23:50:00: 00|ULOŽIT NEWLAND 050XX N|0820|KRÁDEŽ|500 USD A V ČÁSTI|ULICE|false (nepravda)|false (nepravda)|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|ULOŽIT MORSE 011XX W|0460|BATERIE|JEDNODUCHÉ|ULICE|false (nepravda)|true (pravda)|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX LOŽIT FRONT|0486|BATERIE|JEDNODUCHÉ DOMÁCÍ BATERIE|ULICE|false (nepravda)|true (pravda)|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



Použití `substring(start)` výraz k extrahování pouze číslo z případ číslo sloupce, pak ho převést na číselný datový typ a vložit ho do nového sloupce: ID případu.
```
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|Číslo případu|Id případu|Datum|Blokovat|IUCR|Primární typ|Popis|Popis umístění|Zadržení|...|Dál|Oblast komunity|Úřadu FBI kódu|Souřadnici x|Souřadnice Y|Rok|Aktualizace|Zeměpisná šířka|Zeměpisná délka|Umístění|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 23:50:00: 00|ULOŽIT NEWLAND 050XX N|0820|KRÁDEŽ|500 USD A V ČÁSTI|ULICE|false (nepravda)|false (nepravda)|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|ULOŽIT MORSE 011XX W|0460|BATERIE|JEDNODUCHÉ|ULICE|false (nepravda)|true (pravda)|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 11:20:00 PM|121XX LOŽIT FRONT|0486|BATERIE|JEDNODUCHÉ DOMÁCÍ BATERIE|ULICE|false (nepravda)|true (pravda)|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|

## <a name="impute-missing-values"></a>Dává chybějící hodnoty

Sady SDK pro Azure Machine Learning Data Prep můžete dává chybějící hodnoty v zadaných sloupcích. V tomto příkladu se načíst hodnoty zeměpisné šířky a délky a potom se pokuste dává chybějící hodnoty ve vstupních datech.

```
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|Zadržení|Zeměpisná šířka|Zeměpisná délka|
|-----|------|-----|------|-----|
|0|10140490|false (nepravda)|41.973309|-87.800175|
|1|10139776|false (nepravda)|42.008124|-87.659550|
|2|10140270|false (nepravda)|NaN|NaN|
|3|10139885|false (nepravda)|41.902152|-87.754883|
|4|10140379|false (nepravda)|41.885610|-87.657009|

Třetí záznamu chybí hodnoty zeměpisné šířky a délky. Chcete-li dává tyto chybějící hodnoty, můžete použít `ImputeMissingValuesBuilder` další oprava programu. To dává sloupce s buď výpočtová `MIN`, `MAX`, nebo `MEAN` hodnotu nebo `CUSTOM` hodnotu. Když `group_by_columns` není zadána, chybějící hodnoty budou uložené ve skupině s `MIN`, `MAX`, a `MEAN` počítá na skupinu.

Za prvé, rychle zkontrolovat, `MEAN` hodnota zeměpisné šířky sloupce.
```
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

`MEAN` Hodnotu meze vypadá dobře, takže ho můžete dává zeměpisná šířka. Chybí hodnota délky jsme se to dává s 42 na základě externí znalostí.


```
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|Zadržení|Zeměpisná šířka|Zeměpisná délka|
|-----|------|-----|------|-----|
|0|10140490|false (nepravda)|41.973309|-87.800175|
|1|10139776|false (nepravda)|42.008124|-87.659550|
|2|10140270|false (nepravda)|41.878961|42.000000|
|3|10139885|false (nepravda)|41.902152|-87.754883|
|4|10140379|false (nepravda)|41.885610|-87.657009|

Jak je znázorněno výše výsledek, chybějící šířky byl splněn s `MEAN` hodnotu `Arrest=='false'` skupiny. Chybějící délky byl splněn s 42.
```
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Odvození sloupce podle příkladu
Jeden z pokročilejší nástroje v sadě SDK pro Azure Machine Learning Data Prep je schopnost odvodit sloupců pomocí příklady požadované výsledky. Díky tomu můžete poskytnout sadu SDK příklad tak může také generovat kód pro dosažení zamýšlený odvození.

```
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
df = dataflow.head(10)
df
```
||DATE (Datum)|REPORTTYPE (Typ zprávy)|HOURLYDRYBULBTEMPF (Teplota suchého teploměru ve stupních Fahrenheita)|HOURLYRelativeHumidity (Hodinová relativní vlhkost)|HOURLYWindSpeed (Hodinová rychlost větru)|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

Jak je vidět, tento soubor je poměrně jednoduché. Ale předpokládat, že je potřeba připojit tento soubor s datovou sadou, kde jsou data a času ve formátu "10. března 2018 | 2: 00 – 4: 00 ".

Transformujte data na obrazec, který potřebujete.

```
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE (Datum)|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|1. ledna 2015 12 AM - 2 AM|
|1|1/1/2015 1:00|1. ledna 2015 12 AM - 2 AM|
|2|1/1/2015 1:54|1. ledna 2015 12 AM - 2 AM|
|3|1/1/2015 2:54|1. ledna 2015 2 AM - 4 AM|
|4|1/1/2015 3:54|1. ledna 2015 2 AM - 4 AM|
|5|1/1/2015 4:00|1. ledna 2015 4: 00 - 6: 00|
|6|1/1/2015 4:54|1. ledna 2015 4: 00 - 6: 00|
|7|1/1/2015 5:54|1. ledna 2015 4: 00 - 6: 00|
|8|1/1/2015 6:54|1. ledna 2015 6: 00 - 8: 00|
|9|1/1/2015 7:00|1. ledna 2015 6: 00 - 8: 00|

Výše uvedený kód nejprve vytvoří tvůrce odvozené sloupce. Poskytuje celou řadu zdrojové sloupce, které byste měli zvážit (`DATE`) a název pro nový sloupec, který chcete přidat.

Pak, jako v prvním příkladu předané ve druhém řádku (index 1) a dala očekávaná hodnota pro odvozených sloupců.

Nakonec jste volali `builder.preview()` a lze zobrazit odvozené sloupce vedle zdrojového sloupce. Formát vypadá dobře, ale hodnoty se zobrazí jenom pro stejné datum "1. ledna 2015".

Nyní, předejte počet řádků, které chcete `skip` z horní části zobrazte řádků níže.

```
preview_df = builder.preview(skip=30)
preview_df
```

||DATE (Datum)|date_timerange|
|-----|-----|-----|
|30|11/1/2015 22:54|1. ledna 2015 22: 00 - 00: 00|
|31|11/1/2015 23:54|1. ledna 2015 22: 00 - 00: 00|
|32|11/1/2015 23:59|1. ledna 2015 22: 00 - 00: 00|
|33|11/2/2015 0:54|1. února 2015 12 AM - 2 AM|
|34|11/2/2015 1:00|1. února 2015 12 AM - 2 AM|
|35|11/2/2015 1:54|1. února 2015 12 AM - 2 AM|
|36|11/2/2015 2:54|1. února 2015 2 AM - 4 AM|
|37|11/2/2015 3:54|1. února 2015 2 AM - 4 AM|
|38|11/2/2015 4:00|1. února 2015 4: 00 - 6: 00|
|39|11/2/2015 4:54|1. února 2015 4: 00 - 6: 00|

Zde můžete zobrazit problém s generovaného programu: založené výhradně na jeden příklad, který jste zadali výše, program odvodit zvolili analyzovat datum jako "Den/měsíc/rok", což je nechcete v tomto případě.

Chcete-li vyřešit tento problém, budete muset zadat další příklad.

```
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
preview_df = builder.preview(skip=30, count=10)
preview_df
```

||DATE (Datum)|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|1. ledna 2015 22: 00 - 00: 00|
|31|1/1/2015 23:54|1. ledna 2015 22: 00 - 00: 00|
|32|1/1/2015 23:59|1. ledna 2015 22: 00 - 00: 00|
|33|1/2/2015 0:54|2. ledna 2015 12 AM - 2 AM|
|34|1/2/2015 1:00|2. ledna 2015 12 AM - 2 AM|
|35|1/2/2015 1:54|2. ledna 2015 12 AM - 2 AM|
|36|1/2/2015 2:54|2. ledna 2015 2 AM - 4 AM|
|37|1/2/2015 3:54|2. ledna 2015 2 AM - 4 AM|
|38|1/2/2015 4:00|2. ledna 2015 4: 00 - 6: 00|
|39|1/2/2015 4:54|2. ledna 2015 4: 00 - 6: 00|


Nyní správně ošetřit řádků "1/2/2015' jako 'Jan 2 2015", ale pokud se podíváte níže odvozených sloupců, uvidíte, že hodnoty na konci nesouvisí v odvozených sloupců. Který chcete vyřešit, budete muset zadat další příklad pro řádek 66.

```
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|1. ledna 2015 22: 00 - 00: 00|
|1|1/1/2015 23:54|1. ledna 2015 22: 00 - 00: 00|
|2|1/1/2015 23:59|1. ledna 2015 22: 00 - 00: 00|
|3|1/2/2015 0:54|2. ledna 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|2. ledna 2015 12 AM - 2 AM|
|5|1/2/2015 1:54|2. ledna 2015 12 AM - 2 AM|
|6|1/2/2015 2:54|2. ledna 2015 2 AM - 4 AM|
|7|1/2/2015 3:54|2. ledna 2015 2 AM - 4 AM|
|8|1/2/2015 4:00|2. ledna 2015 4: 00 - 6: 00|
|9|1/2/2015 4:54|2. ledna 2015 4: 00 - 6: 00|

Všechno, co vypadá dobře, ale můžete si všimnout, že je přesně jsme chtěli. Budete muset oddělit datum a čas pomocí "|" generovat správný formát.

Který chcete vyřešit, můžete přidat další příklad. Tentokrát, namísto předání za sebou z verze preview, vytvořit slovník název sloupce na hodnotu `source_data` parametru.

```
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```
||DATE (Datum)|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Žádný|
|1|1/1/2015 23:54|Žádný|
|2|1/1/2015 23:59|Žádný|
|3|1/2/2015 0:54|Žádný|
|4|1/2/2015 1:00|Žádný|
|5|1/2/2015 1:54|Žádný|
|6|1/2/2015 2:54|Žádný|
|7|1/2/2015 3:54|Žádný|
|8|1/2/2015 4:00|Žádný|
|9|1/2/2015 4:54|Žádný|

To jasně mělo negativní důsledky, jsou nyní pouze řádky, ve kterých některé hodnoty v odvozených sloupců, které přesně odpovídají příklady, které jsme zadali.

Podívejme se na příklady:
```
examples = builder.list_examples()
examples
```

| |DATE (Datum)|Příklad|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|1. ledna 2015 12 AM - 2 AM|-1|
|1|1/2/2015 0:54|2. ledna 2015 12 AM - 2 AM|-2|
|2|29/1/2015 20:54|29. ledna 2015 20: 00 – 22: 00|-3|
|3|11/11/2015 0:54|11. listopadu 2015 \| 12 AM - 2 AM|-4|

Uvidíte, že jsme zadali nekonzistentní příklady. Pokud chcete problém vyřešit, musíme nahraďte první tři příklady správné (včetně "|" mezi data a času).

Můžeme dosáhnout odstraněním příklady, které jsou nesprávné (buď předáním `example_row` z pandas DataFrame nebo předáním `example_id` hodnota) a přidání nových upravovat příklady zpět.

```
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE (Datum) | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | 1. ledna 2015 \| 12 AM - 2 AM |
| 1 | 1/1/2015 1:00 | 1. ledna 2015 \| 12 AM - 2 AM |
| 2 | 1/1/2015 1:54 | 1. ledna 2015 \| 12 AM - 2 AM |
| 3 | 1/1/2015 2:54 | 1. ledna 2015 \| 2 AM - 4 AM |
| 4 | 1/1/2015 3:54 | 1. ledna 2015 \| 2 AM - 4 AM |
| 5 | 1/1/2015 4:00 | 1. ledna 2015 \| 4: 00 - 6: 00|
| 6 | 1/1/2015 4:54 | 1. ledna 2015 \| 4: 00 - 6: 00|
| 7 | 1/1/2015 5:54 | 1. ledna 2015 \| 4: 00 - 6: 00|
| 8 | 1/1/2015 6:54 | 1. ledna 2015 \| 6: 00 - 8: 00|
| 9 | 1/1/2015 7:00 | 1. ledna 2015 \| 6: 00 - 8: 00|

Nyní správná data a nakonec jsme volat `to_dataflow()` na tvůrce, který vrátí toku dat s požadovanou odvozených sloupců, které jsou přidány.

```
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
df
```

## <a name="filtering"></a>Filtrování

Sada SDK zahrnuje metody `Dataflow.drop_columns` a `Dataflow.filter` umožňuje odfiltrovat sloupců nebo řádků.

### <a name="initial-setup"></a>Počáteční nastavení
```
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

Chcete-li filtrovat sloupce, použijte `Dataflow.drop_columns`. Tato metoda přebírá seznam sloupců, vyřaďte nebo ji volat složitější argument `ColumnSelector`.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrování sloupce s seznamu řetězců

V tomto příkladu `drop_columns` přebírá seznam řetězců. Každý řetězec by měl přesně odpovídat na požadovaný sloupec vyřadit.

``` 
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21,25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>Filtrování sloupce s použitím regex
Alternativně můžete použít `ColumnSelector` výraz vyřazení sloupce, které odpovídají regulárnímu výrazu. V tomto příkladu jsme vyřadit všechny sloupce, které odpovídají výrazu `Column*|.*longitude|.*latitude`.

```
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|Žádný|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21,25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>Filtrování řádků

Chcete-li filtrovat řádky, použijte `DataFlow.filter`. Tato metoda přebírá sady SDK služby Azure Machine Learning Data Prep výraz jako argument a vrací nový tok dat s řádky, které se výraz vyhodnotí jako True. Výrazy se vytvářejí pomocí Tvůrce výrazů (`col`, `f_not`, `f_and`, `f_or`) a pravidelné operátory (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrování řádků pomocí jednoduchých výrazů

Tvůrce výrazů `col`, zadejte název sloupce jako argument řetězec `col('column_name')` a v kombinaci s jedním z následujících standardní operátory >, <>, =, < =, ==,! =, například pokud chcete výraz sestavit `col('Tip_amount') > 0`. A konečně, předejte sestavené výraz do `Dataflow.filter` funkce.

V tomto příkladu `dataflow.filter(col('Tip_amount') > 0)` vrátí nového toku dat s řádky, ve kterém hodnotu `Tip_amount` je větší než 0.

> [!NOTE] 
> `Tip_amount` nejprve převeden na číselnou hodnotu, která umožňuje vytvářet výrazu porovnání oproti dalších číselných hodnot.

```
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0,08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1,05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrování řádků s složité výrazy

Filtrovat pomocí složité výrazy, jeden nebo více jednoduché výrazy s tvůrci výrazu kombinovat `f_not`, `f_and`, nebo `f_or`.

V tomto příkladu `Dataflow.filter` vrátí nového toku dat s řádky kde `'Passenger_count'` je menší než 5 a `'Tolls_amount'` je větší než 0.

```
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```
||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9,57|7.47|5.33|44,8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

Je také možné filtrovat řádky kombinování více než jeden Tvůrce výrazů a vytvořit vnořené výraz.

> [!NOTE]
> `lpep_pickup_datetime` a `Lpep_dropoff_datetime` jsou nejprve převeden na typ datetime, která umožňuje vytvářet výrazu porovnání oproti jiné hodnoty data a času.

```
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
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9,57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52 + 00:00|2013-08-25 09:34:34 + 00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51 + 00:00|2013-08-25 17:13:55 + 00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11 + 00:00|2013-08-25 18:02:57 + 00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>Vlastních transformací Pythonu 

Budou existovat scénáře při nejjednodušší věc, kterou musíte udělat, je napsat kód Pythonu. Sada SDK poskytuje tři Rozšiřovací body, které můžete použít.

- Nový sloupec skriptu
- Nový filtr skriptu
- Transformace oddílu

Každé rozšíření je podporována v běhu vertikálně navýšit kapacitu a škálování na víc systémů. Hlavní výhodou používání těchto Rozšiřovací body je, že není nutné vyžádat všechna data, aby bylo možné vytvořit objekt frame, data. Vaše vlastní Python, který kód se spustí stejně, jako jsou dalších transformací ve velkém měřítku, oddílem a obvykle paralelně.

### <a name="initial-data-preparation"></a>Počáteční data přípravy

Začněte tím, že načítání nějaká data z objektů Blob v Azure.

```
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale kraj|10171002158| |
|1|ALABAMA|1|101710|Hale kraj|10171002162| |
|2|ALABAMA|1|101710|Hale kraj|10171002156| |
|3|ALABAMA|1|101710|Hale kraj|10171000588|2|
|4|ALABAMA|1|101710|Hale kraj|10171000589| |

Trim dolů datové sady a provádět některé základní transformací.

```
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|1|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|2|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|3|ALABAMA|Hale kraj|1.017100e + 10|2|
|4|ALABAMA|Hale kraj|1.017100e + 10|Žádný|

Vyhledejte pomocí filtru hodnot null. Se tady nějaké, takže teď vyplňte tyto chybějící hodnoty.

```
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|1|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|2|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|3|ALABAMA|Hale kraj|1.017100e + 10|Žádný|
|4|ALABAMA|Hale kraj|1.017100e + 10|Žádný|

### <a name="transform-partition"></a>Transformace oddílu

Funkce po ruce pandas můžete nahradit všechny hodnoty null na 0. Tento kód spustí oddílu, nikoli na všechny datové sady v čase. To znamená, že na velkou datovou sadu, tento kód může spouštět paralelně jak modul runtime zpracovává data, oddílu pomocí oddílů.

```
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
h = df.head(5)
h
```
||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|1.017100e + 10|0.0|
|1|ALABAMA|Hale kraj|1.017100e + 10|0.0|
|2|ALABAMA|Hale kraj|1.017100e + 10|0.0|
|3|ALABAMA|Hale kraj|1.017100e + 10|2.0|
|4|ALABAMA|Hale kraj|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Nový sloupec skriptu

Vytvoří nový sloupec, který má název kraje a název stavu a také velké první písmeno názvu stavu, můžete použít kód v Pythonu. Chcete-li to provést, použijte `new_script_column()` metodu na datový tok.

```
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
h = df.head(5)
h
```
||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|
|2|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|
|3|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|2.0|
|4|ALABAMA|Hale kraj|Kraj hale, Alabama|1.017100e + 10|0.0|
### <a name="new-script-filter"></a>Nový filtr skriptu

Nyní, sestavit Python výraz k filtrování datovou sadu, která pouze řádky, kde "Hale" není na novém `county_state` sloupce. Výraz vrací `True` Pokud my chceme zajistit řádku a `False` vyřadit řádku.

```
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
h = df.head(5)
h
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|0.0|
|2|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|0.0|
|3|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|0.0|
|4|ALABAMA|Jefferson kraj|Kraj Jefferson, Alabama|1.019200e + 10|0.0|
