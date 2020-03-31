---
title: Spark Connector s databází Azure SQL a SQL Serverem
description: Přečtěte si, jak používat konektor Spark pro Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471648"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Zrychlete analýzu velkých objemů dat v reálném čase pomocí konektoru Spark pro Azure SQL Database a SQL Server

Konektor Spark pro Azure SQL Database a SQL Server umožňuje databázím SQL, včetně Azure SQL Database a SQL Serveru, fungovat jako vstupní zdroj dat nebo jímka výstupních dat pro úlohy Spark. Umožňuje využívat transakční data v reálném čase v analýze velkých objemů dat a uchovávat výsledky pro adhoc dotazy nebo vytváření sestav. Ve srovnání s vestavěnou konektorjdbc, tento konektor poskytuje možnost hromadné vkládání dat do databází SQL. To může překonat řádek po řádku vložení s 10x až 20x rychlejší výkon. Konektor Spark pro Azure SQL Database a SQL Server také podporuje ověřování AAD. Umožňuje bezpečné připojení k databázi Azure SQL z Azure Databricks pomocí vašeho účtu AAD. Poskytuje podobná rozhraní s vestavěným konektorem JDBC. Je snadné migrovat stávající úlohy Spark používat tento nový konektor.

## <a name="download"></a>Stáhnout
Chcete-li začít, stáhněte si konektor Spark do SQL DB z [úložiště azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) na GitHubu.

## <a name="official-supported-versions"></a>Oficiální podporované verze

| Komponenta                            | Version                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 nebo novější           |
| Scala                                | 2.10 nebo novější            |
| Ovladač Microsoft JDBC pro SQL Server | 6.2 nebo novější             |
| Microsoft SQL Server                 | SQL Server 2008 nebo novější |
| Azure SQL Database                   | Podporuje se                |

Konektor Spark pro Azure SQL Database a SQL Server využívá ovladač Microsoft JDBC pro SQL Server k přesunu dat mezi pracovními uzly Spark a databázemi SQL:
 
Tok dat je následující:
1. Hlavní uzel Spark se připojí k SQL Serveru nebo Azure SQL Database a načte data z určité tabulky nebo pomocí konkrétního dotazu SQL
2. Hlavní uzel Spark distribuuje data do pracovních uzlů pro transformaci. 
3. Pracovní uzel se připojí k SQL Server nebo Azure SQL Database a zapíše data do databáze. Uživatel může použít vložení řádek po řádku nebo hromadné vložení.

Následující diagram znázorňuje tok dat.

   ![Architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Vytvoření konektoru Spark to SQL DB
V současné době projekt konektoru používá maven. Chcete-li vytvořit konektor bez závislostí, můžete spustit:

- mvn čistý balíček
- Stáhněte si nejnovější verze JAR ze složky vydání
- Zahrnout JAZYK SQL DB Spark

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Připojení Sparka k SQL DB pomocí konektoru
Můžete se připojit k Azure SQL Database nebo SQL Serveru z úloh Spark, číst nebo zapisovat data. Můžete také spustit dotaz DML nebo DDL v databázi Azure SQL nebo SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Čtení dat z Azure SQL Database nebo SQL Serveru

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Čtení dat z Azure SQL Database nebo SQL Serveru se zadaným dotazem SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zápis dat do databáze Azure SQL nebo SQL Serveru
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Spuštění dotazu DML nebo DDL v Azure SQL Database nebo SQL Serveru
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Připojení Sparka k Azure SQL Database pomocí ověřování AAD
K Azure SQL Database se můžete připojit pomocí ověřování Azure Active Directory (AAD). Pomocí ověřování AAD můžete centrálně spravovat identity uživatelů databáze a jako alternativu k ověřování serveru SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Připojení pomocí režimu ověřování hesla activedirectorypassword
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování ActiveDirectoryPassword, je třeba stáhnout [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesty sestavení jazyka Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Připojení pomocí přístupového tokenu
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování založený na přístupových tokenech, je třeba stáhnout [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesty sestavení jazyka Java.

Informace o tom, jak získat přístupový token do databáze Azure SQL, najdete v článku [Použití ověřování služby Azure Active Directory pro ověřování pomocí databáze SQL.](sql-database-aad-authentication.md)

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zápis dat do databáze Azure SQL nebo SQL Serveru pomocí hromadného vložení
Tradiční konektor jdbc zapisuje data do databáze Azure SQL nebo SQL Serveru pomocí vložení řádek po řádku. Konektor Spark to SQL DB můžete použít k zápisu dat do databáze SQL pomocí hromadného vložení. Výrazně zlepšuje výkon zápisu při načítání velkých datových sad nebo načítání dat do tabulek, kde se používá index úložiště sloupců.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Další kroky
Pokud jste to ještě neudělali, stáhněte si konektor Spark pro Azure SQL Database a SQL Server z [úložiště GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) a prozkoumejte další prostředky v úložišti:

- [Ukázkové poznámkové bloky Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Ukázkové skripty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Můžete také zkontrolovat [Apache Spark SQL, DataFrames a Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) a [dokumentaci Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

