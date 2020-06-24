---
title: Synchronizovat Apache Spark pro definice externích tabulek Azure synapse na vyžádání SQL (Preview)
description: Přehled postupu dotazování na tabulky Spark pomocí SQL na vyžádání (Preview)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: e36f98a20d1fc6392aef1aebf5fc86e18085cc10
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204894"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Synchronizovat Apache Spark pro definice externích tabulek Azure synapse na vyžádání SQL (Preview)

SQL na vyžádání (Preview) může automaticky synchronizovat metadata z Apache Spark pro fondy Azure synapse. Databáze SQL na vyžádání se vytvoří pro každou databázi existující ve fondech Spark (Preview). 

Pro každou externí tabulku Spark založenou na Parquet a v Azure Storage se vytvoří externí tabulka v databázi SQL na vyžádání. V takovém případě můžete vypnout fondy Spark a i nadále dotazovat externí tabulky Spark z SQL na vyžádání.

Když je tabulka rozdělená do oddílu Spark, soubory v úložišti jsou uspořádány podle složek. SQL na vyžádání použije metadata oddílu a zacílí jenom na relevantní složky a soubory pro váš dotaz.

Synchronizace metadat se automaticky nakonfiguruje pro každý fond Spark zřízený v pracovním prostoru Azure synapse. Můžete okamžitě spustit dotazování externích tabulek Spark.

Každá externí tabulka Spark Parquet umístěná v Azure Storage je reprezentovaná externí tabulkou ve schématu dbo, která odpovídá databázi SQL na vyžádání. 

U dotazů na externí tabulku Spark spusťte dotaz, který cílí na externí [spark_table]. Před spuštěním následujícího příkladu se ujistěte, že máte správný [přístup k účtu úložiště](develop-storage-files-storage-access-control.md) , ve kterém jsou soubory umístěné.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Datové typy Sparku pro mapování datových typů SQL

| Typ dat Spark | Datový typ SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar (max) *               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) * (do formátu JSON) * * |
| MapType         | varchar (max) * (do formátu JSON) * * |
| StructType      | varchar (max) * (do formátu JSON) * * |

\*Použitá kolace je Latin1_General_100_BIN2_UTF8.

* * ArrayType, MapType a StructType se reprezentují jako JSON.



## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o řízení přístupu k úložišti, přejděte k Access Control v článku [úložiště](develop-storage-files-storage-access-control.md) .
