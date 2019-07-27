---
title: Použití Javy k dotazování služby Azure SQL Database | Dokumentace Microsoftu
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
ms.openlocfilehash: d463851d40192443c0c02bb8452e2251592072a1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569266"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rychlý start: Použití Javy k dotazování databáze Azure SQL

Tento článek ukazuje, jak se pomocí [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) připojit k databázi SQL Azure. Pak můžete použít příkazy T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázky se ujistěte, že máte následující požadavky:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Create| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z webu](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  
  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- Software týkající se Java nainstalovaný pro váš operační systém:

  - **MacOS**, nainstalujte homebrew a Java a pak nainstalujte Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, nainstalujte Java, Java Development Kit a pak nainstalujte Maven. Viz [kroky 1.2, 1.3 a 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, nainstalujte Java a pak nainstalujte Maven. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

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

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)  

- [Ovladač Microsoft JDBC pro SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Hlášení problémů/kladení dotazů](https://github.com/microsoft/mssql-jdbc/issues)  
