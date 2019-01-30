---
title: 'Zápis: Python SDK pro přípravu dat'
titleSuffix: Azure Machine Learning service
description: Další informace o vytváření dat pomocí sady SDK služby Azure Machine Learning Data Prep. Můžete napsat data v libovolném bodě v toku dat a soubory v libovolné z našich podporovaných umístění (místního systému souborů Azure Blob Storage a Azure Data Lake Storage).
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
ms.openlocfilehash: 1e508d4c7ed8a8d7df8e9ae586c74258958838e9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239821"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Zápis dat pomocí sady SDK pro Azure Machine Learning Data Prep

V tomto článku se dozvíte, různé metody zapsat data pomocí sady SDK pro Azure Machine Learning Data Prep. Výstupní data lze zapsat v libovolném bodě toku dat a zápisy jsou přidány jako kroky pro výsledný toku dat a spouštějí se pokaždé, když je datový tok. Data se zapisují do více souborů oddíl umožňuje paralelní zápisy.

Vzhledem k tomu, že neexistují žádná omezení, kolik zapsat kroky jsou v kanálu, můžete snadno přidat další zápis kroky k získání průběžné výsledky při odstraňování problémů nebo jiných kanálů.

Vyvolá se pokaždé, když spustíte zápisu krok, úplné o přijetí změn dat v toku dat. Například toku dat s tři kroky zápis se číst a zpracovávat každý záznam v datové sadě třikrát.

## <a name="supported-data-types-and-location"></a>Podporované datové typy a umístění

Jsou podporovány následující formáty souborů
-   Soubory s oddělovači (CSV, TSV, atd.)
-   Soubory parquet

Použití [přípravy dat Machine Learning Azure python SDK](https://aka.ms/data-prep-sdk), může zapsat data do:
+ místního systému souborů
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Důležité informace o Sparku

Při spuštění toku dat ve Sparku, musíte napsat k prázdné složce. Probíhá pokus o spuštění zápis existující složce důsledkem chyby. Ujistěte se, že cílová složka je prázdná nebo použít jiné cílové umístění pro každé spuštění nebo zápisu se nezdaří.

## <a name="monitoring-write-operations"></a>Monitorování operací zápisu

Pro usnadnění práce je vygenerován sentinel soubor s názvem úspěch po dokončení zápisu. Jeho přítomnost pomáhá identifikovat, když zprostředkující zápisu byla dokončena bez nutnosti čekat celý kanál k dokončení.

## <a name="example-write-code"></a>Příklad zápisu kódu

V tomto příkladu nejprve načítání dat do datového toku. Je-li znovu použít tato data pomocí různých formátech.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

Příklad výstupu:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Žádný|       NO|     NO  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Žádný|       NO|     NO  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Žádný|   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Žádný|   NO| NO| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Žádný|   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Žádný|   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Žádný|   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Žádný|   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Žádný|   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    Žádný|   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Příklad souboru s oddělovači

Následující kód používá `write_to_csv` funkce k zápisu dat do souboru s oddělovači.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Příklad výstupu:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   CHYBA |       NO|     NO  |   ENRS    |CHYBA    |   CHYBA |   CHYBA|    
|   1|      10003.0 |   99999.0 |   CHYBA |       NO|     NO  |   ENSO|       CHYBA|        CHYBA |CHYBA|   
|   2|  10010.0|    99999.0|    CHYBA |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    CHYBA |   NO| NO| |   CHYBA|    CHYBA|    CHYBA|
|4| 10014.0|    99999.0|    CHYBA |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    CHYBA |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   CHYBA |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    CHYBA |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    CHYBA |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    CHYBA |   NO| SV|     |77000.0|   15500.0|    120.0|

V předchozím výstupu se zobrazí několik chyb v číselných sloupcích z důvodu čísla, která nebyla správně parsovat. Při zápisu do sdíleného svazku clusteru, jsou hodnoty null řetězcem "Chyba" nahrazuje výchozí.

Přidáte parametry jako součást vaší zápisu volání a specifikaci řetězec použitý k reprezentaci hodnoty null.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

Předchozí kód vytvoří tento výstup:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NO|     NO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NO|     NO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NO| NO| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Příklad souboru parquet

Podobně jako `write_to_csv`, `write_to_parquet` nového toku dat pomocí zápisu Parquet krok, který se spustí při spuštění toku dat vrátí funkce hodnotu.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Spuštění toku dat. Pokud chcete spustit operaci zápisu.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Předchozí kód vytvoří tento výstup:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NO| SV|     |77000.0|   15500.0|    120.0|
