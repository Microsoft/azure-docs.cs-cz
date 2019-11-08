---
title: Konektor Spark s Azure SQL Database a SQL Server
description: Naučte se používat konektor Spark pro Azure SQL Database a SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: d398019b62078da6943cb98cbafc3ac39640513f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820878"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Urychlení analýzy velkých objemů dat v reálném čase pomocí konektoru Spark pro Azure SQL Database a SQL Server

Konektor Spark pro Azure SQL Database a SQL Server umožňuje, aby databáze SQL, včetně Azure SQL Database a SQL Server, sloužily jako vstupní zdroj dat nebo jímka výstupních dat pro úlohy Spark. Umožňuje využívat transakční data v reálném čase s analýzou velkých objemů dat a uchovávat výsledky pro dotazy a vytváření sestav v režimu ad hoc. V porovnání s integrovaným konektorem JDBC poskytuje tento konektor možnost hromadného vkládání dat do databází SQL. Může překoná řádek po vložení řádku s 10x, aby 20krát rychlejší výkon. Konektor Spark pro Azure SQL Database a SQL Server také podporuje ověřování AAD. Umožňuje zabezpečené připojení k databázi SQL Azure z Azure Databricks pomocí svého účtu AAD. Poskytuje podobná rozhraní s integrovaným konektorem JDBC. K použití tohoto nového konektoru je snadné migrovat stávající úlohy Sparku.

## <a name="download"></a>Stáhnout
Začněte tím, že si stáhnete konektor Spark do SQL DB z [úložiště Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) na GitHubu.

## <a name="official-supported-versions"></a>Oficiální podporované verze

| Komponenta                            |Verze                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 nebo novější           |
| Scala                                |2,10 nebo novější            |
| Ovladač Microsoft JDBC pro SQL Server |6,2 nebo novější             |
| Microsoft SQL Server                 |SQL Server 2008 nebo novější |
| Azure SQL Database                   |Podporuje se                |

Konektor Spark pro Azure SQL Database a SQL Server využívá ovladač Microsoft JDBC pro SQL Server přesun dat mezi uzly Spark Worker a databáze SQL:
 
Tok dat je následující:
1. Uzel Spark Master se připojuje k SQL Server nebo Azure SQL Database a načítá data z konkrétní tabulky nebo pomocí konkrétního dotazu SQL.
2. Uzel Spark Master distribuuje data do uzlů pracovního procesu pro transformaci. 
3. Pracovní uzel se připojuje k SQL Server nebo Azure SQL Database a zapisuje data do databáze. Uživatel se může rozhodnout, že se má použít vkládání řádků nebo hromadného vložení.

Tok dat znázorňuje následující diagram.

   ![Architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Vytvoření konektoru Spark na SQL DB
V současné době projekt konektoru používá Maven. Pokud chcete konektor vytvořit bez závislostí, můžete spustit:

- MVN vyčistit balíček
- Stáhnout nejnovější verze JAR ze složky pro vydání
- Zahrnutí SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Připojení Sparku k databázi SQL pomocí konektoru
Můžete se připojit k Azure SQL Database nebo SQL Server z úloh Spark, číst nebo zapisovat data. Můžete také spustit dotaz DML nebo DDL ve službě Azure SQL Database nebo v databázi SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Čtení dat z Azure SQL Database nebo SQL Server

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Čtení dat z Azure SQL Database nebo SQL Server se zadaným dotazem SQL
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
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Spustit dotaz DML nebo DDL v Azure SQL Database nebo SQL Server
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

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Připojení Sparku k Azure SQL Database pomocí ověřování AAD
K Azure SQL Database se můžete připojit pomocí ověřování Azure Active Directory (AAD). Ověřování AAD slouží k centrální správě identit uživatelů databáze a jako alternativu k ověřování SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Připojení pomocí režimu ověřování ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování ActiveDirectoryPassword, musíte si stáhnout [Azure-Active-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a její závislosti a zahrnout je do cesty sestavení Java.

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

### <a name="connecting-using-access-token"></a>Připojování pomocí přístupového tokenu
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování na základě přístupového tokenu, musíte si stáhnout [Azure-Active-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a její závislosti a zahrnout je do cesty sestavení Java.

Informace o tom, jak získat přístupový token ke službě Azure SQL Database, najdete v tématu [použití Azure Active Directory ověřování pro ověřování pomocí SQL Database](sql-database-aad-authentication.md) .

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zápis dat do služby Azure SQL Database nebo SQL Server pomocí hromadného vložení
Tradiční konektor JDBC zapisuje data do služby Azure SQL Database nebo SQL Server pomocí vkládání po řádcích. Spojnici Spark to SQL DB můžete použít k zápisu dat do SQL Database pomocí hromadného vložení. Významně zlepšuje výkon při zápisu při načítání velkých datových sad nebo načítání dat do tabulek, ve kterých se používá index úložiště sloupců.

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
Pokud jste to ještě neudělali, Stáhněte si konektor Spark pro Azure SQL Database a SQL Server z [úložiště GitHub Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) a Prozkoumejte další prostředky v úložišti:

-   [Ukázka Azure Databricks poznámkových blocích](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Ukázkové skripty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Můžete také zkontrolovat [Apache Spark SQL, datové rámce a příručky datových sad](https://spark.apache.org/docs/latest/sql-programming-guide.html) a [dokumentaci k Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

