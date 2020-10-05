---
title: Import a export dat mezi fondy Spark (Preview) a fondy SQL
description: Tento článek poskytuje informace o tom, jak používat vlastní konektor pro přesouvání dat mezi fondy SQL a fondy Spark (Preview).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 11f73d2becb40b800c49afe0cd58f56953f8d42d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91259913"
---
# <a name="introduction"></a>Úvod

Azure synapse Apache Spark pro synapse SQL Connector je navržený tak, aby efektivně přenesl data mezi fondy Spark (Preview) a fondy SQL ve službě Azure synapse. Služba Azure synapse Apache Spark pro synapse SQL Connector funguje jenom na fondech SQL, ale nefunguje s SQL na vyžádání.

## <a name="design"></a>Návrh

Přenos dat mezi fondy Spark a fondy SQL se dá provést pomocí JDBC. Nicméně u dvou distribuovaných systémů, jako jsou Spark a SQL, je JDBC kritickým bodem pro přenos dat pomocí sériového přenosu dat.

Azure synapse Apache Spark fond až synapse SQL Connector je implementace zdroje dat pro Apache Spark. Používá Azure Data Lake Storage Gen2 a základnu v fondech SQL k efektivnímu přenosu dat mezi clusterem Spark a instancí SQL synapse.

![Architektura konektoru](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Ověřování ve službě Azure synapse Analytics

Ověřování mezi systémy je v Azure synapse Analytics bezproblémové. Služba tokenů se připojuje k Azure Active Directory, aby získala tokeny zabezpečení pro použití při přístupu k účtu úložiště nebo k serveru datového skladu.

Z tohoto důvodu není nutné vytvářet přihlašovací údaje ani je zadat v rozhraní API konektoru, pokud je v účtu úložiště a na serveru datového skladu nakonfigurováno AAD-auth. V takovém případě může být zadáno ověřování SQL. Další podrobnosti najdete v části věnované [používání](#usage) .

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

### <a name="transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Přenos dat do nebo z fondu SQL připojeného k pracovnímu prostoru

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

Rozhraní Write API vytvoří tabulku ve fondu SQL a potom vyvolá základ, aby data načetla.  Tabulka nesmí existovat ve fondu SQL nebo může být vrácena chyba oznamující, že objekt s názvem již existuje...

Hodnoty TableType

- Konstanty. interní spravovaná tabulka ve fondu SQL
- Konstanty. EXTERNAL – externí tabulka ve fondu SQL

Tabulka spravovaná fondem SQL

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Externí tabulka fondu SQL

Aby bylo možné zapisovat do externí tabulky fondu SQL, externí zdroj dat a externí formát souboru musí existovat ve fondu SQL.  Další informace najdete v [tématu Vytvoření externího zdroje dat](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) a [formátů externích souborů](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ve fondu SQL.  Níže jsou uvedeny příklady pro vytvoření externího zdroje dat a formátů externích souborů ve fondu SQL.

```sql
--For an external table, you need to pre-create the data source and file format in SQL pool using SQL queries:
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

### <a name="if-you-transfer-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Pokud přenášíte data do nebo z fondu nebo databáze SQL mimo pracovní prostor

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

### <a name="use-sql-auth-instead-of-aad"></a>Místo AAD použít ověřování SQL

#### <a name="read-api"></a>Rozhraní API pro čtení

V současné době konektor nepodporuje ověřování na základě tokenů pro fond SQL, který je mimo pracovní prostor. Budete muset použít ověřování SQL.

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

- Připojení k účtu úložiště připojenému k pracovnímu prostoru z Průzkumník služby Storage pomocí AAD
- Vyberte svůj účet a zadejte adresu URL ADLS Gen2 a výchozí systém souborů pro pracovní prostor.
- Jakmile uvidíte účet úložiště, který je uvedený v seznamu, klikněte pravým tlačítkem na pracovní prostor výpisu a vyberte spravovat přístup.
- Přidejte uživatele do složky/a s oprávněním "spustit" přístup. Vyberte OK.

> [!IMPORTANT]
> Pokud nechcete, ujistěte se, že nevyberete možnost výchozí.

## <a name="next-steps"></a>Další kroky

- [Vytvoření fondu SQL pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Vytvoření nového fondu Apache Spark pomocí Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
