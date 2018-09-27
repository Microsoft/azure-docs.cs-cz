---
title: Konektor Azure SQL Database a SQL Server Spark | Dokumentace Microsoftu
description: Další informace o použití konektoru Spark pro Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 04/23/2018
ms.openlocfilehash: 09035b19ee48602fde70736ab918fccae71108a5
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166228"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Urychlení analýzy velkých objemů dat v reálném čase pomocí konektor Spark pro Azure SQL Database a SQL Server

Konektor Spark pro Azure SQL Database a SQL Server umožňuje databází SQL, včetně Azure SQL Database a SQL Server, tak, aby fungoval jako vstupní data zdroje nebo výstupní datová jímka pro Sparkových úloh. Umožňuje využívat transakční data v reálném čase v analýzy velkých objemů dat a zachování výsledků pro ad hoc dotazy nebo vytváření sestav. Ve srovnání s integrovaného konektoru JDBC, tento konektor poskytuje možnost hromadné vložení dat do databáze SQL. To lze překonat vložení řádek po řádku s 10 x 20 x rychlejší výkon. Konektor Spark pro Azure SQL Database a SQL Server podporuje také ověřování AAD. Je možné bezpečně připojuje k databázi Azure SQL z Azure Databricks pomocí svého účtu AAD. Poskytuje podobné rozhraní integrovaného konektoru JDBC. Je snadno migrovat stávající úlohy Spark chcete použít tento nový konektor.

## <a name="download"></a>Ke stažení
Abyste mohli začít, stahovat do konektoru SQL DB z Sparku [úložiště azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) na Githubu.

## <a name="official-supported-versions"></a>Oficiální podporované verze

| Komponenta                            |Verze                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 nebo novější           |
| Scala                                |2.10 nebo novější            |
| Ovladač Microsoft JDBC pro SQL Server |6.2 nebo novější             |
| Microsoft SQL Server                 |SQL Server 2008 nebo novější |
| Azure SQL Database                   |Podporováno                |

Konektor Spark pro Azure SQL Database a SQL Server používá ovladač Microsoft JDBC pro SQL Server pro přesun dat mezi Spark pracovních uzlů a SQL Database:
 
Toku dat je následující:
1. Hlavní uzel Spark se připojuje k systému SQL Server nebo Azure SQL Database a načte data z určité tabulky nebo konkrétní dotaz SQL
2. Hlavní uzel Spark distribuuje data do pracovní uzly pro transformaci. 
3. Pracovní uzel připojí k serveru SQL Server nebo databázi SQL Azure a zapisuje data do databáze. Uživatele můžete použít řádek po řádku vložení nebo hromadné vložení.

Následující diagram znázorňuje tok dat.

   ![Architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Sestavení Spark ke konektoru SQL DB
V současné době používá konektor projektu maven. Pokud chcete sestavit konektor bez závislosti, můžete spustit:

- Vyčištění mvn balíčku
- Stáhněte si nejnovější verze soubor JAR ze složky vydání
- Zahrnout soubor JAR SQL DB Sparku

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Připojení k databázi SQL pomocí konektoru Spark
Může připojit k Azure SQL Database nebo SQL Server z Sparkových úloh, číst nebo zapisovat data. Dotaz DML nebo DDL můžete také spustit v Azure SQL database nebo databáze systému SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Čtení dat z Azure SQL Database nebo SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Čtení dat ze služby Azure SQL Database nebo SQL Server s zadaný dotaz SQL
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
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zápis dat do Azure SQL Database nebo SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Spuštění dotazu DML nebo DDL v Azure SQL Database nebo SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Připojení k Azure SQL Database pomocí ověřování AAD Spark
Můžete připojit ke službě Azure SQL Database pomocí ověřování Azure Active Directory (AAD). Centrálně spravovat identity uživatelů databáze a jako alternativu k ověřování SQL serveru pomocí ověřování AAD.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Připojení pomocí ActiveDirectoryPassword režim ověřování
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování ActiveDirectoryPassword, budete muset stáhnout [azure-activedirectory knihovny pro java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesta sestavení Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Připojení pomocí přístupový Token
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování pomocí tokenu přístupu, budete muset stáhnout [azure-activedirectory knihovny pro java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesta sestavení Java.

Zobrazit [používání ověřování Azure Active Directory pro ověřování s využitím SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) k zjistěte, jak získat přístupový token k vaší databázi Azure SQL.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zápis dat do Azure SQL database nebo SQL serveru pomocí hromadné vložení
Konektor tradiční jdbc zapisuje data do Azure SQL database nebo SQL serveru pomocí vložení řádek po řádku. Spark pro konektor SQL DB slouží k zápisu dat do SQL database pomocí hromadné vložení. Výrazně zlepšuje výkon při zápisu při načítání velkých datových sad nebo načítání dat do tabulek, ve kterém se používá index úložiště sloupců.

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
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Další postup
Pokud jste tak dosud neučinili, stáhněte si konektor Spark pro Azure SQL Database a SQL Server z [úložiště GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) a další materiály v úložišti:

-   [Ukázka poznámkových bloků Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Ukázky skriptů (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Můžete také zkontrolovat [Apache Spark SQL, datových rámců a datových sad průvodce](http://spark.apache.org/docs/latest/sql-programming-guide.html) a [dokumentace k Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

