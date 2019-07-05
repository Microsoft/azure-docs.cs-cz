---
title: Prozkoumejte a transformace dat (třídu datové sady)
titleSuffix: Azure Machine Learning service
description: Zkoumání dat pomocí souhrnnou statistiku a připravit data prostřednictvím čištění dat, transformaci a vytváření funkcí
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: 80137c7f1ecebab4d2da0c4b7ba0ca9292dad22e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443973"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Prozkoumejte a příprava dat pomocí třídy datové sady (Preview)

Zjistěte, jak zkoumat a příprava dat pomocí Azure ml datových sad balíčku v [SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). [Datovou sadu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) třídy (preview) umožňuje prozkoumat a připravit data tím, že poskytuje funkce jako například: vzorkování, souhrnné statistiky a inteligentní transformace. Kroky transformace jsou uloženy v [definicích datových sad](how-to-manage-dataset-definitions.md) s vysoce škálovatelnou způsobem zpracovávat několik velkých souborů, různých schémat.

> [!Important]
> Některé třídy datové sady (preview) mají závislosti [azureml přípravy](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) balíčku (GA). Při transformaci funkcí lze provést přímo s GA'ed [Data Prep funkce](how-to-transform-data.md), doporučujeme obálky balíček datové sady jsou popsané v tomto článku, pokud vytváříte nové řešení. Sady Azure Machine Learning dat (preview) umožňuje nejen transformace dat, ale také [dat snímků](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) a ukládat [definice verzí datové sady](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Datové sady je další verze sady SDK přípravy dat nabídky Rozšířené funkce pro správu datových sad v řešení AI.

## <a name="prerequisites"></a>Požadavky

