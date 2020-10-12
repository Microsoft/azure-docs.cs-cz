---
title: Implementace geograficky distribuovaného řešení
description: Naučte se konfigurovat databázi v Azure SQL Database a klientské aplikace pro převzetí služeb při selhání replikovanou databází a testovací převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 8e64e1a63b01e900988c999321423dfe92df00c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335054"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Kurz: implementace geografické distribuované databáze (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Nakonfigurujte databázi v SQL Database a klientské aplikace pro převzetí služeb při selhání do vzdálené oblasti a otestujte plán převzetí služeb při selhání. Získáte informace o těchto tématech:

> [!div class="checklist"]
>
> - Vytvoření [skupiny převzetí služeb při selhání](auto-failover-group-overview.md)
> - Spuštění aplikace Java pro dotazování databáze v SQL Database
> - Testovací převzetí služeb při selhání

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

K dokončení tohoto kurzu se ujistěte, že máte nainstalované následující položky:

- [Azure PowerShell](/powershell/azure/)
- Jedna databáze v Azure SQL Database. Chcete-li vytvořit jedno použití,
  - [Portál Azure](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > V tomto kurzu se používá ukázková databáze *AdventureWorksLT* .

- Java a Maven, přečtěte si téma [Vytvoření aplikace pomocí SQL Server](https://www.microsoft.com/sql-server/developer-get-started/), zvýraznění **Java** a výběr vašeho prostředí a pak postupujte podle pokynů.

> [!IMPORTANT]
> Nezapomeňte nastavit pravidla brány firewall tak, aby používala veřejnou IP adresu počítače, na kterém provedete kroky v tomto kurzu. Pravidla brány firewall na úrovni databáze se automaticky replikují na sekundární server.
>
> Informace najdete v tématu vytvoření [pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo určení IP adresy používané pro pravidlo brány firewall na úrovni serveru pro váš počítač v tématu [Vytvoření brány firewall na úrovni serveru](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Vytvoření skupiny převzetí služeb při selhání

Pomocí Azure PowerShell vytvořte [skupiny převzetí služeb při selhání](auto-failover-group-overview.md) mezi existujícím serverem a novým serverem v jiné oblasti. Pak přidejte ukázkovou databázi do skupiny převzetí služeb při selhání.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Spusťte `az login` , abyste se přihlásili do Azure.

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

Nastavení geografické replikace můžete v Azure Portal změnit také tak, že vyberete databázi a pak **Nastavení**  >  **geografická replikace**.

![Nastavení geografické replikace](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

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

1. Pomocí oblíbeného editoru otevřete soubor *pom.xml* ve složce projektu.

1. Přidejte ovladač Microsoft JDBC pro závislost SQL Server přidáním následující `dependency` části. Závislost musí být vložená v rámci větší `dependencies` části.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Zadejte verzi jazyka Java přidáním `properties` části za `dependencies` oddíl:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Podporu souborů manifestu přidáním `build` části za `properties` oddíl:

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Roli serveru pro zotavení po havárii můžete zkontrolovat během testu pomocí následujícího příkazu:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Testování převzetí služeb při selhání:

1. Spustit ruční převzetí služeb při selhání pro skupinu převzetí služeb při selhání:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Roli serveru pro zotavení po havárii můžete zkontrolovat během testu pomocí následujícího příkazu:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Testování převzetí služeb při selhání:

1. Spustit ruční převzetí služeb při selhání pro skupinu převzetí služeb při selhání:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali databázi v Azure SQL Database a aplikaci pro převzetí služeb při selhání do vzdálené oblasti a otestovali jste plán převzetí služeb při selhání. Naučili jste se:

> [!div class="checklist"]
>
> - Vytvoření skupiny převzetí služeb při selhání geografické replikace
> - Spuštění aplikace Java pro dotazování databáze v SQL Database
> - Testovací převzetí služeb při selhání

Přejděte k dalšímu kurzu o přidání instance spravované instance Azure SQL do skupiny převzetí služeb při selhání:

> [!div class="nextstepaction"]
> [Přidání instance spravované instance Azure SQL do skupiny převzetí služeb při selhání](../managed-instance/failover-group-add-instance-tutorial.md)
