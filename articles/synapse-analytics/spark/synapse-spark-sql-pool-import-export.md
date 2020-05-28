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
ms.openlocfilehash: 20b030079121104fe7bd75924a63ab0e12be9b19
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020859"
---
# <a name="introduction"></a>Úvod

Azure synapse Apache Spark pro synapse SQL Connector je navržený tak, aby efektivně přenesl data mezi fondy Spark (Preview) a fondy SQL ve službě Azure synapse. Služba Azure synapse Apache Spark pro synapse SQL Connector funguje jenom na fondech SQL, ale nefunguje s SQL na vyžádání.

## <a name="design"></a>Návrh

Přenos dat mezi fondy Spark a fondy SQL se dá provést pomocí JDBC. Nicméně u dvou distribuovaných systémů, jako jsou Spark a SQL, je JDBC kritickým bodem pro přenos dat pomocí sériového přenosu dat.

Azure synapse Apache Spark fond až synapse SQL Connector je implementace zdroje dat pro Apache Spark. Používá Azure Data Lake Storage Gen2 a základnu v fondech SQL k efektivnímu přenosu dat mezi clusterem Spark a instancí SQL synapse.

![Architektura konektoru](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Ověřování ve službě Azure synapse Analytics

Ověřování mezi systémy je v Azure synapse Analytics bezproblémové. Existuje služba tokenů, která se připojuje k Azure Active Directory, aby získala tokeny zabezpečení pro použití při přístupu k účtu úložiště nebo k serveru datového skladu. 

Z tohoto důvodu není nutné vytvářet přihlašovací údaje ani je zadat v rozhraní API konektoru, pokud je v účtu úložiště a na serveru datového skladu nakonfigurováno AAD-auth. V takovém případě může být zadáno ověřování SQL. Další podrobnosti najdete v části věnované [používání](#usage) .

## <a name="constraints"></a>Omezení

- Tento konektor funguje pouze v Scala.

## <a name="prerequisites"></a>Požadavky

- Měli byste mít **db_exporter** roli v databázi nebo ve fondu SQL, do které chcete přenést data.

Chcete-li vytvořit uživatele, připojte se k databázi a postupujte podle těchto příkladů:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Přiřazení role:

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Využití

Příkazy import nejsou vyžadovány, jsou předem importovány pro prostředí poznámkového bloku.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Přenos dat do nebo z fondu SQL připojeném k pracovnímu prostoru

> [!NOTE]
> **V prostředí poznámkových blocích nejsou importy nutné.**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Rozhraní API pro čtení

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Výše uvedené rozhraní API bude fungovat pro interní (spravované) i externí tabulky ve fondu SQL.

#### <a name="write-api"></a>Zapisovat rozhraní API

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

kde TableType můžou být konstanty. INTERNAL nebo konstanty. EXTERNAL

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Ověřování pro úložiště a SQL Server je dokončeno.

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Pokud přenášíte data do nebo z fondu nebo databáze SQL mimo pracovní prostor

> [!NOTE]
> V prostředí poznámkových blocích nejsou importy nutné.

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Rozhraní API pro čtení

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Zapisovat rozhraní API

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Použití ověřování SQL místo AAD

#### <a name="read-api"></a>Rozhraní API pro čtení

V současné době konektor nepodporuje ověřování na základě tokenů pro fond SQL, který je mimo pracovní prostor. Budete muset použít ověřování SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Zapisovat rozhraní API

```scala
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

Vytvořte dočasnou tabulku pomocí datového rámce v PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Spusťte v poznámkovém bloku PySpark Scala buňku pomocí MAGICS:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Podobně ve scénáři čtení si přečtěte data pomocí Scala a zapište je do dočasné tabulky a pomocí Spark SQL v PySpark se Dotazujte dočasnou tabulku do datového rámce.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Povolení použití konektoru DW v pracovním prostoru jiným uživatelům

V účtu úložiště ADLS Gen2 připojeném k pracovnímu prostoru musíte být vlastníkem dat objektu BLOB úložiště, abyste mohli změnit chybějící oprávnění pro ostatní. Ujistěte se, že uživatel má přístup k pracovnímu prostoru a oprávnění ke spouštění poznámkových bloků.

### <a name="option-1"></a>Možnost 1

- Nastavit uživatele jako přispěvatele nebo vlastníka dat objektu BLOB úložiště

### <a name="option-2"></a>2. možnost

- Zadejte následující seznamy ACL pro strukturu složek:

| Složka | / | synapse | pracovní prostory  | <workspacename> | sparkpools | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Přístupová oprávnění | --X | --X | --X | --X | --X | --X | – WX |
| Výchozí oprávnění | ---| ---| ---| ---| ---| ---| ---|

- Měli byste být schopni se připojit k seznamu všech složek z "synapse" a dolů od Azure Portal. Pro kořenovou složku "/" seznamu ACL postupujte podle následujících pokynů.

- Připojení k účtu úložiště připojenému k pracovnímu prostoru z Průzkumník služby Storage pomocí AAD
- Vyberte svůj účet a zadejte adresu URL ADLS Gen2 a výchozí systém souborů pro pracovní prostor.
- Jakmile uvidíte účet úložiště, který je uvedený v seznamu, klikněte pravým tlačítkem na pracovní prostor výpisu a vyberte spravovat přístup.
- Přidejte uživatele do složky/a s oprávněním "spustit" přístup. Vyberte OK.

> [!IMPORTANT]
> Pokud nechcete, ujistěte se, že nevyberete možnost výchozí.

## <a name="next-steps"></a>Další kroky

- [Vytvoření fondu SQL pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Vytvoření nového fondu Apache Spark pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
