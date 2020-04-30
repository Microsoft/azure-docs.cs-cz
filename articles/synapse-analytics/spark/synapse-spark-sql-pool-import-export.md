---
title: Import a export dat mezi fondy Spark (Preview) a fondy SQL
description: Tento článek poskytuje informace o tom, jak používat vlastní konektor pro přesouvání dat mezi fondy SQL a fondy Spark (Preview).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424290"
---
# <a name="introduction"></a>Úvod

Konektor Spark SQL Analytics je navržený tak, aby efektivně přenesl data mezi fondem Spark (Preview) a fondy SQL v Azure synapse. Konektor Spark SQL Analytics funguje jenom na fondech SQL, ale nefunguje s SQL na vyžádání.

## <a name="design"></a>Návrh

Přenos dat mezi fondy Spark a fondy SQL se dá provést pomocí JDBC. Nicméně u dvou distribuovaných systémů, jako jsou Spark a SQL, je JDBC kritickým bodem pro přenos dat pomocí sériového přenosu dat.

Fondy Sparku na SQL Analytics Connector je implementace zdroje dat pro Apache Spark. Používá Azure Data Lake Storage Gen 2 a základnu v rámci fondů SQL k efektivnímu přenosu dat mezi clusterem Spark a instancí SQL Analytics.

![Architektura konektoru](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Ověřování ve službě Azure synapse Analytics

Ověřování mezi systémy je v Azure synapse Analytics bezproblémové. Existuje služba tokenů, která se připojuje k Azure Active Directory, aby získala tokeny zabezpečení pro použití při přístupu k účtu úložiště nebo k serveru datového skladu. Z tohoto důvodu není nutné vytvářet přihlašovací údaje ani je zadat v rozhraní API konektoru, pokud je v účtu úložiště a na serveru datového skladu nakonfigurováno AAD-auth. V takovém případě může být zadáno ověřování SQL. Další podrobnosti najdete v části věnované [používání](#usage) .

## <a name="constraints"></a>Omezení

- Tento konektor funguje pouze v Scala.

## <a name="prerequisites"></a>Požadavky

- Měli byste mít **db_exporter** roli v databázi nebo ve fondu SQL, do které chcete přenést data.

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

Příkazy import není nutné poskytnout, jsou předem importovány pro prostředí poznámkového bloku.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Přenos dat do nebo z fondu SQL na logickém serveru (instance DW) připojené k pracovnímu prostoru

> [!NOTE]
> **V prostředí poznámkových blocích nejsou importy nutné.**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Rozhraní API pro čtení

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Výše uvedené rozhraní API bude fungovat pro interní (spravované) i externí tabulky ve fondu SQL.

#### <a name="write-api"></a>Zapisovat rozhraní API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

kde TableType můžou být konstanty. INTERNAL nebo konstanty. EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Ověřování pro úložiště a SQL Server je dokončeno.

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Pokud přenášíte data do nebo z fondu nebo databáze SQL na logickém serveru mimo pracovní prostor

> [!NOTE]
> V prostředí poznámkových blocích nejsou importy nutné.

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Rozhraní API pro čtení

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Zapisovat rozhraní API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Použití ověřování SQL místo AAD

#### <a name="read-api"></a>Rozhraní API pro čtení

V současné době konektor nepodporuje ověřování na základě tokenů pro fond SQL, který je mimo pracovní prostor. Musíte použít ověřování SQL.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Zapisovat rozhraní API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Použití konektoru PySpark

> [!NOTE]
> Tento příklad je dán pouze v případě, že máte na paměti poznámkové bloky zachovány.

Předpokládejme, že máte datový rámec "pyspark_df", který chcete zapisovat do datové sady DW.

Vytvoření dočasné tabulky pomocí datového rámce v PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Spuštění Scala buňky v poznámkovém bloku PySpark pomocí MAGICS

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Podobně ve scénáři čtení si přečtěte data pomocí Scala a zapište je do dočasné tabulky a pomocí Spark SQL v PySpark se Dotazujte dočasnou tabulku do datového rámce.

## <a name="next-steps"></a>Další kroky

- [Vytvoření fondu SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Vytvoření nového fondu Apache Spark pro pracovní prostor Azure synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 