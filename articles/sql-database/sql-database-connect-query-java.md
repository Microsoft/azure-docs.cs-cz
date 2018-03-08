---
title: "Použití Javy k dotazování služby Azure SQL Database | Dokumentace Microsoftu"
description: "Toto téma vám ukáže, jak pomocí Javy vytvořit program, který se připojí ke službě Azure SQL Database a bude ji dotazovat s použitím příkazů jazyka Transact-SQL."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 07/11/2017
ms.author: andrela
ms.openlocfilehash: 7de8a1e19de1a72dfc02d726447270fc1ee047d1
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Použití Javy k dotazování databáze SQL Azure

Tento rychlý start ukazuje použití [Javy](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) pro připojení k databázi SQL Azure a následné použití příkazů jazyka Transact-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý úvodní kurz, ujistěte se, že máte následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pro veřejnou IP adresu počítače, který používáte pro tento rychlý úvodní kurz.

- Máte nainstalovanou Javu a související software pro váš operační systém:

    - **MacOS:** Nainstalujte Homebrew a Javu a potom nainstalujte Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu:** Nainstalujte Java Development Kit a Maven. Viz [kroky 1.2, 1.3 a 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows:** Nainstalujte Java Development Kit a Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Informace o připojení k SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-maven-project-and-dependencies"></a>**Vytvoření projektu a závislostí v Mavenu**
1. Na terminálu vytvořte nový projekt v Mavenu s názvem **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Po zobrazení výzvy zadejte **Y**.
3. Změňte adresář na **sqltest** a v oblíbeném textovém editoru otevřete soubor ***pom.xml***.  Pomocí následujícího kódu přidejte k závislostem projektu **Ovladač Microsoft JDBC pro SQL Server**:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.4.0.jre8</version>
   </dependency>
   ```

4. Také do souboru ***pom.xml*** přidejte k projektu následující vlastnosti.  Pokud nemáte část s vlastnostmi, můžete ji přidat po závislostech.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Soubor ***pom.xml*** uložte a zavřete.

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. V projektu v Mavenu už byste měli mít soubor ***App.java*** umístěný v: ..\sqltest\src\main\java\com\sqlsamples\App.java

2. Otevřete tento soubor, jeho obsah nahraďte následujícím kódem a přidejte odpovídající hodnoty pro váš server, databázi, uživatele a heslo.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.DriverManager;

   public class App {

    public static void main(String[] args) {
    
        // Connect to database
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
           Connection connection = null;

           try {
                   connection = DriverManager.getConnection(url);
                   String schema = connection.getSchema();
                   System.out.println("Successful connection - Schema: " + schema);

                   System.out.println("Query data example:");
                   System.out.println("=========================================");

                   // Create and execute a SELECT SQL statement.
                   String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                       + "FROM [SalesLT].[ProductCategory] pc "  
                       + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. V příkazovém řádku spusťte následující příkazy:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Ověřte, že se vrátilo prvních 20 řádků, a potom zavřete okno aplikace.


## <a name="next-steps"></a>Další kroky
- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Hlášení problémů/kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)

