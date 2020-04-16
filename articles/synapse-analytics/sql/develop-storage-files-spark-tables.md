---
title: Tabulky Zapalování dotazů pomocí SQL na vyžádání (preview)
description: Přehled dotazování tabulek Spark pomocí SQL na vyžádání (preview)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424045"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Tabulky Query Spark s Azure Synapse Analytics pomocí SQL na vyžádání (preview)

SQL na vyžádání (preview) můžete automaticky synchronizovat metadata z fondů Spark v rámci synapse pracovního prostoru (náhled). Databáze SQL na vyžádání se vytvoří pro každou databázi existující ve fondech Spark (preview). Pro každou tabulku Spark založenou na parketách nebo CSV externí tabulka se vytvoří v databázi SQL na vyžádání. Jako takové můžete vypnout fondy Spark a stále dotaz Spark tabulky z SQL na vyžádání.

Když je tabulka rozdělená na oddíly v Sparku, soubory v úložišti jsou uspořádány podle složek. SQL na vyžádání bude využívat metadata oddílu a cílí pouze na relevantní složky a soubory pro váš dotaz.

Synchronizace metadat se automaticky nakonfiguruje pro každý fond Spark zřízený v pracovním prostoru Azure Synapse. Můžete začít dotazování Spark tabulky okamžitě.

Každá tabulka Spark je reprezentována externí tabulkou ve schématu dbo, které odpovídá databázi SQL na vyžádání. Pro dotazy tabulek Spark spusťte dotaz, který cílí na externí [spark_table]. Před spuštěním níže uvedeného příkladu se ujistěte, že máte správný [přístup k účtu úložiště,](develop-storage-files-storage-access-control.md) kde jsou soubory umístěny.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Mapování datových typů Spark pro datové typy SQL

| Datový typ Jiskry | Datový typ SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| Dlouhý typ        | bigint                      |
| FloatType       | reálná                        |
| Dvojitý typ      | float                       |
| Desítkový typ     | decimal                     |
| Typ časového razítka   | datetime2                   |
| Typ data        | date                        |
| Typ řetězce      | varchar(max.)               |
| BinaryType      | Varbinary                   |
| Logický typ     | bitové                         |
| ArrayType       | varchar(max)* (do JSON)** |
| Maptype         | varchar(max)* (do JSON)** |
| StructType      | varchar(max)* (do JSON)** |

\*Řazení se používá Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType a StructType jsou reprezentovány jako JSONs.



## <a name="next-steps"></a>Další kroky

Přejdete k článku [Řízení přístupu k úložišti,](develop-storage-files-storage-access-control.md) kde najdete další informace o řízení přístupu k úložišti.
