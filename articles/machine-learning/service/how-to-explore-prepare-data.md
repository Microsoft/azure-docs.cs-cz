---
title: Prozkoumat a transformovat data (třída DataSet)
titleSuffix: Azure Machine Learning service
description: Zkoumání dat pomocí souhrnných statistik a Příprava dat pomocí nástroje pro čištění a transformaci dat a pro práci s funkcemi
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: dbdd088e5a78a4f78eec27b5ee74856c6aecc209
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847910"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Zkoumání a Příprava dat pomocí třídy DataSet (Preview)

Naučte se zkoumat a připravovat data pomocí balíčku AzureML-DataSets v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Třída [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) (Preview) umožňuje prozkoumat a připravit data poskytnutím funkcí, jako jsou vzorkování, souhrnné statistiky a inteligentní transformace. Kroky transformace jsou uloženy v [definicích datových sad](how-to-manage-dataset-definitions.md) s možností zpracování více velkých souborů různých schémat vysoce škálovatelným způsobem.

> [!Important]
> Některé třídy DataSet (Preview) mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). I když lze transformační funkce provést přímo pomocí funkcí přípravy GA'ed data, doporučujeme, abyste balíčky [datových](how-to-transform-data.md)sad popsaných v tomto článku popsali, pokud vytváříte nové řešení. Azure Machine Learning datové sady (Preview) umožňují nejen transformovat data, ale také [data snímku](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py) a ukládat [Definice datových sad s verzemi](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py). Datové sady jsou další verzí sady SDK pro přípravu dat, která nabízí rozšířené funkce pro správu datových sad v řešeních AI.

## <a name="prerequisites"></a>Požadavky

K prozkoumávání a přípravě dat budete potřebovat:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru služby Azure Machine Learning](how-to-manage-workspace.md).

