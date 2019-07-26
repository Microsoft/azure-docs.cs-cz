---
title: 'Zápis: Příprava dat Python SDK'
titleSuffix: Azure Machine Learning service
description: Další informace o vytváření dat pomocí sady SDK služby Azure Machine Learning Data Prep. Můžete napsat data v libovolném bodě v toku dat a soubory v libovolné z našich podporovaných umístění (místního systému souborů Azure Blob Storage a Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6753be5613b10b64936cddaafbb9859aad837b02
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358641"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Zápis a konfigurace dat pomocí sady Azure Machine Learning data PREP SDK

V tomto článku se seznámíte s různými metodami pro zápis dat pomocí [sady Azure Machine Learning data PREP Python SDK](https://aka.ms/data-prep-sdk) a jak nakonfigurovat tato data pro experimentování s [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Výstupní data lze zapsat v jakémkoli okamžiku v toku dat. Zápisy jsou přidány jako kroky do výsledného toku dat a tyto kroky jsou spouštěny při každém spuštění toku dat. Data se zapisují do více souborů oddíl umožňuje paralelní zápisy.

> [!Important]
> Pokud vytváříte nové řešení, vyzkoušejte [Azure Machine Learning datové sady](how-to-explore-prepare-data.md) (Preview), abyste mohli transformovat data, data snímků a ukládat definice datových sad s verzemi. Datové sady jsou další verzí sady SDK pro přípravu dat, která nabízí rozšířené funkce pro správu datových sad v řešeních AI.
> Použijete `azureml-dataprep` -li balíček k vytvoření datového toku s transformacemi namísto `azureml-datasets` použití balíčku k vytvoření datové sady, nebudete moci později použít snímky nebo datové sady s verzemi.

Vzhledem k tomu, že neexistují žádná omezení, kolik zapsat kroky jsou v kanálu, můžete snadno přidat další zápis kroky k získání průběžné výsledky při odstraňování problémů nebo jiných kanálů.

Vyvolá se pokaždé, když spustíte zápisu krok, úplné o přijetí změn dat v toku dat. Například toku dat s tři kroky zápis se číst a zpracovávat každý záznam v datové sadě třikrát.

## <a name="supported-data-types-and-location"></a>Podporované datové typy a umístění

Jsou podporovány následující formáty souborů
-   Soubory s oddělovači (CSV, TSV, atd.)
-   Soubory parquet

Pomocí sady Azure Machine Learning data PREP Python SDK můžete zapisovat data do:
+ místního systému souborů
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Důležité informace o Sparku

Při spuštění toku dat ve Sparku, musíte napsat k prázdné složce. Probíhá pokus o spuštění zápis existující složce důsledkem chyby. Ujistěte se, že cílová složka je prázdná nebo použít jiné cílové umístění pro každé spuštění nebo zápisu se nezdaří.

## <a name="monitoring-write-operations"></a>Monitorování operací zápisu

Pro usnadnění práce je vygenerován sentinel soubor s názvem úspěch po dokončení zápisu. Jeho přítomnost pomáhá identifikovat, když zprostředkující zápisu byla dokončena bez nutnosti čekat celý kanál k dokončení.

## <a name="example-write-code"></a>Příklad zápisu kódu

V tomto příkladu začněte načtením dat do toku dat pomocí `auto_read_file()`. Je-li znovu použít tato data pomocí různých formátech.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Příklad výstupu:

| | Sloupec1 | Column2 | Sloupec3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | Žádný | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | Žádný | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | Žádný | NO | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | Žádný | NO | NO |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | Žádný | NO | NO | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>Příklad souboru s oddělovači

Následující kód používá [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) funkci pro zápis dat do souboru s oddělovači.

```python
# Create a new data flow using `write_to_csv`
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Příklad výstupu:

| | Sloupec1 | Column2 | Sloupec3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | CHYBA | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | CHYBA | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | CHYBA | NO | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | CHYBA | NO | NO |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | CHYBA | NO | NO | ENSO |    59783.0 | 5350.0 |  500.0|

V předchozím výstupu se zobrazí několik chyb v číselných sloupcích z důvodu čísla, která nebyla správně parsovat. Při zápisu do sdíleného svazku clusteru, jsou hodnoty null řetězcem "Chyba" nahrazuje výchozí.

Přidáte parametry jako součást vaší zápisu volání a specifikaci řetězec použitý k reprezentaci hodnoty null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'),
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Předchozí kód vytvoří tento výstup:

| | Sloupec1 | Column2 | Sloupec3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | NO | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | NO | NO | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Příklad souboru parquet

`write_to_csv()` [PodobnějakofunkcevrátífunkcenovýtokdatskrokemzápisuParquet,kterýsespustí`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) při spuštění toku dat.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
                                     error='MiscreantData')
```

Spuštění toku dat. Pokud chcete spustit operaci zápisu.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Předchozí kód vytvoří tento výstup:

|   | Sloupec1 | Column2 | Sloupec3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | NO| NO| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Konfigurace dat pro automatizované školení strojového učení

Dodejte nově zapsaný datový soubor do [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) objektu v přípravě na automatizované školení služby Machine Learning. 

Následující příklad kódu ukazuje, jak převést datový tok na PANDAS dataframe a následně ho rozdělit na školení a testování datových sad pro automatizované školení strojového učení.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Pokud nepotřebujete žádné mezilehlé kroky přípravy dat, jako v předchozím příkladu, můžete předat datový tok přímo do `AutoMLConfig`.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Další kroky
* Vzory návrhu a příklady použití najdete v [přehledu](https://aka.ms/data-prep-sdk) sady SDK. 
* Příklad regresního modelu najdete v [kurzu](tutorial-auto-train-models.md) automatizovaného strojového učení.
