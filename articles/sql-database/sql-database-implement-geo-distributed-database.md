---
title: Implementace řešení geograficky distribuované databáze Azure SQL | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat databázi Azure SQL a aplikace převzetí služeb při selhání do replikované databáze a otestovat převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/10/2019
ms.openlocfilehash: 086e1957c68c5864997c9df9213a0c82cbf4c852
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560749"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Kurz: Implementace geograficky distribuované databáze

Konfigurace Azure SQL database a aplikace převzetí služeb při selhání do vzdálené oblasti a testovacího převzetí služeb při selhání plánu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvoření [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md)
> - Spuštění aplikace v Javě pro dotazování Azure SQL database
> - Testovací převzetí služeb při selhání

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu, ujistěte se, že jste nainstalovali následující položky:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Databázi SQL Azure. Vytvořte jedno použití
  - [Azure Portal](sql-database-get-started-portal.md)
  - [Rozhraní příkazového řádku](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > V tomto kurzu použijete *AdventureWorksLT* ukázkovou databázi.

- Java a Maven, najdete v části [vytvářet aplikace pomocí systému SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), zvýrazněte **Java** a vyberte vaše prostředí, postupujte podle pokynů.

> [!IMPORTANT]
> Ujistěte se, že nastavení pravidel brány firewall, chcete-li použít veřejnou IP adresu počítače, na kterém provádíte kroky v tomto kurzu. Databáze na úrovni firewall pravidla budou automaticky replikovat do sekundárního serveru.
>
> Informace naleznete v tématu [vytvořit pravidlo brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo zjistit IP adresu používanou pro pravidlo brány firewall na úrovni serveru pro váš počítač najdete v článku [vytvoření brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md).  

## <a name="create-a-failover-group"></a>Vytvořte skupinu převzetí služeb při selhání

Pomocí Azure Powershellu vytvořit [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serveru Azure SQL a nový server Azure SQL v jiné oblasti. Pak přidejte ukázkovou databázi do skupiny převzetí služeb při selhání.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Pokud chcete vytvořit skupinu převzetí služeb při selhání, spusťte následující skript:

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
    New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzureRMSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzureRmSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzureRmSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Nastavení geografické replikace lze také změnit na webu Azure Portal, vyberte databázi, potom **nastavení** > **geografickou replikaci**.

![Nastavení geografické replikace](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Spuštění ukázkového projektu

1. V konzole vytvořte projekt Maven pomocí následujícího příkazu:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Typ **Y** a stiskněte klávesu **Enter**.

1. Změňte adresář na nový projekt.

   ```bash
   cd SqlDbSample
   ```

1. Pomocí oblíbeného editoru, otevřete *pom.xml* souboru ve složce vašeho projektu.

1. Přidat ovladač Microsoft JDBC pro SQL Server závislost přidáním následujícího kódu `dependency` oddílu. Závislost je potřeba vložit do větší `dependencies` oddílu.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Zadejte verzi Javy tak, že přidáte `properties` části po `dependencies` části:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Podpora soubory manifestu tak, že přidáte `build` části po `properties` části:

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

1. Uložte a zavřete *pom.xml* souboru.

1. Otevřít *App.java* souboru je umístěn v.. \SqlDbSample\src\main\java\com\sqldbsamples a nahraďte jeho obsah následujícím kódem:

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

1. Uložte a zavřete *App.java* souboru.

1. V příkazové konzole spusťte následující příkaz:

   ```bash
   mvn package
   ```

1. Spuštění aplikace, který se spustí přibližně 1 hodinu až do ukončení ručně, což že je čas ke spuštění testu převzetí služeb při selhání.

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

Spuštěním následujících skriptů k simulaci převzetí služeb při selhání a sledujte výsledky aplikace. Všimněte si, jak některé vloží a vybere selžou během migrace databáze.

Role serveru pro obnovení po havárii můžete také zkontrolovat během testu pomocí následujícího příkazu:

   ```powershell
   (Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

K testování převzetí služeb při selhání:

1. Spusťte ruční převzetí služeb při selhání skupiny převzetí služeb při selhání:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Vrácení skupiny převzetí služeb při selhání zpět na primární server:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali Azure SQL database a aplikace převzetí služeb při selhání do vzdálené oblasti a otestovat převzetí služeb při selhání plánu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření skupiny převzetí služeb při selhání geografické replikace
> - Spuštění aplikace v Javě pro dotazování Azure SQL database
> - Testovací převzetí služeb při selhání

Přejděte k dalšímu kurzu, o tom, jak migrovat pomocí DMS.

> [!div class="nextstepaction"]
> [Migrace SQL serveru do Azure SQL database spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md)
