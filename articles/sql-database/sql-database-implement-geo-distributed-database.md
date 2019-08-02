---
title: Implementace řešení geograficky distribuované databáze SQL Azure | Microsoft Docs
description: Naučte se konfigurovat službu Azure SQL Database a aplikaci pro převzetí služeb při selhání replikovanou databází a testovací převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4a21fe3ed15d1dc2550f6863611b27d2b36c5c51
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568092"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Kurz: Implementace geograficky distribuované databáze

Nakonfigurujte službu Azure SQL Database a aplikaci pro převzetí služeb při selhání do vzdálené oblasti a otestujte plán převzetí služeb při selhání. Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvoření [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md)
> - Spuštění aplikace Java pro dotazování databáze SQL Azure
> - Testovací převzetí služeb při selhání

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

K dokončení tohoto kurzu se ujistěte, že máte nainstalované následující položky:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Jedna databáze v Azure SQL Database. Chcete-li vytvořit jedno použití,
  - [Azure Portal](sql-database-single-database-get-started.md)
  - [Rozhraní příkazového řádku](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > V tomto kurzu se používá ukázková databáze *AdventureWorksLT* .

- Java a Maven, přečtěte si téma [Vytvoření aplikace pomocí SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), zvýraznění **Java** a výběr vašeho prostředí a pak postupujte podle pokynů.

> [!IMPORTANT]
> Nezapomeňte nastavit pravidla brány firewall tak, aby používala veřejnou IP adresu počítače, na kterém provedete kroky v tomto kurzu. Pravidla brány firewall na úrovni databáze se automaticky replikují na sekundární server.
>
> Informace najdete v tématu vytvoření [pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo určení IP adresy používané pro pravidlo brány firewall na úrovni serveru pro váš počítač v tématu [Vytvoření brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání

Pomocí Azure PowerShell vytvořit [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem SQL Azure a novým serverem SQL Azure v jiné oblasti. Pak přidejte ukázkovou databázi do skupiny převzetí služeb při selhání.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Chcete-li vytvořit skupinu převzetí služeb při selhání, spusťte následující skript:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Nastavení geografické replikace můžete v Azure Portal změnit také tak, že vyberete databázi a pak **Nastavení** > **geografická replikace**.

![Nastavení geografické replikace](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Spuštění ukázkového projektu

1. V konzole vytvořte projekt Maven pomocí následujícího příkazu:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Zadejte **Y** a stiskněte **ENTER**.

1. Změňte adresáře na nový projekt.

   ```bash
   cd SqlDbSample
   ```

1. Pomocí oblíbeného editoru otevřete soubor *pom. XML* ve složce projektu.

1. Přidejte ovladač Microsoft JDBC pro závislost SQL Server přidáním následující `dependency` části. Závislost musí být vložená v rámci větší `dependencies` části.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Zadejte verzi jazyka Java přidáním `properties` části `dependencies` za oddíl:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Podporu souborů manifestu přidáním `build` části `properties` za oddíl:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Uložte a zavřete soubor *pom. XML* .

1. Otevřete soubor *App. Java* umístěný v souboru.. \SqlDbSample\src\main\java\com\sqldbsamples a nahraďte obsah následujícím kódem:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Uložte a zavřete soubor *App. Java* .

1. V konzole příkazů spusťte následující příkaz:

   ```bash
   mvn package
   ```

1. Spusťte aplikaci, která bude běžet přibližně 1 hodinu až do ručního zastavení, což vám umožní spustit test převzetí služeb při selhání.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testovací převzetí služeb při selhání

Spusťte následující skripty pro simulaci převzetí služeb při selhání a sledujte výsledky aplikace. Všimněte si, že při migraci databáze dojde k selhání některých vložení a výběrů.

V průběhu testu můžete také zkontrolovat roli serveru pro zotavení po havárii pomocí následujícího příkazu:

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Testování převzetí služeb při selhání:

1. Spustit ruční převzetí služeb při selhání pro skupinu převzetí služeb při selhání:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali službu Azure SQL Database a aplikaci pro převzetí služeb při selhání do vzdálené oblasti a otestovali jste plán převzetí služeb při selhání. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření skupiny převzetí služeb při selhání geografické replikace
> - Spuštění aplikace Java pro dotazování databáze SQL Azure
> - Testovací převzetí služeb při selhání

Přejděte k dalšímu kurzu migrace pomocí DMS.

> [!div class="nextstepaction"]
> [Migrace SQL Server do Azure SQL Database Managed instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md)
