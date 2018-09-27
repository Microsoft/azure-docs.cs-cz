---
title: Zápis dat pomocí Azure Machine Learning Prep sady SDK pro Data – Python
description: Další informace o vytváření dat pomocí sady SDK služby Azure Machine Learning Data Prep. Můžete napsat data v libovolném bodě v toku dat a soubory v libovolné z našich podporovaných umístění (místního systému souborů Azure Blob Storage a Azure Data Lake Storage).
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 81344d388fbba0db034b8adb06adab6797ec2ce1
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166738"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Zápis dat pomocí sady SDK pro Azure Machine Learning Data Prep
Můžete napsat data v libovolném bodě datový tok. Tyto zápisy jsou přidány jako kroky pro výsledný toku dat a se spustí pokaždé, když tok dat je. Data se zapisují do více souborů oddíl umožňuje paralelní zápisy.

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

V tomto příkladu nejprve načítání dat do datového toku. Můžeme znovu použije tentýž tato data pomocí různých formátech.

```python
import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

Příklad výstupu:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   Žádný|       NE|     NE  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   Žádný|       NE|     NE  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    Žádný|   NE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    Žádný|   NE| NE| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    Žádný|   NE| NE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    Žádný|   NE| NE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   Žádný|   NE| NE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    Žádný|   NE| NE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    Žádný|   NE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    Žádný|   NE| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>Příklad souboru s oddělovači

V této části uvidíte příklad použití `write_to_csv` funkce jak psát pomocí souboru s oddělovači.

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
| 0 |   10000.0 |   99999.0 |   CHYBA |       NE|     NE  |   ENRS    |CHYBA    |   CHYBA |   CHYBA|    
|   1|      10003.0 |   99999.0 |   CHYBA |       NE|     NE  |   ENSO|       CHYBA|        CHYBA |CHYBA|   
|   2|  10010.0|    99999.0|    CHYBA |   NE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    CHYBA |   NE| NE| |   CHYBA|    CHYBA|    CHYBA|
|4| 10014.0|    99999.0|    CHYBA |   NE| NE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    CHYBA |   NE| NE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   CHYBA |   NE| NE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    CHYBA |   NE| NE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    CHYBA |   NE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    CHYBA |   NE| SV|     |77000.0|   15500.0|    120.0|

Zobrazí se v předchozím výstupu, který zobrazí několik chyb v číselných sloupcích z důvodu čísla, která nebyla správně parsovat. Při zápisu do sdíleného svazku clusteru, jsou tyto hodnoty null řetězcem "Chyba" nahrazuje výchozí. 

Můžete přidat parametry jako součást vaší zápisu volání a specifikaci řetězec použitý k reprezentaci hodnoty null. Příklad:

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
| 0 |   10000.0 |   99999.0 |   BadData |       NE|     NE  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NE|     NE  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NE| NE| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NE| NE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NE| NE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NE| NE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NE| NE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NE| SV|     |77000.0|   15500.0|    120.0|


### <a name="parquet-file-example"></a>Příklad souboru parquet

Podobně jako `write_to_csv`, `write_to_parquet` nového toku dat pomocí zápisu Parquet krok, který se spustí při spuštění toku dat vrátí funkce hodnotu.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Potom můžete spustit tok dat spustit operaci zápisu.

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

Předchozí kód vytvoří tento výstup:
|   |  Sloupec1 |    Column2 | Sloupec3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NE|     NE  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NE|     NE  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NE| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NE| NE| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NE| NE| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NE| NE| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NE| NE|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NE| NE| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NE| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NE| SV|     |77000.0|   15500.0|    120.0|
