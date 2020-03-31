---
title: 'Kurz: Přidání elastického fondu do skupiny převzetí služeb při selhání'
description: Přidejte elastický fond Azure SQL Database do skupiny s podporou převzetí služeb při selhání pomocí portálu Azure, PowerShellu nebo nastavení řízení příkazového příkazu Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268974"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání

Nakonfigurujte skupinu převzetí služeb při selhání pro elastický fond Azure SQL Database a otestujte převzetí služeb při selhání pomocí portálu Azure.  V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořte jednu databázi Azure SQL Database.
> - Přidejte jednu databázi do elastického fondu. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro dva elastické fondy mezi dvěma logickými servery SQL.
> - Otestujte převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.


## <a name="1---create-a-single-database"></a>1 - Vytvoření jedné databáze 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Přidání jedné databáze do elastického fondu
V tomto kroku vytvoříte elastický fond a přidáte do něj jednu databázi. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvořte elastický fond pomocí portálu Azure. 


1. V levé nabídce portálu Azure vyberte **Azure SQL.** Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Výběrem **možnosti + Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace o různých databázích můžete zobrazit výběrem možnosti Zobrazit podrobnosti na dlaždici Databáze.
1. V rozevíracím okně **Typ prostředku** vyberte **elastický fond** v rozbalovací masce Typ prostředku na **dlaždici Databáze SQL.** Vyberte **Vytvořit,** chcete-li vytvořit elastický fond. 

    ![Výběr elastického fondu](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Nakonfigurujte elastický fond s následujícími hodnotami:
   - **Název**: Zadejte jedinečný název elastického fondu, například `myElasticPool`. 
   - **Předplatné**: Vyberte si předplatné z rozevíracího souboru.
   - **ResourceGroup**: `myResourceGroup` Vyberte z rozevíracího souboru skupiny prostředků, kterou jste vytvořili v části 1. 
   - **Server**: V rozevíracím souboru vyberte server, který jste vytvořili v části 1.  

       ![Vytvoření nového serveru pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Výpočetní + úložiště:** Vyberte **Konfigurovat elastický fond** pro konfiguraci výpočetních prostředků, úložiště a přidejte do elastického fondu jednu databázi. Na kartě **Nastavení fondu** ponechte výchozí gen5 s 2 virtuálními jádry a 32 gb. 

1. Na stránce **Konfigurovat** vyberte kartu **Databáze** a pak zvolte **Přidat databázi**. Vyberte databázi, kterou jste vytvořili v části 1 a pak vyberte **Použít,** chcete-li ji přidat do elastického fondu. Vyberte **Použít** znovu, chcete-li použít nastavení elastického fondu a zavřete stránku **Konfigurovat.** 

    ![Přidání jazyka SQL DB do elastického fondu](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Vyberte **Revize + vytvořit,** chcete-li zkontrolovat nastavení elastického fondu, a pak vyberte **Vytvořit** a vytvořte elastický fond. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte elastické fondy a sekundární server pomocí PowerShellu. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase) | Nastaví vlastnosti databáze nebo přesune existující databázi do elastického fondu. | 

---

## <a name="3---create-the-failover-group"></a>3 - Vytvoření skupiny převzetí služeb při selhání 
V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem Azure SQL a novým serverem Azure SQL v jiné oblasti. Potom přidejte elastický fond do skupiny převzetí služeb při selhání. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvořte skupinu s podporou převzetí služeb při selhání pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte elastický fond vytvořený v `myElasticPool`předchozí části, například . 
1. V podokně **Přehled** vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.
  
    ![Otevřený server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu,** abyste vytvořili novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **Vytvořit**:
    - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny s podporou převzetí služeb při selhání, například `failovergrouptutorial`. 
    - **Sekundární server**: Vyberte možnost *konfigurace požadovaných nastavení* a pak zvolte vytvořit nový **server**. Případně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot pro nový sekundární server vyberte **vybrat**. 
        - **Název serveru**: Zadejte jedinečný název sekundárního `mysqlsecondary`serveru, například . 
        - **Přihlášení správce serveru**: Zadejte`azureuser`
        - **Heslo**: Zadejte složité heslo, které splňuje požadavky na heslo.
        - **Umístění**: Z rozbalovací nabídky zvolte `East US`umístění, například . Toto umístění nemůže být stejné jako primární server.

       > [!NOTE]
       > Nastavení přihlášení a brány firewall serveru se musí shodovat s nastavením primárního serveru. 
    
       ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Vyberte **Databáze ve skupině** a vyberte elastický fond, který jste vytvořili v oddíle 2. Mělo by se zobrazit upozornění s výzvou k vytvoření elastického fondu na sekundárním serveru. Vyberte upozornění a pak vyberte **OK,** chcete-li vytvořit elastický fond na sekundárním serveru. 
        
    ![Přidání elastického fondu do skupiny převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Vyberte **Vybrat,** chcete-li použít nastavení elastického fondu pro skupinu s podporou převzetí služeb při selhání, a pak vyberte **Vytvořit,** chcete-li vytvořit skupinu převzetí služeb při selhání. Přidáním elastického fondu do skupiny převzetí služeb při selhání se automaticky spustí proces geografické replikace.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu s podporou převzetí služeb při selhání pomocí Prostředí PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický server. | 
| [Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [Nová-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází Azure SQL do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam skupin y převzetí služeb při selhání Azure SQL Database. |

---


## <a name="4---test-failover"></a>4 - Zkušební převzetí služeb při selhání 
V tomto kroku se nezdaří vaše skupina převzetí služeb při selhání přejdete na sekundární server a potom poselháníní pomocí portálu Azure. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Otestujte převzetí služeb při selhání skupiny převzetí služeb při selhání pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte elastický fond vytvořený v `myElasticPool`předchozí části, například . 
1. Vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.

    ![Otevřený server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny s podporou převzetí služeb při selhání** a pak zvolte skupinu s podporou převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. Vyberte **převzetí služeb při selhání** z podokna úloh, chcete-li přepojit skupinu převzetí služeb při selhání obsahující elastický fond. 
1. Vyberte **Ano** na upozornění, které vás upozorní, že relace TDS budou odpojeny. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání obsahující databázi SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je primární, který server je sekundární. Pokud je převzetí služeb při selhání úspěšné, měly mít dva servery vyměněné role. 
1. Vyberte **převzetí služeb při selhání** znovu, chcete-li skupinu převzetí služeb při selhání vrátit zpět do původního nastavení. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otestujte převzetí služeb při selhání skupiny s podporou převzetí služeb při selhání pomocí prostředí PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Selhat skupiny převzetí služeb při selhání převést na sekundární server a potom zpět pomocí služby Poselhání z mocenek. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam skupin y převzetí služeb při selhání Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny Azure SQL Database převzetí služeb při selhání. |


---

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Vyčistěte prostředky odstraněním skupiny prostředků. 


# <a name="portal"></a>[Portál](#tab/azure-portal)


1. Přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com).
1. Vyberte **Odstranit skupinu prostředků,** chcete-li odstranit všechny prostředky ve skupině, stejně jako samotnou skupinu prostředků. 
1. Do textového pole zadejte název skupiny prostředků `myResourceGroup`, a pak vyberte **Odstranit,** chcete-li skupinu prostředků odstranit. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vyčistěte prostředky pomocí PowerShellu. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Tato část kurzu používá následující rutinu prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

---

> [!IMPORTANT]
> Pokud chcete zachovat skupinu prostředků, ale odstranit sekundární databázi, odeberte ji ze skupiny převzetí služeb při selhání před odstraněním. Odstranění sekundární databáze před odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování. 

## <a name="full-script"></a>Celý skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický server. | 
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou databázi Azure SQL Database. | 
| [Nový elastický fond AzSql](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase) | Nastaví vlastnosti databáze nebo přesune existující databázi do elastického fondu. | 
| [Nová-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází Azure SQL do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam skupin y převzetí služeb při selhání Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny Azure SQL Database převzetí služeb při selhání. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Pro portál Azure portal nejsou k dispozici žádné skripty.

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali elastický fond Azure SQL Database do skupiny převzetí služeb při selhání a otestovali převzetí služeb při selhání. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvořte jednu databázi Azure SQL Database.
> - Přidejte jednu databázi do elastického fondu. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro dva elastické fondy mezi dvěma logickými servery SQL.
> - Otestujte převzetí služeb při selhání.

Převést na další návod, jak migrovat pomocí DMS.

> [!div class="nextstepaction"]
> [Kurz: Migrace serveru SQL Server do sdružené databáze pomocí služby DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
