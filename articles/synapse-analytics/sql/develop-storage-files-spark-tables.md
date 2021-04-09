---
title: Synchronizovat Apache Spark pro definice externích tabulek ve fondu SQL bez serveru
description: Přehled postupu dotazování na tabulky Spark pomocí SQL fondu bez serveru
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446494"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Synchronizace Apache Spark pro definice externích tabulek Azure synapse ve fondu SQL bez serveru

Fond SQL bez serveru může automaticky synchronizovat metadata z Apache Spark. Databáze fondu SQL bez serveru se vytvoří pro každou databázi existující v Apache Spark fondech bez serveru. 

Pro každou externí tabulku Spark založenou na Parquet a nacházející se v Azure Storage se vytvoří externí tabulka v neserverové databázi fondu SQL. V takovém případě můžete vypnout fondy Spark a i nadále dotazovat externí tabulky Spark z SQL fondu bez serveru.

Když je tabulka rozdělená do oddílu Spark, soubory v úložišti jsou uspořádány podle složek. Fond SQL bez serveru bude používat metadata oddílu a v dotazu budou pouze relevantní složky a soubory.

Synchronizace metadat se automaticky nakonfiguruje pro každý fond Apache Spark bez serveru zřízený v pracovním prostoru Azure synapse. Můžete okamžitě spustit dotazování externích tabulek Spark.

Každá externí tabulka Spark Parquet umístěná v Azure Storage je reprezentovaná externí tabulkou ve schématu dbo, která odpovídá databázi fondu SQL bez serveru. 

U dotazů na externí tabulku Spark spusťte dotaz, který cílí na externí [spark_table]. Před spuštěním následujícího příkladu se ujistěte, že máte správný [přístup k účtu úložiště](develop-storage-files-storage-access-control.md) , ve kterém jsou soubory umístěné.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Příkazy pro přidání, zrušení nebo změnu v externí tabulce Spark pro sloupec se neprojeví v externí tabulce ve fondu SQL bez serveru.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark datových typů do mapování datových typů SQL

| Typ dat Spark | Datový typ SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Krátký typ      | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) \* (do formátu JSON)\** |
| MapType         | varchar (max) \* (do formátu JSON)\** |
| StructType      | varchar (max) \* (do formátu JSON)\** |

\* Použitá kolace je Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType a StructType se reprezentují jako JSON.



## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o řízení přístupu k úložišti, přejděte k Access Control v článku [úložiště](develop-storage-files-storage-access-control.md) .
