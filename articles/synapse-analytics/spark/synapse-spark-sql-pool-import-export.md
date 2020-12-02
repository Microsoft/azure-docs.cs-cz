---
title: Import a export dat mezi Apache Spark fondy bez serveru a fondy SQL
description: Tento článek poskytuje informace o tom, jak používat vlastní konektor pro přesun dat mezi vyhrazenými fondy SQL a fondy bez serveru Apache Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 178fc12fe8e8e20af8deb40c62990c279af4ab64
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452836"
---
# <a name="introduction"></a>Úvod

Azure synapse Apache Spark pro synapse SQL Connector je navržený tak, aby efektivně přenesl data mezi fondy Apache Spark serverů bez serveru a fondy SQL v Azure synapse. Azure synapse Apache Spark pro synapse SQL Connector funguje jenom na vyhrazených fondech SQL, ale nefunguje s fondem SQL bez serveru.

## <a name="design"></a>Návrh

Přenos dat mezi fondy Spark a fondy SQL se dá provést pomocí JDBC. Nicméně u dvou distribuovaných systémů, jako jsou Spark a SQL, je JDBC kritickým bodem pro přenos dat pomocí sériového přenosu dat.

Azure synapse Apache Spark fond až synapse SQL Connector je implementace zdroje dat pro Apache Spark. Používá Azure Data Lake Storage Gen2 a základnu ve vyhrazených fondech SQL k efektivnímu přenosu dat mezi clusterem Spark a instancí SQL synapse.

![Architektura konektoru](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Ověřování ve službě Azure synapse Analytics

Ověřování mezi systémy je v Azure synapse Analytics bezproblémové. Služba tokenů se připojuje k Azure Active Directory, aby získala tokeny zabezpečení pro použití při přístupu k účtu úložiště nebo k serveru datového skladu.

Z tohoto důvodu není nutné vytvářet přihlašovací údaje ani je zadat v rozhraní API konektoru, pokud je služba Azure AD-Auth nakonfigurovaná na účtu úložiště a na serveru datového skladu. V takovém případě může být zadáno ověřování SQL. Další podrobnosti najdete v části věnované [používání](#usage) .

## <a name="constraints"></a>Omezení

- Tento konektor funguje pouze v Scala.

## <a name="prerequisites"></a>Předpoklady

- Musí být členem role **db_exporter** v databázi nebo ve fondu SQL, do které chcete přenést data.
- Musí být členem role Přispěvatel dat objektů BLOB úložiště ve výchozím účtu úložiště.

Chcete-li vytvořit uživatele, připojte se k databázi fondu SQL a postupujte podle těchto příkladů:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Přiřazení role:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Využití

Příkazy import nejsou vyžadovány, jsou předem importovány pro prostředí poznámkového bloku.

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Přenos dat do nebo z vyhrazeného fondu SQL připojeného v pracovním prostoru

> [!NOTE]
> **V prostředí poznámkových blocích nejsou importy nutné.**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Rozhraní API pro čtení

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

Výše uvedené rozhraní API bude fungovat pro interní (spravované) i externí tabulky ve fondu SQL.

#### <a name="write-api"></a>Zapisovat rozhraní API

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

Rozhraní Write API vytvoří tabulku ve vyhrazeném fondu SQL a potom vyvolá základ, aby data načetla.  Tabulka nesmí existovat ve vyhrazeném fondu SQL, jinak se vrátí chyba oznamující, že objekt s názvem již existuje...

Hodnoty TableType

- Konstanty. interní spravovaná tabulka ve vyhrazeném fondu SQL
- Konstanty. EXTERNAL – externí tabulka ve vyhrazeném fondu SQL

Tabulka spravovaná fondem SQL

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Externí tabulka fondu SQL

Chcete-li zapisovat do vyhrazené externí tabulky fondu SQL, externí zdroj dat a externí formát souboru musí existovat ve vyhrazeném fondu SQL.  Další informace najdete v [tématu Vytvoření externího zdroje dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [externích formátů souborů](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ve vyhrazeném fondu SQL.  Níže jsou uvedeny příklady pro vytvoření externího zdroje dat a externích formátů souborů ve vyhrazeném fondu SQL.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

EXTERNÍ objekt PŘIHLAŠOVACÍch údajů není nutný při použití Azure Active Directory předávacího ověřování pro účet úložiště.  Ujistěte se, že jste členem role "Přispěvatel dat objektů BLOB úložiště" v účtu úložiště.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Přenos dat do nebo z vyhrazeného fondu nebo databáze SQL mimo pracovní prostor

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
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Použití ověřování SQL místo Azure AD

#### <a name="read-api"></a>Rozhraní API pro čtení

V současné době konektor nepodporuje ověřování na základě tokenů pro vyhrazený fond SQL, který je mimo pracovní prostor. Budete muset použít ověřování SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Zapisovat rozhraní API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-the-pyspark-connector"></a>Použití konektoru PySpark

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

scala_df.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Podobně ve scénáři čtení si přečtěte data pomocí Scala a zapište je do dočasné tabulky a pomocí Spark SQL v PySpark se Dotazujte dočasnou tabulku do datového rámce.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Povolí ostatním uživatelům používat Azure synapse Apache Spark ke konektoru SQL synapse ve vašem pracovním prostoru.

V účtu úložiště ADLS Gen2 připojeném k pracovnímu prostoru musíte být vlastníkem dat objektu BLOB úložiště, abyste mohli změnit chybějící oprávnění pro ostatní. Ujistěte se, že uživatel má přístup k pracovnímu prostoru a oprávnění ke spouštění poznámkových bloků.

### <a name="option-1"></a>Možnost 1

- Nastavit uživatele jako přispěvatele nebo vlastníka dat objektu BLOB úložiště

### <a name="option-2"></a>Možnost 2

- Zadejte následující seznamy ACL pro strukturu složek:

| Složka | / | synapse | pracovní prostory  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Přístupová oprávnění | --X | --X | --X | --X | --X | --X | – WX |
| Výchozí oprávnění | ---| ---| ---| ---| ---| ---| ---|

- Měli byste být schopni se připojit k seznamu všech složek z "synapse" a dolů od Azure Portal. Pro kořenovou složku "/" seznamu ACL postupujte podle následujících pokynů.

- Připojení k účtu úložiště připojenému k pracovnímu prostoru z Průzkumník služby Storage pomocí Azure AD
- Vyberte svůj účet a zadejte adresu URL ADLS Gen2 a výchozí systém souborů pro pracovní prostor.
- Jakmile uvidíte účet úložiště, který je uvedený v seznamu, klikněte pravým tlačítkem na pracovní prostor výpisu a vyberte spravovat přístup.
- Přidejte uživatele do složky/a s oprávněním "spustit" přístup. Vyberte OK.

> [!IMPORTANT]
> Pokud nechcete, ujistěte se, že nevyberete možnost výchozí.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vyhrazeného fondu SQL pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Vytvoření nového fondu Apache Spark pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
