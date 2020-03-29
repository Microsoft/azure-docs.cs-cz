---
title: Implementace geograficky distribuovaného řešení
description: Naučte se nakonfigurovat databázi Azure SQL a aplikaci pro převzetí služeb při selhání na replikovanou databázi a otestujte převzetí služeb při selhání.
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
ms.openlocfilehash: 58d5bd4a7f3087e11056354f7534c3c9dbebca3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067289"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Kurz: Implementace geograficky distribuované databáze

Nakonfigurujte databázi Azure SQL a aplikaci pro převzetí služeb při selhání do vzdálené oblasti a otestujte plán převzetí služeb při selhání. Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvoření [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md)
> - Spuštění aplikace Java pro dotazování na databázi Azure SQL
> - Testovací převzetí služeb při selhání

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Chcete-li kurz dokončit, zkontrolujte, zda jste nainstalovali následující položky:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Jedna databáze v Azure SQL Database. Chcete-li vytvořit jedno použití,
  - [Portál](sql-database-single-database-get-started.md)
  - [Cli](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Kurz používá ukázkovou databázi *AdventureWorksLT.*

- Java a Maven, viz [Vytvoření aplikace pomocí SQL Serveru](https://www.microsoft.com/sql-server/developer-get-started/), zvýrazněte **Java** a vyberte své prostředí, postupujte podle pokynů.

> [!IMPORTANT]
> Nezapomeňte nastavit pravidla brány firewall pro použití veřejné IP adresy počítače, ve kterém provádíte kroky v tomto kurzu. Pravidla brány firewall na úrovni databáze se automaticky replikují na sekundární server.
>
> Informace naleznete [v tématu Vytvoření pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo určení adresy IP použité pro pravidlo brány firewall na úrovni serveru pro váš počítač naleznete v [tématu Vytvoření brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).  

## <a name="create-a-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání

Pomocí Azure PowerShellu vytvořte [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem Azure SQL a novým serverem Azure SQL v jiné oblasti. Potom přidejte ukázkovou databázi do skupiny převzetí služeb při selhání.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Chcete-li vytvořit skupinu převzetí služeb při selhání, spusťte následující skript:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Spusťte `az login` přihlášení do Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Nastavení geografické replikace lze změnit také na webu Azure Portal, výběrem databáze a potom **nastavením** > **geografické replikace**.

![Nastavení geografické replikace](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Spuštění ukázkového projektu

1. V konzole vytvořte projekt Maven s následujícím příkazem:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Zadejte **Y** a stiskněte **klávesu Enter**.

1. Změňte adresáře na nový projekt.

   ```bash
   cd SqlDbSample
   ```

1. Pomocí oblíbeného editoru otevřete soubor *pom.xml* ve složce projektu.

1. Přidejte závislost microsoft jdbc driver pro SQL `dependency` Server přidáním následující části. Závislost musí být vložena do `dependencies` větší části.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Zadejte verzi Jazyka `properties` Java přidáním oddílu za `dependencies` oddíl:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Podpora souborů manifestu `build` přidáním `properties` oddílu za oddíl:

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

1. Uložte a zavřete soubor *pom.xml*.

1. Otevřete soubor *App.java* umístěný v .. \SQLDbSample\src\main\java\com\sqldbsamples a nahraďte obsah následujícím kódem:

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

1. Uložte a zavřete soubor *App.java.*

1. V příkazové konzole spusťte následující příkaz:

   ```bash
   mvn package
   ```

1. Spusťte aplikaci, která bude spuštěna přibližně 1 hodinu, dokud nebude ručně zastavena, což vám umožní spustit test převzetí služeb při selhání.

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

Spusťte následující skripty, abyste simulovali převzetí služeb při selhání a sledovali výsledky aplikace. Všimněte si, jak některé vloží a vybere se nezdaří během migrace databáze.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Roli serveru pro zotavení po havárii můžete zkontrolovat během testu pomocí následujícího příkazu:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Testování převzetí služeb při selhání:

1. Spusťte ruční převzetí služeb při selhání skupiny převzetí služeb při selhání:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Vrátit skupinu převzetí služeb při selhání zpět na primární server:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Roli serveru pro zotavení po havárii můžete zkontrolovat během testu pomocí následujícího příkazu:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Testování převzetí služeb při selhání:

1. Spusťte ruční převzetí služeb při selhání skupiny převzetí služeb při selhání:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Vrátit skupinu převzetí služeb při selhání zpět na primární server:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali databázi Azure SQL a aplikaci pro převzetí služeb při selhání do vzdálené oblasti a otestovali plán převzetí služeb při selhání. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření skupiny převzetí služeb při selhání geografické replikace
> - Spuštění aplikace Java pro dotazování na databázi Azure SQL
> - Testovací převzetí služeb při selhání

Převést na další návod, jak migrovat pomocí DMS.

> [!div class="nextstepaction"]
> [Migrace SQL Serveru do spravované instance databáze Azure SQL pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md)