* Sada SDK Azure Machine Learning pro Python (verze 1.0.21 nebo novější), která obsahuje balíček AzureML-DataSets. Chcete-li nainstalovat nebo aktualizovat nejnovější verzi sady SDK, přečtěte si téma [instalace nebo aktualizace sady SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Sada Azure Machine Learning data PREP SDK. Informace o instalaci nebo aktualizaci na nejnovější verzi najdete v tématu [instalace nebo aktualizace sady SDK pro přípravu dat](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).

* Stáhněte si ukázkové soubory a sledujte je spolu s příklady: [zločin. csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) a [City. JSON](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Vzorkování

Pořiďte si ukázku vašich dat a získejte počáteční znalosti vaší datové architektury a obsahu. V tuto chvíli [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-) metoda z třídy DataSet podporuje strategie prvních N, jednoduchých náhodných a Stratified vzorkování.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Ukázka TOP N

V případě vzorkování TOP N je prvních n záznamů vaší datové sady ukázka. To je užitečné, pokud se právě snažíte získat představu o tom, co vaše záznamy dat vypadají, nebo jak zobrazit pole, která jsou ve vašich datech.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||id|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|KLAMAVÉ POSTUPY|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|KRÁDEŽ|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|KLAMAVÉ POSTUPY|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|KLAMAVÉ POSTUPY|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...

### <a name="simple-random-sample"></a>Jednoduchá náhodná ukázka

V jednoduchém náhodném vzorkování má každý člen populace dat stejnou pravděpodobnost výběru jako součást vzorku. `simple_random` V ukázkové strategii jsou záznamy z datové sady vybrány na základě zadané pravděpodobnosti a vrátí upravenou datovou sadu. Parametr počáteční hodnoty je nepovinný.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||id|Číslo případu|Datum|Zablokovat|IUCR|Primární typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|KRÁDEŽ|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|KLAMAVÉ POSTUPY|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...

### <a name="stratified-sample"></a>Ukázka Stratified

Ukázky Stratified zajišťují, že v ukázce jsou zastoupeny určité skupiny obyvatel. Ve vzorové strategii je populace rozdělená do vrstev nebo podskupin na základě podobností a záznamy se náhodně vyberou z jednotlivých vrstev podle váhy vrstev, které jsou uvedené v `fractions` parametru. `stratified`

V následujícím příkladu seskupme jednotlivé záznamy podle zadaných sloupců a zahrneme uvedený záznam na základě informací o váhy X vrstev v `fractions`. Pokud se nezadá vrstvy nebo záznam nejde seskupit, výchozí tloušťka vzorku je 0.

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
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|KRÁDEŽ|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|KRÁDEŽ|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|KRÁDEŽ|...

## <a name="explore-with-summary-statistics"></a>Prozkoumat se souhrnnou statistikou

 Pomocí [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) metody zjistíte anomálie, chybějící hodnoty nebo počty chyb. Tato funkce získá profil a souhrnnou statistiku vašich dat, která zase pomáhá určit nezbytné operace přípravy dat, které se mají použít.

```Python
dataset.get_profile()
```

||type|Minimum|Maximum|Počet|Chybí počet|Nechybí počet|Procento chybějících|Počet chyb|Prázdný počet|0,1 % Quantile|1 % Quantile|5 % Quantile|25 % Quantile|50 % Quantile|75 % Quantile|95 % Quantile|99 % Quantile|99,9 % Quantile|střední hodnotu|Směrodatná odchylka|Odchylka|Zešikmení|Míra fluktuace
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Číslo případu|FieldType.STRING|HZ239907|HZ278872|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Date|FieldType. DATE|2016-04-04 23:56:00 + 00:00|2016-04-15 17:00:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zablokovat|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType. INTEGER|810|1154|10.0|0.0|10.0|0.0|0.0|0.0|810|850|810|890|1136|1153|1154|1154|1154|1058,5|137,285|18847,2|-0,785501|-1,3543
Primární typ|FieldType.STRING|KLAMAVÉ POSTUPY|KRÁDEŽ|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis|FieldType.STRING|NEFALEŠNÁ KONTROLU|NAD $500|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Popis umístění|FieldType.STRING||ŠKOLA, VEŘEJNÁ A BUDOVA|10.0|0.0|10.0|0.0|0.0|1.0||||||||||||||
Zadržení|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Domácí|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Signál|FieldType. INTEGER|531|2433|10.0|0.0|10.0|0.0|0.0|0.0|531|531|531|614|1318,5|1911|2433|2433|2433|1371,1|692,094|478994|0,105418|-1,60684
Okres|FieldType. INTEGER|5|24|10.0|0.0|10.0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13.5|6,94822|48,2778|0,0930109|-1,62325
Dál|FieldType. INTEGER|1|48|10.0|0.0|10.0|0.0|0.0|0.0|1|5|1|9|22.5|40|48|48|48|24.5|16,2635|264,5|0,173723|-1,51271
Oblast komunity|FieldType. INTEGER|4|77|10.0|0.0|10.0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41,2|26,6366|709,511|0,112157|-1,73379
Úřadu FBI kódu|FieldType. INTEGER|6|11|10.0|0.0|10.0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2,36643|5.6|-0,702685|-1,59582
Souřadnici x|FieldType. INTEGER|1.16309 e + 06|1.18336 e + 06|10.0|7.0|3.0|0,7|0.0|0.0|1.16309 e + 06|1.16309 e + 06|1.16309 e + 06|1.16401 e + 06|1.16678 e + 06|1.17921 e + 06|1.18336 e + 06|1.18336 e + 06|1.18336 e + 06|1.17108 e + 06|10793,5|1.165 e + 08|0,335126|-2,33333
Souřadnice Y|FieldType. INTEGER|1.8315 e + 06|1.908 e + 06|10.0|7.0|3.0|0,7|0.0|0.0|1.8315 e + 06|1.8315 e + 06|1.8315 e + 06|1.83614 e + 06|1.85005 e + 06|1.89352 e + 06|1.908 e + 06|1.908 e + 06|1.908 e + 06|1.86319 e + 06|39905,2|1.59243 e + 09|0,293465|-2,33333
Rok|FieldType. INTEGER|2016|2016|10.0|0.0|10.0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aktualizace|FieldType. DATE|2016-05-11 15:48:00 + 00:00|2016-05-27 15:45:00 + 00:00|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zeměpisná šířka|FieldType.DECIMAL|41,6928|41,9032|10.0|7.0|3.0|0,7|0.0|0.0|41,6928|41,6928|41,6928|41,7057|41,7441|41,8634|41,9032|41,9032|41,9032|41,78|0,109695|0,012033|0,292478|-2,33333
Zeměpisná délka|FieldType.DECIMAL|-87,6764|-87,6043|10.0|7.0|3.0|0,7|0.0|0.0|-87,6764|-87,6764|-87,6764|-87,6734|-87,6645|-87,6194|-87,6043|-87,6043|-87,6043|-87,6484|0,0386264|0,001492|0,344429|-2,33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10.0|0.0|10.0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Dává chybějící hodnoty

V datových sadách hodnoty null, NaN a hodnoty, které neobsahují žádný obsah, se považují za chybějící hodnoty. To může mít vliv na výkon vašich modelů strojového učení nebo vést k neplatným závěrům. Imputac je jeden společný přístup k adresování chybějících hodnot a je užitečný, když máte vysoké procento chybějících záznamů hodnot, které nemůžete jednoduše odstranit.

Z profilu datové sady generovaného v předchozí části uvidíme, že `Latitude` a `Longitude` sloupce mají vysoké procento chybějících hodnot. V tomto příkladu vypočítáme pro tyto dva sloupce střední hodnotu a hodnoty imputace.

Nejdřív Získejte nejnovější definici datové sady s [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow)daty a zredukovali je, takže zobrazíme jenom sloupce, které chceme adresovat.

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
|0|10498554|False|41,692834|-87,604319|
|1|10516598|False| 41,744107 |-87,664494|
|2|10519196|False| NaN|NaN|

Potom ve sloupci Zeměpisná šířka `MEAN` [`summarize()`](/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-ml-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow--summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow) pomocí funkce ověřte hodnotu. Tato funkce přijímá pole sloupců `group_by_columns` parametr k určení úrovně agregace. Parametr akceptuje funkci, která určuje aktuální název sloupce, nový název počítaného `SummaryFunction` pole a provede se. `SummaryColumnsValue` `summary_columns`

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
|0|False|41,780049|

Jakmile ověříte hodnoty, které se mají použít, použijte [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py) k získání informací o výrazu s pevnou hodnotou, která tyto sloupce `CUSTOM` vyhodnotí `MAX`, `MEAN` a to pomocí počítané `MIN`,, hodnoty nebo hodnoty. Když `group_by_columns` není zadána, chybějící hodnoty budou uložené ve skupině s `MIN`, `MAX`, a `MEAN` počítá na skupinu.

Přijímá column_id řetězec `ReplaceValueFunction` a k určení typu imputac. [`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) U chybějících zeměpisných hodnot ji imputace s-87 na základě externích znalostí.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Kroky imputace lze zřetězit do `ImputeMissingValuesBuilder` objektu [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py) pomocí funkce [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder)třídy. `impute_columns` Parametr přijímá pole `ImputeColumnArguments` objekty.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Zavolejte funkci pro uložení kroků imputac a použijte je pro objekt toku dat pomocí [`to_dataflow()`.](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow) [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none)

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Jak je znázorněno v následující výstupní tabulce, chybějící Zeměpisná šířka byla imputované s `MEAN` `Arrest==False` hodnotou skupiny a chybějící Zeměpisná délka byla imputované s-87.

||id|Zadržení|Zeměpisná šířka|Zeměpisná délka
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

Aktualizujte definici datové sady s [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-) , aby bylo možné zachovat provedené kroky transformace.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||id|Zadržení|Zeměpisná šířka|Zeměpisná délka
-|---------|-----|---------|----------
0|10498554|False|41,692834|-87,604319
1|10516598|False|41,744107|-87,664494
2|10519196|False|41,780049|-87,000000

## <a name="create-assertion-rules"></a>Vytvořit pravidla kontrolního výrazu

Často platí, že data, se kterými pracujete, zatímco čištění a Příprava dat, jsou jenom podmnožinou celkových dat, která potřebujeme pro produkční prostředí. V důsledku toho mohou být některé z předpokladů, které jsme provedli v rámci našeho čištění, nepravdivé. Například v sadě dat, která se aktualizuje průběžně, může být sloupec, který původně obsahoval pouze čísla v rámci určitého rozsahu, obsahovat širší rozsah hodnot v pozdějším spuštění. Tyto chyby často způsobují buď přerušené kanály, nebo nesprávná data.

Datové sady podporují vytváření kontrolních výrazů pro data, která jsou vyhodnocována při spuštění kanálu. Tyto kontrolní výrazy nám umožňují ověřit, že naše předpoklady na datech jsou i nadále přesné a v případě potřeby nemusejí zpracovávat chyby odpovídajícím způsobem.

Například pokud chcete omezit `Latitude` `Longitude` hodnoty ve vaší datové sadě na konkrétní číselné rozsahy, metoda zajistí, že [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) se vždy jedná o tento případ.

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

||type|Minimum|Maximum|Počet|Chybí počet|Nechybí počet|Procento chybějících|Počet chyb|Prázdný počet|0,1 % Quantile|1 % Quantile|5 % Quantile|25 % Quantile|50 % Quantile|75 % Quantile|95 % Quantile|99 % Quantile|99,9 % Quantile|střední hodnotu|Směrodatná odchylka|Odchylka|Zešikmení|Míra fluktuace
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
id|FieldType. INTEGER|1.04986 e + 07|1.05351 e + 07|10.0|0.0|10.0|0.0|0.0|0.0|1.04986 e + 07|1.04992 e + 07|1.04986 e + 07|1.05166 e + 07|1.05209 e + 07|1.05259 e + 07|1.05351 e + 07|1.05351 e + 07|1.05351 e + 07|1.05195 e + 07|12302,7|1.51358 e + 08|-0,495701|-1,02814
Zadržení|FieldType. BOOLEAN|False|False|10.0|0.0|10.0|0.0|0.0|0.0||||||||||||||
Zeměpisná šířka|FieldType.DECIMAL|41,6928|41,9032|10.0|0.0|10.0|0.0|0.0|0.0|41,6928|41,7185|41,6928|41,78|41,78|41,78|41,9032|41,9032|41,9032|41,78|0,0517107|0,002674|0,837593|1,05
Zeměpisná délka|FieldType. INTEGER|-87|-87|10.0|0.0|10.0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Z profilu vidíte, že `Error Count` `Longitude` sloupec má hodnotu 3. Následující kód filtruje datovou sadu, načte chybu a zjistí, která hodnota způsobí selhání kontrolního výrazu. Z tohoto místa upravte svůj kód a patřičně vyčistěte data.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Odvodit sloupce podle příkladu

Jedním z pokročilejších nástrojů pro datové sady je schopnost odvodit sloupce pomocí příkladů požadovaných výsledků. To vám umožní poskytnout sadě SDK příklad, takže může vygenerovat kód pro dosažení zamýšlené transformace.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||id|Číslo případu|Datum|Zablokovat|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Řekněme, že je nutné transformovat formát data a času na hodnotu "2016-04-04 10PM-12AM". V argumentu zadejte příklady požadovaného výstupu `example_data` v parametru v tomto formátu: *(původní výstup, požadovaný výstup)* . [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow)

Následující kód poskytuje dva příklady požadovaného výstupu ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") a ("2016-04-15 17:00:00", "2016-04-15 16:00-18:00")

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

V následující tabulce si všimněte, že nový sloupec Date_Time_Range obsahuje záznamy v zadaném formátu.

||id|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 16:00-18:00
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Fuzzy seskupení

Když shromažďujete data z různých zdrojů, můžete zaznamenat variace při pravopisu, velkých písmenech nebo zkratkách stejných entit. Automaticky standardizovat a sjednotit tyto varianty pomocí seskupení nepřibližné nebo textové clusteringu, funkčnosti sady SDK.

Sloupec `inspections.business.city` například obsahuje několik forem názvu města "San Francisco".

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|prohlídky. business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Rychlé – N – Ezee indických příkrmů|3861 24 července St|SF|...
1|67565|Král thajského Noodlesu Cafe|1541 TARAVAL St|FRANCISCO SAN|...
2|67565|Král thajského Noodlesu Cafe|1541 TARAVAL St|FRANCISCO SAN|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier stravovací & události, Inc.|1255 22 St|S.F.|...

Pojďme použít [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow) metodu k přidání sloupce s automaticky zjištěným kanonickým tvarem "San Francisco". `source_column` Argumenty`new_column_name` jsou povinné. Máte také možnost:

* Vytvoří nový sloupec `similarity_score_column_name`, který zobrazuje skóre podobnosti mezi všemi páry původních a kanonických hodnot.

* `similarity_threshold`Zadejte, což je minimální skóre podobnosti pro hodnoty, které mají být seskupeny dohromady.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|prohlídky. business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Rychlé – N – Ezee indických příkrmů|3861 24 července St|SF|San Francisco|0,814806|...
1|67565|Král thajského Noodlesu Cafe|1541 TARAVAL St|FRANCISCO SAN|San Francisco|1,000000|...
2|67565|Král thajského Noodlesu Cafe|1541 TARAVAL St|FRANCISCO SAN|San Francisco|1,000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0,814806|...
4|69186|Premier stravovací & události, Inc.|1255 22 St|S.F.|San Francisco|0,814806|...

V definici výsledné datové sady jsou všechny různé variace představující "San Francisco" v datech normalizovány na stejný řetězec "San Francisco".

Uložte tento krok přibližného seskupení do nejnovější definice datové sady pomocí `update_definition()`.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Další postup

* Příklad regresního modelu najdete v [kurzu](tutorial-auto-train-models.md) automatizovaného strojového učení.

* Vzory návrhu a příklady použití najdete v [přehledu](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) sady SDK.

* Příklad použití datových sad najdete v ukázkových poznámkových [blocích](https://aka.ms/dataset-tutorial).
