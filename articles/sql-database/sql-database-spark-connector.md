---
title: Konektor s Azure SQL Database a SQL Server z Spark | Microsoft Docs
description: Další informace o použití konektoru Spark pro Azure SQL Database a SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: a422f65097466e4bbe5740c449d3ccf88701802b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650158"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Urychlit analýzy velkých objemů dat v reálném čase s konektorem Spark pro Azure SQL Database a SQL Server

Konektor Spark pro Azure SQL Database a SQL Server umožňuje databází SQL, včetně Azure SQL Database a SQL Server, tak, aby fungoval jako vstupní data zdroj nebo výstupní jímku dat pro úlohy Spark. Umožňuje využití dat v reálném čase transakcí v analýzy velkých objemů dat a zachovat výsledky pro ad hoc dotazy nebo generování sestav. Tento konektor, který je ve srovnání s integrovaného konektoru služby JDBC, poskytuje schopnost hromadné vložení dat do databáze SQL. Můžete ho překonat vložení řádku na řádek s 10 x 20 x vyšší výkon. Konektor Spark pro Azure SQL Database a SQL Server také podporuje ověřování AAD. Umožňuje bezpečné připojení k vaší databázi Azure SQL z Databricks Azure pomocí svého účtu AAD. Integrovaného konektoru služby JDBC poskytne podobné rozhraní. Je snadné migrovat své existující úlohy Spark chcete použít tento nový konektor.

## <a name="download"></a>Ke stažení
Abyste mohli začít, stáhněte Spark do konektor databáze SQL z [úložiště azure. sqldb spark](https://github.com/Azure/azure-sqldb-spark) na Githubu.

## <a name="official-supported-versions"></a>Oficiální podporované verze

| Komponenta                            |Verze                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 nebo novější           |
| Scala                                |2.10 nebo novější            |
| Ovladač Microsoft JDBC pro SQL Server |6.2 nebo novější             |
| Microsoft SQL Server                 |SQL Server 2008 nebo novější |
| Azure SQL Database                   |Podporováno                |

Konektor Spark pro Azure SQL Database a SQL Server využívá ovladač JDBC Microsoft pro systém SQL Server pro přesun dat mezi uzly pracovního procesu Spark a databází SQL:
 
Toku dat je následující:
1. Hlavní uzel Spark se připojí k serveru SQL Server nebo Azure SQL Database a načte data z určité tabulky nebo pomocí specifického dotazu SQL
2. Hlavní uzel Spark distribuuje data k pracovním uzlům pro transformaci. 
3. Pracovního uzlu se připojí k serveru SQL Server nebo Azure SQL Database a zapisuje data do databáze. Uživatel může vybrat řádek po řádku vložení nebo hromadné vložení.

Následující diagram znázorňuje tok dat.

   ![Architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Sestavení Spark ke konektoru SQL DB
V současné době používá konektor projekt maven. Pokud chcete vytvořit konektor bez závislosti, můžete spustit:

- Vyčištění balíček mvn
- Stažení nejnovější verze JAR ve složce verze
- Zahrnout SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Připojení k databázi SQL s použitím konektoru Spark
Můžete připojit k Azure SQL Database nebo SQL Server z úloh Spark, číst nebo zapisovat data. DML nebo DDL dotaz můžete také spustit v Azure SQL database nebo databáze systému SQL Server.

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Čtení dat z Azure SQL Database nebo SQL Server s zadaný dotaz SQL
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

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zapsat data do Azure SQL Database nebo SQL Server
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
Můžete připojit k databázi SQL Azure pomocí ověřování Azure Active Directory (AAD). Ověřování pomocí AAD centrálně spravovat identity uživatelů, databáze a jako alternativu k ověřování serveru SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Připojení pomocí režimu ověřování ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování ActiveDirectoryPassword, budete muset stáhnout [azure-Active Directory – knihovna pro java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesta sestavení Java.

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

### <a name="connecting-using-access-token"></a>Připojení pomocí tokenu přístupu
#### <a name="setup-requirement"></a>Požadavek na instalaci
Pokud používáte režim ověřování na základě tokenu přístupu, budete muset stáhnout [azure-Active Directory – knihovna pro java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a jeho závislosti a zahrnout je do cesta sestavení Java.

V tématu [použití Azure ověřování služby Active Directory pro ověřování s databází SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) se dozvíte, jak získat přístup k vaší databázi Azure SQL tokenu.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zapsat data do Azure SQL database nebo SQL Server pomocí hromadného vložení
Konektor tradiční jdbc zapisuje data do Azure SQL database nebo SQL Server pomocí vložení po řádcích. Spark k databázi SQL konektor slouží k zápisu dat do databáze SQL použít hromadné vložení. Výrazně zvyšuje výkon zápisu, při načítání velkých datových sad nebo načítání dat do tabulky, kde se používá index úložiště sloupců.

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
Pokud jste tak ještě neučinili, stáhněte si konektor Spark pro Azure SQL Database a SQL Server z [úložiště azure. sqldb spark GitHub](https://github.com/Azure/azure-sqldb-spark) a seznamte se s další prostředky v úložišti:

-   [Ukázka Azure Databricks poznámkových bloků](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Ukázkové skripty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Můžete také zkontrolovat [Apache Spark SQL, průvodce datové sady a DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) a [dokumentace Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

