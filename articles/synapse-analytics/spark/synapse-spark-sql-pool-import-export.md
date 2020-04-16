---
title: Import a export dat mezi fondy Spark (preview) a fondy SQL
description: Tento článek obsahuje informace o tom, jak používat vlastní konektor pro přesun dat tam a zpět mezi fondy SQL a fondy Spark (náhled).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424290"
---
# <a name="introduction"></a>Úvod

Spark SQL Analytics Connector je navržený pro efektivní přenos dat mezi fondem Spark (preview) a fondy SQL v Azure Synapse. Spark SQL Analytics Konektor funguje pouze na fondy SQL, nefunguje s SQL na vyžádání.

## <a name="design"></a>Návrh

Přenos dat mezi fondy Spark a fondy SQL lze provést pomocí JDBC. Však vzhledem k tomu, dva distribuované systémy, jako je například Spark a SQL fondy, JDBC inklinuje být kritickým bodem s přenosem sériových dat.

Spark fondy SQL Analytics Connector je implementace zdroje dat pro Apache Spark. Používá Azure Data Lake Storage Gen 2 a Polybase ve fondech SQL k efektivnímu přenosu dat mezi clusterem Spark a instancí SQL Analytics.

![Architektura konektoru](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Ověřování v Azure Synapse Analytics

Ověřování mezi systémy se provádí bezproblémově v Azure Synapse Analytics. K dispozici je tokenová služba, která se připojuje k Azure Active Directory získat tokeny zabezpečení pro použití při přístupu k účtu úložiště nebo serveru datového skladu. Z tohoto důvodu není nutné vytvářet přihlašovací údaje nebo je zadávat v rozhraní API konektoru, pokud je AAD-Auth nakonfigurován na účtu úložiště a serveru datového skladu. Pokud ne, lze zadat sql auth. Další podrobnosti najdete v části [Využití.](#usage)

## <a name="constraints"></a>Omezení

- Tento konektor funguje pouze v Modelu Scala.

## <a name="prerequisites"></a>Požadavky

- Mějte **db_exporter** roli v databázi/fondu SQL, do kterého chcete přenést data do/z nich.

Chcete-li vytvořit uživatele, připojte se k databázi a postupujte podle těchto příkladů:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Přiřazení role:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Využití

Příkazy importu není nutné zadat, jsou předem importovány pro prostředí poznámkového bloku.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Přenos dat do nebo z fondu SQL v logickém serveru (instance DW) připojeném k pracovníploše

> [!NOTE]
> **Importy, které nejsou potřeba v notebooku**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Čtení API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Výše uvedené rozhraní API bude fungovat pro interní (spravované) i externí tabulky ve fondu SQL.

#### <a name="write-api"></a>Rozhraní API pro zápis

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

where TableType může být Constants.INTERNAL nebo Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Ověřování do úložiště a SQL Server se provádí

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Pokud přenášíte data do nebo z fondu SQL nebo databáze na logickém serveru mimo pracovní prostor

> [!NOTE]
> Importy, které nejsou potřeba v notebooku

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Čtení API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Rozhraní API pro zápis

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Použití sql auth místo AAD

#### <a name="read-api"></a>Čtení API

V současné době konektor nepodporuje ověřování založené na tokenech do fondu SQL, který je mimo pracovní prostor. Musíte použít SQL Auth.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Rozhraní API pro zápis

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Použití konektoru PySpark

> [!NOTE]
> Tento příklad je uveden pouze s ohledem na zážitek z poznámkového bloku.

Předpokládejme, že máte datový rámec "pyspark_df", který chcete zapsat do DW.

Vytvoření dočasné tabulky pomocí datového rámce v PySparku

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Spuštění buňky Scala v notebooku PySpark pomocí kouzel

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Podobně ve scénáři pro čtení si přečtěte data pomocí Scaly a zapište je do dočasné tabulky a použijte Spark SQL v PySparku k dotazování dočasné tabulky do datového rámce.

## <a name="next-steps"></a>Další kroky

- [Vytvoření fondu SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Vytvoření nového fondu Apache Spark pro pracovní prostor Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 