---
title: Použití Javy k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Ukazuje, jak pomocí Javy vytvořit program, který se připojí ke službě Azure SQL database a ji dotazovat s použitím příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: 3a036ac1260923a5030b8b0c3345482346c183fe
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563115"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rychlý start: Použití Javy k dotazování databáze SQL Azure

Tento článek ukazuje, jak používat [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) pro připojení k databázi Azure SQL. Pak můžete použít příkazy jazyka T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázce, ujistěte se, že jsou splněné následující požadavky:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- S jazykem Java nainstalovaný software pro váš operační systém:

  - **MacOS**, nainstalujte Homebrew a Javu a potom nainstalujte Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, nainstalujte Java, sady Java Development Kit, potom nainstalujte Maven. Viz [kroky 1.2, 1.3 a 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, nainstalujte Java a potom nainstalujte Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-database-connection"></a>Získání připojení k databázi

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>Vytvoření projektu

1. Z příkazového řádku, vytvořte nový projekt Maven s názvem *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Změnit složku, do které *sqltest* a otevřete *pom.xml* v oblíbeném textovém editoru. Přidat **ovladač Microsoft JDBC pro SQL Server** na závislosti svého projektu pomocí následujícího kódu.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Také do souboru *pom.xml* přidejte k projektu následující vlastnosti. Pokud nemáte část s vlastnostmi, můžete ji přidat po závislostech.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Soubor *pom.xml* uložte a zavřete.

## <a name="add-code-to-query-database"></a>Přidejte kód pro dotaz na databázi

1. Měli byste už soubor s názvem *App.java* projektu v Mavenu umístění:

   *.\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Soubor otevřete a nahraďte jeho obsah následujícím kódem. Přidejte příslušné hodnoty pro server, databázi, uživatele a heslo.

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
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

   > [!NOTE]
   > Příklad kódu používá **AdventureWorksLT** ukázkovou databázi SQL Azure.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte aplikaci.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ověření se vrátí prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)  

- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Hlášení problémů/kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)  
