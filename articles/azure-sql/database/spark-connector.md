---
title: Použití konektoru Spark s Microsoft Azure SQL a SQL Server
description: Naučte se používat konektor Spark s Azure SQL Database, spravovanou instancí Azure SQL a SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 36010ff0206ddf9dae08391eb6e4c3dd7762cc10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319329"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Urychlení analýzy velkých objemů dat v reálném čase pomocí konektoru Spark
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Od SEP 2020 není tento konektor aktivně udržován. [Apache Spark konektor pro SQL Server a Azure SQL](https://docs.microsoft.com/sql/connect/spark/connector) je teď k dispozici, ale podporuje vazby Pythonu a R, jednodušší použití rozhraní k hromadnému vkládání dat a mnoho dalších vylepšení. Důrazně doporučujeme, abyste tento nový konektor vyhodnotili a použili místo něj. Informace o starém konektoru (této stránce) jsou uchovávány pouze pro účely archivace.

Konektor Spark umožňuje používat databáze v Azure SQL Database, Azure SQL Managed instance a SQL Server fungovat jako vstupní zdroj dat nebo jímka výstupních dat pro úlohy Spark. Umožňuje využívat transakční data v reálném čase s analýzou velkých objemů dat a uchovávat výsledky pro dotazy nebo generování sestav ad hoc. V porovnání s integrovaným konektorem JDBC poskytuje tento konektor možnost hromadného vkládání dat do databáze. Může překoná vkládání po řádcích s 10x, aby 20krát rychlejší výkon. Konektor Spark podporuje ověřování Azure Active Directory (Azure AD) pro připojení k Azure SQL Database a spravované instanci SQL Azure, což vám umožní připojit vaši databázi od Azure Databricks pomocí svého účtu Azure AD. Poskytuje podobná rozhraní s integrovaným konektorem JDBC. K použití tohoto nového konektoru je snadné migrovat stávající úlohy Sparku.

## <a name="download-and-build-a-spark-connector"></a>Stažení a sestavení konektoru Spark

Úložiště GitHub pro starý konektor, na který se dřív připojilo z této stránky, se aktivně neudržuje. Místo toho důrazně doporučujeme, abyste tento [nový konektor](https://github.com/microsoft/sql-spark-connector)vyhodnotili a použili.

### <a name="official-supported-versions"></a>Oficiální podporované verze

| Součást                             | Verze                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 nebo novější           |
| Scala                                 | 2,10 nebo novější            |
| Ovladač Microsoft JDBC pro SQL Server  | 6,2 nebo novější             |
| Microsoft SQL Server                  | SQL Server 2008 nebo novější |
| Azure SQL Database                    | Podporováno                |
| Spravovaná instance Azure SQL            | Podporováno                |

Konektor Spark využívá ovladač Microsoft JDBC pro SQL Server k přesouvání dat mezi uzly Spark Worker a databázemi:

Tok dat je následující:

1. Uzel Spark Master se připojuje k databázím ve SQL Database nebo SQL Server a načítá data z konkrétní tabulky nebo pomocí konkrétního dotazu SQL.
2. Uzel Spark Master distribuuje data do uzlů pracovního procesu pro transformaci.
3. Pracovní uzel se připojuje k databázím, které se připojují k SQL Database a SQL Server a zapisuje data do databáze. Uživatel se může rozhodnout, že se má použít vkládání řádků nebo hromadného vložení.

Tok dat znázorňuje následující diagram.

   ![Diagram znázorňuje popsaný tok s hlavním uzlem připojujícím se přímo k databázi a připojuje se ke třem pracovním uzlům, které se připojují k databázi.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Sestavení konektoru Spark

V současné době projekt konektoru používá Maven. Pokud chcete konektor vytvořit bez závislostí, můžete spustit:

- MVN vyčistit balíček
- Stáhnout nejnovější verze JAR ze složky pro vydání
- Zahrnout SQL Database Spark JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>Připojení a čtení dat pomocí konektoru Spark

Můžete se připojit k databázím v SQL Database a SQL Server z úlohy Sparku pro čtení nebo zápis dat. Můžete také spustit dotaz DML nebo DDL v databázích v SQL Database a SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Čtení dat z Azure SQL a SQL Server

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

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Čtení dat z Azure SQL a SQL Server se zadaným dotazem SQL

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

### <a name="write-data-to-azure-sql-and-sql-server"></a>Zápis dat do Azure SQL a SQL Server

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

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Spuštění dotazu DML nebo DDL v Azure SQL a SQL Server

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

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Připojení ze Sparku pomocí ověřování Azure AD

Můžete se připojit k Azure SQL Database a spravované instanci SQL pomocí ověřování Azure AD. Pomocí ověřování Azure AD můžete centrálně spravovat identity uživatelů databáze a jako alternativu k ověřování SQL Server.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Připojení pomocí režimu ověřování ActiveDirectoryPassword

#### <a name="setup-requirement"></a>Požadavek na instalaci

Pokud používáte režim ověřování ActiveDirectoryPassword, musíte si stáhnout [Azure-Active-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a její závislosti a zahrnout je do cesty sestavení Java.

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

### <a name="connecting-using-an-access-token"></a>Připojení pomocí přístupového tokenu

#### <a name="setup-requirement"></a>Požadavek na instalaci

Pokud používáte režim ověřování na základě přístupového tokenu, musíte si stáhnout [Azure-Active-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) a její závislosti a zahrnout je do cesty sestavení Java.

V tématu [použití Azure Active Directory ověřování pro ověřování](authentication-aad-overview.md) se dozvíte, jak získat přístupový token do vaší databáze v Azure SQL Database nebo spravované instanci SQL Azure.

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

## <a name="write-data-using-bulk-insert"></a>Zápis dat pomocí hromadného vložení

Tradiční konektor JDBC zapisuje data do vaší databáze pomocí vkládání po řádcích. Konektor Spark můžete použít k zápisu dat do Azure SQL a SQL Server pomocí hromadného vložení. Významně zlepšuje výkon při zápisu při načítání velkých datových sad nebo načítání dat do tabulek, ve kterých se používá index úložiště sloupců.

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

Pokud jste to ještě neudělali, Stáhněte si konektor Spark z [úložiště GitHub Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) a Prozkoumejte další prostředky v úložišti:

- [Ukázka Azure Databricks poznámkových blocích](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Ukázkové skripty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Můžete také zkontrolovat [Apache Spark SQL, datové rámce a příručky datových sad](https://spark.apache.org/docs/latest/sql-programming-guide.html) a [dokumentaci k Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).
