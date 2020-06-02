---
title: Použití jazyka Java k dotazování databáze
description: Ukazuje, jak pomocí jazyka Java vytvořit program, který se připojí k databázi ve službě Azure SQL Database nebo Azure SQL Managed instance a provede dotaz pomocí příkazů T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267388"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: použití jazyka Java k dotazování databáze v Azure SQL Database nebo spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto rychlém startu použijete Java pro připojení k databázi ve službě Azure SQL Database nebo Azure SQL Managed instance a k dotazování na data použijeme příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || Databáze SQL | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure |
  |:--- |:--- |:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z místního prostředí](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) | [Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) |
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- Software související s jazykem [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macos"></a>[macOS](#tab/macos)

  Nainstalujte homebrew a Java a pak nainstalujte Maven pomocí kroků **1,2** a **1,3** v [vytváření aplikací Java pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte Java, nainstalujte sadu Java Development Kit a pak nainstalujte Maven pomocí kroků **1,2**, **1,3**a **1,4** v části [vytváření aplikací Java pomocí SQL Server v Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Nainstalujte Java a pak nainstalujte Maven pomocí kroků **1,2** a **1,3** v [vytváření aplikací Java pomocí SQL Server ve Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> K vytvoření a konfiguraci použijte [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a pak nastavte [místní nebo](../managed-instance/point-to-site-p2s-configure.md) konektivitu [virtuálních počítačů](../managed-instance/connect-vm-instance-configure.md) .
>
> Pokud chcete načíst data, přečtěte si téma [Restore with BacPac](database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo si přečtěte část [obnovení databáze World Importers](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **databáze SQL** nebo otevřete stránku **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

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

## <a name="add-code-to-query-the-database"></a>Přidání kódu pro dotaz do databáze

1. V projektu Maven byste už měli mít soubor s názvem *App. Java* , který najdete tady:

   *.. \sqltest\src\main\java\com\sqldbsamples\App.java*

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
   > Příklad kódu používá ukázkovou databázi **AdventureWorksLT** v Azure SQL Database.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte aplikaci.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ověřte, zda je vráceno prvních 20 řádků a okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze v Azure SQL Database](design-first-database-tutorial.md)  
- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Hlášení problémů / kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)  