K prozkoumání a připravit data, budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní prostor služby Azure Machine Learning. Zobrazit [vytvořit pracovní prostor služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Sady SDK Azure Machine Learning pro Python (verze 1.0.21 nebo novější), která obsahuje balíček azureml datové sady. Pro instalaci nebo aktualizaci na nejnovější verzi sady SDK, naleznete v tématu [nainstalovat nebo aktualizovat sadu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Azure Machine Learning přípravy dat sady SDK. Pro instalaci nebo aktualizaci na nejnovější verzi, najdete v článku [nainstalovat nebo aktualizovat sadu SDK pro Data Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install).

* Stažení ukázkových souborů chcete postupovat podle příkladů: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) a [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Vzorkování

Využijte ukázku vaše data, abyste získali počáteční pochopení architektury dat a obsahu. V tuto chvíli [ `sample()` ](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metoda ze třídy Dataset podporuje strategie Top N, jednoduchý náhodné a Stratified vzorkování.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Ukázka horních N

Pro nejlepší vzorkování prvních n záznamů datové sady jsou ukázky. To je užitečné, pokud se právě pokoušíte získáte představu o jaká data záznamů vzhledu jako nebo pole jsou ve vašich datech.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|PODVODNÝ POSTUPEM|...
1|10516598|HZ258664|4/15/2016 17:00|082XX LOŽIT MARSHFIELD|890|KRÁDEŽ|...
2|10519196|HZ261252|4/15/2016 10:00|104XX LOŽIT SACRAMENTO|1154|PODVODNÝ POSTUPEM|...
3|10519591|HZ261534|4/15/2016 9:00|113XX LOŽIT PSOUNŮ|1120|PODVODNÝ POSTUPEM|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...

### <a name="simple-random-sample"></a>Jednoduché náhodného vzorku

Každý člen zaplňování daty v jednoduché náhodných vzorkování, má stejnou šanci výběru jako součást vzorku. V `simple_random` strategie vzorku záznamů z datové sady. jsou určí pravděpodobnost zadán a vrátí upravenou datové sady. Je volitelný parametr počáteční hodnoty.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX LOŽIT MARSHFIELD|890|KRÁDEŽ|...
1|10519196|HZ261252|4/15/2016 10:00|104XX LOŽIT SACRAMENTO|1154|PODVODNÝ POSTUPEM|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...

### <a name="stratified-sample"></a>Vrstveného vzorku

Ukázky vrstveného Ujistěte se, že určité skupiny naplnění jsou reprezentovány v ukázce. V `stratified` strategie vzorku obyvatelstva je rozdělen na setkání strata nebo podskupiny, na základě a záznamy jsou náhodně vybrány z jednotlivých vrstev podle váhy setkání strata indikován `fractions` parametru.

V následujícím příkladu, jsme jednotlivé záznamy seskupit podle zadaných sloupců a zahrnují uvedené záznam na základě informací o setkání strata X váha v `fractions`. Pokud není zadán setkání strata nebo záznam nelze seskupit, výchozí tloušťka ukázku je 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||id|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX LOŽIT MARSHFIELD|890|KRÁDEŽ|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...
2|10535059|HZ278872|4/15/2016 4:30|004XX LOŽIT KILBOURN|810|KRÁDEŽ|...

## <a name="explore-with-summary-statistics"></a>Prozkoumat s souhrnné statistiky

 Detekovat anomálie, chybějící hodnoty, nebo se vrátí chyba [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metody. Tato funkce získá profil a souhrnné statistiky svá data, která zase pomáhá určit operace přípravy potřebná data použít.

```Python
dataset.get_profile()
```

||Type|Minimum|Maximum|Počet|Chybí počet|Nechybí počet|Procento chybějících|Počet chyb|Prázdný počet|0,1 % Quantile|1 % Quantile|5 % Quantile|25 % Quantile|50 % Quantile|75 % Quantile|95 % Quantile|99 % Quantile|99,9 % Quantile|střední hodnotu|Směrodatná odchylka|Odchylka|Zešikmení|Míra fluktuace
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Číslo případu|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Datum|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zablokovat|FieldType.STRING|004XX LOŽIT KILBOURN|113XX LOŽIT PSOUNŮ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primární typ|FieldType.STRING|PODVODNÝ POSTUPEM|KRÁDEŽ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis|FieldType.STRING|NEPLATNÉ KONTROLY|NAD 500 USD|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis umístění|FieldType.STRING||ŠKOLA, VEŘEJNÁ, VYTVÁŘENÍ|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Zadržení|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domácí|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Signál|FieldType.INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6.94822|48.2778|0.0930109|-1.62325
Dál|FieldType.INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16.2635|264.5|0.173723|-1.51271
Oblast komunity|FieldType.INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37.5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
Úřadu FBI kódu|FieldType.INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
Souřadnici x|FieldType.INTEGER|1.16309e + 06|1.18336e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.16309e + 06|1.16309e + 06|1.16309e + 06|1.16401e + 06|1.16678e + 06|1.17921e + 06|1.18336e + 06|1.18336e + 06|1.18336e + 06|1.17108e + 06|10793.5|1.165e + 08|0.335126|-2.33333
Souřadnice Y|FieldType.INTEGER|1.8315e + 06|1.908e + 06|10.0|7.0|3.0|0.7|0.0|0.0|1.8315e + 06|1.8315e + 06|1.8315e + 06|1.83614e + 06|1.85005e + 06|1.89352e + 06|1.908e + 06|1.908e + 06|1.908e + 06|1.86319e + 06|39905.2|1.59243e + 09|0.293465|-2.33333
Rok|FieldType.INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aktualizace|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zeměpisná šířka|FieldType.DECIMAL|41.6928|41.9032|10.0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Zeměpisná délka|FieldType.DECIMAL|-87.6764|-87.6043|10.0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Dává chybějící hodnoty

V datových sadách, hodnoty null, od NaN a hodnoty, které obsahují žádný obsah se považují za chybějící hodnoty. Tyto dopad na výkon vašich modelů machine learning nebo vést k neplatné závěry. Imputace je jeden běžný postup k vyřešení chybějících hodnot a je užitečné, když máte vysoký podíl chybí hodnota záznamy, které nelze jednoduše odstranit.

Z datové sady profilu generovány v předchozí části, uvidíme `Latitude` a `Longitude` sloupce mají vysoké procento chybějících hodnot. V tomto příkladu jsme vypočítat průměr a dává chybějící hodnoty pro tyto dva sloupce.

Nejprve získejte nejnovější definice datové sady s [ `get_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) a Zredukovat data s využitím [ `keep_columns()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), takže můžeme zobrazit jenom sloupce chceme, aby na adresu.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||id|Zadržení| Zeměpisná šířka|Zeměpisná délka|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Dále zkontrolujte `MEAN` hodnotu pomocí zeměpisné šířky sloupce [ `summarize()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) funkce. Tato funkce přijímá pole sloupců `group_by_columns` parametr k určení úrovně agregace. `summary_columns` Parametr přijímá `SummaryColumnsValue` funkce, která určuje aktuální název sloupce, nový název počítaného pole a `SummaryFunction` provádět.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Zadržení|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

Jakmile ověříme hodnoty tak, aby dává, použijte [ `ImputeMissingValuesBuilder` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) další pevné výraz, který uplatňuje sloupce s buď výpočtová `MIN`, `MAX`, `MEAN` hodnotu, nebo `CUSTOM` hodnotu. Když `group_by_columns` není zadána, chybějící hodnoty budou uložené ve skupině s `MIN`, `MAX`, a `MEAN` počítá na skupinu.

[ `ImputeColumnArguments` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) Přijímá řetězec column_id a `ReplaceValueFunction` k určení typu impute. Pro chybějící hodnoty zeměpisné délky dává s-87 na základě externí znalostí.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Dává kroky je možné zřetězit do `ImputeMissingValuesBuilder` pomocí [ `Builders` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) třídy funkce [ `impute_missing_values()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder). `impute_columns` Parametr přijímá pole `ImputeColumnArguments` objekty.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Volání [ `learn()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) funkci pro uložení impute kroky a použít je u objektu toku dat pomocí [ `to_dataflow()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow).

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Jak je znázorněno v následující tabulce výstup, chybějící šířky byl splněn s `MEAN` hodnotu `Arrest==False` s-87 byl splněn skupiny a chybějící délky.

||id|Zadržení|Zeměpisná šířka|Zeměpisná délka
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Aktualizovat definici datové sady, [ `update_definition()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) zachovat kroky prováděné transformace.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Zadržení|Zeměpisná šířka|Zeměpisná délka
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Vytvořit kontrolní výraz pravidla

Často data spolupracujeme se při čištění a příprava dat je pouze podmnožinu celkové množství dat, kterou potřebujeme pro produkční prostředí. V důsledku toho některé předpoklady, které můžeme udělat v rámci naší čištění může třeba vyjít najevo jako NEPRAVDA. Například v sadě dat, která průběžně aktualizuje, může obsahovat sloupec, který původně obsahoval pouze čísla v určitém rozsahu širší rozsah hodnot v pozdější spuštění. Tyto chyby často způsobit přerušení kanály nebo chybnými daty.

Vytváří se kontrolní výrazy s daty, které jsou vyhodnocovány, jak se kanál spustí podpora datové sady. Tyto kontrolní výrazy nám ověřte, že naše předpoklady na data i nadále být přesné a pokud ne, řešit selhání odpovídajícím způsobem.

Například, pokud chcete omezit `Latitude` a `Longitude` hodnoty ve vaší datové sadě konkrétní číselných rozsahů [ `assert_value()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) metoda zajišťuje, že tomu tak vždycky.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Minimum|Maximum|Počet|Chybí počet|Nechybí počet|Procento chybějících|Počet chyb|Prázdný počet|0,1 % Quantile|1 % Quantile|5 % Quantile|25 % Quantile|50 % Quantile|75 % Quantile|95 % Quantile|99 % Quantile|99,9 % Quantile|střední hodnotu|Směrodatná odchylka|Odchylka|Zešikmení|Míra fluktuace
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType.INTEGER|1.04986e + 07|1.05351e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986e + 07|1.04992e + 07|1.04986e + 07|1.05166e + 07|1.05209e + 07|1.05259e + 07|1.05351e + 07|1.05351e + 07|1.05351e + 07|1.05195e + 07|12302.7|1.51358e + 08|-0.495701|-1.02814
Zadržení|FieldType.BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zeměpisná šířka|FieldType.DECIMAL|41.6928|41.9032|10.0|0.0|10.0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1,05
Zeměpisná délka|FieldType.INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Z profilu, můžete vidět, že `Error Count` pro `Longitude` sloupec je 3. Následující kód filtry datovou sadu, načte chybu a vidí, jaké hodnoty způsobí, že kontrolní výraz selže. Zde upravte kód tak a odpovídajícím způsobem vyčistit data.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Odvození sloupce podle příkladu

Jeden z rozšířené nástroje pro datové sady je schopnost odvodit sloupců pomocí příklady požadovaných výsledků. Díky tomu můžete poskytnout sadu SDK příklad, tak může také generovat kód pro dosažení zamýšlený transformace.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Číslo případu|Datum|Zablokovat|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX LOŽIT MARSHFIELD|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX LOŽIT SACRAMENTO|...

Řekněme, že budete muset převést formát data a času na "2016-04-04 22: 00 - 00: 00". V [ `derive_column_by_example()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) argument, obsahují příklady požadovaného výstupu v `example_data` parametrů v tomto formátu: *(původní výstup, požadované výstupu)* .

Následující kód obsahuje dva příklady požadovaného výstupu ("2016-04-04 23:56:00", "2016-04-04 10 PM-12 AM") a ("2016-04-15 17:00:00", "2016-04-15, 16: 00 – 18: 00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

V následující tabulce Všimněte si, že nový sloupec, Date_Time_Range obsahuje záznamy ve formátu určeném.

||id|Datum|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10 PM-12 AM
1|10516598|2016-04-15 17:00:00|2016-04-15, 16: 00 – 18: 00
2|10519196|2016-04-15 10:00:00|2016-04-15 10: 00 - 12 HODIN

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Přibližné seskupení

Při shromažďování dat z různých zdrojů můžete setkat, kolísání pravopisu, malá a velká písmena nebo zkratky stejné entity. Automaticky standardizovat a zaznamenávají tyto varianty s fuzzy logikou seskupení nebo clustering textu, funkce sady SDK.

Například sloupec `inspections.business.city` obsahuje několik tvarů název města "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Rychlé N-Ezee Indická potravin|3861 24th St|SF|...
1|67565|King thajština nudle Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King thajština nudle Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 clement St|SF|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|...

Použijeme [ `fuzzy_group_column()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) způsob, jak přidat sloupec s automaticky zjištěnými kanonický tvar "San Francisco". Argumenty `source_column` a `new_column_name` jsou povinné. Máte také možnost:

* Vytvoří nový sloupec, `similarity_score_column_name`, který ukazuje skóre podobnosti mezi každou dvojici hodnot původní a canonical.

* Zadejte `similarity_threshold`, což je minimální podobnost skóre pro hodnoty, které mají být seskupeny.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Rychlé N-Ezee Indická potravin|3861 24th St|SF|San Francisco|0.814806|...
1|67565|King thajština nudle Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King thajština nudle Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 clement St|SF|San Francisco|0.814806|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

V definici výsledné datové sady byly všechny různé varianty reprezentovat "San Francisco" v datech normalizovány na stejný řetězec "San Francisco".

Uložit tento krok přibližných shod seskupení do nejnovější definice datové sady s `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Další postup

* Automatizované strojového učení najdete v článku [kurzu](tutorial-auto-train-models.md) příklad regresní model.

* Sada SDK [přehled](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vzory návrhu a příklady použití.

* Příklad použití datových sad, najdete v článku [ukázkové poznámkové bloky](https://aka.ms/dataset-tutorial).
