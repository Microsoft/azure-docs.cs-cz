---
title: Použití jazyka Java k dotazování databáze
description: Ukazuje, jak pomocí jazyka Java vytvořit program, který se připojí ke službě Azure SQL Database a provede dotaz pomocí příkazů T-SQL.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768640"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rychlý start: Použití Javy k dotazování databáze Azure SQL

V tomto rychlém startu pomocí Java se připojíte k databázi SQL Azure a použijete k dotazování dat příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze SQL Azure](sql-database-single-database-get-started.md)
- Software související s jazykem [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Nainstalujte homebrew a Java a pak nainstalujte Maven pomocí kroků **1,2** a **1,3** v [vytváření aplikací Java pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte Java, Java Development Kit a pak nainstalujte Maven pomocí kroků **1,2**, **1,3**a **1,4** v části [vytváření aplikací Java pomocí SQL Server v Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Nainstalujte Java a pak nainstalujte Maven pomocí kroků **1,2** a **1,3** v [vytváření aplikací Java pomocí SQL Server ve Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> K vytvoření a konfiguraci použijte [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a pak nastavte připojení [na pracovišti](sql-database-managed-instance-configure-p2s.md) nebo [virtuálním počítači](sql-database-managed-instance-configure-vm.md) .
>
> Pokud chcete načíst data, přečtěte si téma [Restore with BacPac](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo si přečtěte část [obnovení databáze World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).

2. Vyberte **databáze SQL** nebo otevřete stránku **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** . 

## <a name="create-the-project"></a>Vytvoření projektu

1. Z příkazového řádku vytvořte nový projekt Maven s názvem *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Změňte složku na *sqltest* a otevřete *pom. XML* v oblíbeném textovém editoru. Přidejte **ovladač Microsoft JDBC pro SQL Server** do závislostí projektu pomocí následujícího kódu.

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

1. V projektu Maven byste už měli mít soubor s názvem *App. Java* , který najdete tady:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Soubor otevřete a nahraďte jeho obsah následujícím kódem. Pak přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

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

1. Ověřte, že se vrátí prvních 20 řádků a okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)  

- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Hlášení problémů / kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)  
