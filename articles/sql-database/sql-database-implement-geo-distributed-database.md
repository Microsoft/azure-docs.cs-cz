---
title: Implementace řešení geograficky distribuované služby Azure SQL Database | Microsoft Docs
description: Zjistěte, jak u své služby Azure SQL Database a aplikace nakonfigurovat převzetí služeb při selhání do replikované databáze a otestovat ho.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 0fe24c22c42c826db28b6cee460936597b8de83c
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269240"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Kurz: Implementace geograficky distribuované databáze

V tomto kurzu nakonfigurujete u databáze SQL Azure a aplikace převzetí služeb při selhání do vzdálené oblasti a pak otestujete svůj plán převzetí služeb při selhání. Získáte informace o těchto tématech:

> [!div class="checklist"]
> - Vytvoření uživatelů databáze a udělení oprávnění
> - Nastavení pravidla brány firewall na úrovni databáze
> - Vytvoření [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md)
> - Vytvoření a kompilace aplikace v Javě pro dotazování databáze SQL Azure
> - Provedení postupu zotavení po havárii

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

- Máte nainstalovanou nejnovější verzi [Azure PowerShellu](https://docs.microsoft.com/powershell/azureps-cmdlets-docs).
- Máte nainstalovanou databázi SQL Azure. V tomto kurzu se používá ukázková databáze AdventureWorksLT s názvem **mySampleDatabase** z jednoho z těchto rychlých startů:

  - [Vytvoření databáze – portál](sql-database-get-started-portal.md)
  - [Vytvoření databáze – rozhraní příkazového řádku](sql-database-cli-samples.md)
  - [Vytvoření databáze – PowerShell](sql-database-powershell-samples.md)

- Identifikovali jste metodu provádění skriptů SQL proti databázi. Můžete použít některý z následujících nástrojů pro dotazování:
  - Editor dotazů na webu [Azure Portal](https://portal.azure.com). Další informace o používání editoru dotazů na webu Azure Portal najdete v tématu popisujícím [připojení a dotazování pomocí Editoru dotazů](sql-database-get-started-portal.md#query-the-sql-database).
  - Nejnovější verze aplikace [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), což je integrované prostředí pro správu jakékoliv infrastruktury SQL, od SQL Serveru po službu SQL Database pro Microsoft Windows.
  - Nejnovější verze [Visual Studio Code](https://code.visualstudio.com/docs), což je grafický editor kódu pro Linux, macOS a Windows, který podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Serveru a služeb Azure SQL Database a SQL Data Warehouse. Další informace o používání tohoto nástroje se službou Azure SQL Database najdete v tématu [Připojení a dotazování pomocí Visual Studio Code](sql-database-connect-query-vscode.md).

## <a name="create-database-users-and-grant-permissions"></a>Vytvoření uživatelů databáze a udělení oprávnění

Připojte se ke své databázi a vytvořte uživatelské účty pomocí některého z následujících nástrojů pro dotazování:

- Editor dotazů na webu Azure Portal
- SQL Server Management Studio
- Visual Studio Code

Tyto uživatelské účty se automaticky replikují na váš sekundární server (a udržují se synchronizované). Pokud chcete použít aplikaci SQL Server Management Studio nebo Visual Studio Code, možná budete muset nakonfigurovat pravidlo brány firewall, pokud se připojujete z klienta na IP adrese, pro kterou jste ještě nenakonfigurovali bránu firewall. Podrobný postup najdete v tématu popisujícím [vytvoření pravidla brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md).

- Spuštěním následujícího dotazu v okně dotazu vytvořte ve své databázi dva uživatelské účty. Tento skript udělí uživateli **db_owner** oprávnění k účtu **app_admin** a účtu **app_user** udělí oprávnění **SELECT** a **UPDATE**.

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin;
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Vytvoření brány firewall na úrovni databáze

Vytvořte pro svou databázi [pravidlo brány firewall na úrovni databáze](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). Toto pravidlo brány firewall na úrovni databáze se automaticky replikuje na sekundární server, který v tomto kurzu vytvoříte. Pro zjednodušení (v tomto kurzu) použijte veřejnou IP adresu počítače, na kterém provádíte kroky v tomto kurzu. Pokud chcete zjistit IP adresu použitou pro pravidlo brány firewall na úrovni serveru pro váš aktuální počítač, přečtěte si téma popisující [vytvoření brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md).  

- V otevřeném okně dotazu nahraďte předchozí dotaz následujícím dotazem, ve kterém nahraďte IP adresy odpovídajícími IP adresami pro vaše prostředí.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-a-failover-group"></a>Vytvořte skupinu převzetí služeb při selhání

Pomocí Azure Powershellu vytvořit [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existující server Azure SQL a novým prázdným serverem Azure SQL v oblasti Azure a pak přidejte svou ukázkovou databázi do skupiny převzetí služeb při selhání.

> [!IMPORTANT]
> Tyto rutiny vyžadují Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Do proměnných ve skriptech PowerShellu vyplňte hodnoty pro váš stávající server a ukázkovou databázi a jako název skupiny převzetí služeb při selhání zadejte globálně jedinečnou hodnotu.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Ve své oblasti pro převzetí služeb při selhání vytvořte prázdný záložní server.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver
   ```

3. Vytvořte skupinu převzetí služeb při selhání mezi těmito dvěma servery.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup
   ```

4. Přidejte do skupiny převzetí služeb při selhání svou databázi.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup
   ```

## <a name="install-java-software"></a>Instalace softwaru Java

Kroky v této části předpokládají, že máte zkušenosti s vývojem pomocí Javy a teprve začínáte pracovat se službou Azure SQL Database.

### <a name="mac-os"></a>Mac OS

Otevřete terminál a přejděte do adresáře, kde plánujete vytvoření projektu v Javě. Zadáním následujících příkazů nainstalujte **brew** a **Maven**:

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Podrobné pokyny k instalaci a konfiguraci prostředí Java a Maven najdete na webu [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Sestavení aplikace s použitím SQL Serveru), kde vyberte **Java**, pak **MacOS** a pak postupujte podle podrobných pokynů ke konfiguraci Javy a Mavenu v krocích 1.2 a 1.3.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

Otevřete terminál a přejděte do adresáře, kde plánujete vytvoření projektu v Javě. Zadáním následujících příkazů nainstalujte **Maven**:

```bash
sudo apt-get install maven
```

Podrobné pokyny k instalaci a konfiguraci prostředí Java a Maven najdete na webu [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Sestavení aplikace s použitím SQL Serveru), kde vyberte **Java**, pak **Ubuntu** a pak postupujte podle podrobných pokynů ke konfiguraci Javy a Mavenu v krocích 1.2, 1.3 a 1.4.

### <a name="windows"></a>Windows

Nainstalujte [Maven](https://maven.apache.org/download.cgi) pomocí oficiální instalační služby. Maven použijte jako pomoc při správě závislostí, sestavování, testování a spouštění vašeho projektu v Javě. Podrobné pokyny k instalaci a konfiguraci prostředí Java a Maven najdete na webu [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Sestavení aplikace s použitím SQL Serveru), kde vyberte **Java**, pak Windows a pak postupujte podle podrobných pokynů ke konfiguraci Javy a Mavenu v krocích 1.2 a 1.3.

## <a name="create-sqldbsample-project"></a>Vytvoření projektu SqlDbSample

1. V příkazové konzole (jako je například Bash) vytvořte projekt Maven.

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Zadejte **Y** a stiskněte **Enter**.
3. Přejděte do adresáře svého nově vytvořeného projektu.

   ```bash
   cd SqlDbSamples
   ```

4. Pomocí oblíbeného editoru otevřete soubor pom.xml ve složce projektu.

5. Přidejte do projektu Maven závislost ovladače Microsoft JDBC pro SQL Server tak, že otevřete oblíbený textový editor a zkopírujete následující řádky a vložíte je do souboru pom.xml. Nepřepisujte stávající hodnoty, které jsou v souboru již vyplněné. Závislost JDBC je potřeba vložit do větší části dependencies (závislosti).

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Zadejte verzi Javy, ve které se projekt zkompiluje, přidáním následující části properties (vlastnosti) do souboru pom.xml za část dependencies (závislosti).

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

7. Přidejte do souboru pom.xml následující část build (sestavení) za část properties (vlastnosti) pro zajištění podpory souborů manifestu v souborech .jar.

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

8. Soubor pom.xml uložte a zavřete.
9. Otevřete soubor App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) a nahraďte jeho obsah následujícím obsahem. Název skupiny převzetí služeb při selhání nahraďte názvem vaší skupiny převzetí služeb při selhání. Pokud jste změnili hodnoty pro název databáze, uživatele nebo heslo, změňte i tyto hodnoty.

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts
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

10. Soubor App.java uložte a zavřete.

## <a name="compile-and-run-the-sqldbsample-project"></a>Kompilace a spuštění projektu SqlDbSample

1. V příkazové konzole spusťte následující příkaz.

   ```bash
   mvn package
   ```

2. Po dokončení spusťte následující příkaz, kterým spustíte aplikaci (pokud ji ručně nezastavíte, bude spuštěná přibližně 1 hodinu):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"

   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Provedení postupu zotavení po havárii

1. Zavolejte ruční převzetí služeb při selhání skupiny převzetí služeb při selhání.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Během přebírání služeb při selhání sledujte výsledky aplikace. Některá vložení selžou, zatímco se aktualizuje mezipaměť DNS.

3. Zjistěte, jakou roli váš server pro zotavení po havárii provádí.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Navraťte služby po obnovení.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Během navracení služeb po obnovení sledujte výsledky aplikace. Některá vložení selžou, zatímco se aktualizuje mezipaměť DNS.

6. Zjistěte, jakou roli váš server pro zotavení po havárii provádí.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili nakonfigurovat u databáze SQL Azure a aplikace převzetí služeb při selhání do vzdálené oblasti a potom svůj plán převzetí služeb při selhání otestovat.  Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření uživatelů databáze a udělení oprávnění
> - Nastavení pravidla brány firewall na úrovni databáze
> - Vytvoření skupiny převzetí služeb při selhání geografické replikace
> - Vytvoření a kompilace aplikace v Javě pro dotazování databáze SQL Azure
> - Provedení postupu zotavení po havárii

Přejděte k dalšímu kurzu, kde migrace SQL serveru do Azure SQL Database Managed Instance pomocí DMS.

> [!div class="nextstepaction"]
>[Migrace SQL Serveru do Azure SQL Database Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md)
