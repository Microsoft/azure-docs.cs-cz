---
title: Použití Javy k dotazování na databázi
description: Ukazuje, jak pomocí javy vytvořit program, který se připojí k databázi Azure SQL a dotaz ovat pomocí příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768640"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rychlý start: Použití Javy k dotazování databáze Azure SQL

V tomto rychlém startu použijete Javu k připojení k databázi Azure SQL a k dotazování dat použijete příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md)
- Software související s [Javou](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macos"></a>[Macos](#tab/macos)

  Nainstalujte Homebrew a Java, pak nainstalujte Maven pomocí kroků **1.2** a **1.3** v [vytvořit Java aplikace pomocí SQL Server na macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte Java, Java Development Kit, pak nainstalovat Maven pomocí kroků **1.2**, **1.3**a **1.4** v [Vytvořit Java aplikace pomocí SQL Server na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Nainstalujte Jazyk Java a pak nainstalujte Maven pomocí kroků **1.2** a **1.3** v [článku Vytvoření aplikací Java pomocí SQL Serveru v systému Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou zapsány k použití databáze **Adventure Works.**

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> Chcete-li vytvořit a nakonfigurovat, použijte [portál Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a nastavte připojení [na místě](sql-database-managed-instance-configure-p2s.md) nebo [v počítači.](sql-database-managed-instance-configure-vm.md)
>
> Chcete-li načíst data, viz [obnovení pomocí BACPAC](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo naleznete [v tématu obnovení databáze Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte **databáze SQL** nebo otevřete stránku **spravovaných instancí SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.** 

## <a name="create-the-project"></a>Vytvoření projektu

1. Z příkazového řádku vytvořte nový projekt Maven s názvem *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Změňte složku na *sqltest* a otevřete *pom.xml* pomocí oblíbeného textového editoru. Přidejte **ovladač Microsoft JDBC pro SQL Server** do závislostí projektu pomocí následujícího kódu.

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

## <a name="add-code-to-query-database"></a>Přidání kódu do databáze dotazů

1. Měli byste již mít soubor s názvem *App.java* ve vašem projektu Maven se nachází na adrese:

   *.. \sqltest\src\main\java\com\sqldbsamples\App.java*

1. Soubor otevřete a nahraďte jeho obsah následujícím kódem. Potom přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

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
   > Příklad kódu používá ukázkovou databázi **AdventureWorksLT** pro Azure SQL.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte aplikaci.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ověřte, zda je vráceno prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)  

- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Hlášení problémů / kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)  
