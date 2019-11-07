---
title: 'Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání '
description: Přidejte Azure SQL Database elastický fond do skupiny převzetí služeb při selhání pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: 980740c4417d1085d7c367b172005b10ce828e0e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690495"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání

Nakonfiguruje skupinu převzetí služeb při selhání pro elastický fond Azure SQL Database a testovací převzetí služeb při selhání pomocí Azure Portal.  V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi.
> - Přidejte do elastického fondu jednu databázi. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro dva elastické fondy mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1\. vytvoření samostatné databáze 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 – přidat izolovanou databázi do elastického fondu
V tomto kroku vytvoříte elastický fond a přidáte do něj jednu databázi. 


# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte elastický fond pomocí Azure Portal. 


1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici databáze vyberete Zobrazit podrobnosti.
1. V rozevíracím seznamu **typ prostředku** na dlaždici **databáze SQL** vyberte **elastický fond** . Vyberte **vytvořit** a vytvořte tak elastický fond. 

    ![Vybrat elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Nastavte elastický fond s následujícími hodnotami:
   - **Název**: Zadejte jedinečný název elastického fondu, například `myElasticPool`. 
   - **Předplatné**: v rozevíracím seznamu vyberte své předplatné.
   - Skupina **prostředků: v**rozevíracím seznamu vyberte možnost `myResourceGroup`, skupiny prostředků, kterou jste vytvořili v části 1. 
   - **Server**: vyberte server, který jste vytvořili v části 1, z rozevíracího seznamu.  

       ![Vytvořit nový server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **COMPUTE + úložiště**: vyberte **Konfigurovat elastický fond** pro konfiguraci výpočetních prostředků, úložiště a přidejte svoji databázi do elastického fondu. Na kartě **nastavení fondu** ponechte výchozí hodnotu Gen5 se 2 virtuální jádra a 32 GB. 

1. Na stránce **Konfigurace** vyberte kartu **databáze** a pak zvolte možnost **Přidat databázi**. Zvolte databázi, kterou jste vytvořili v části 1, a pak vyberte **použít** pro přidání do elastického fondu. Pokud chcete použít nastavení elastického fondu a zavřít stránku **Konfigurace** , vyberte **použít** znovu. 

    ![Přidat databázi SQL do elastického fondu](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení elastického fondu a potom vyberte **vytvořit** a vytvořte tak elastický fond. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte své elastické fondy a sekundární server pomocí PowerShellu. 

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Nastaví vlastnosti pro databázi nebo přesune existující databázi do elastického fondu. | 

---

## <a name="3---create-the-failover-group"></a>3\. Vytvoření skupiny převzetí služeb při selhání 
V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem SQL Azure a novým serverem SQL Azure v jiné oblasti. Pak přidejte elastický fond do skupiny převzetí služeb při selhání. 


# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání pomocí Azure Portal. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte elastický fond vytvořený v předchozí části, například `myElasticPool`. 
1. V podokně **Přehled** vyberte název serveru v části **název serveru** a otevřete tak nastavení serveru.
  
    ![Otevřít server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **vytvořit**:
    - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny převzetí služeb při selhání, například `failovergrouptutorial`. 
    - **Sekundární server**: vyberte možnost *Konfigurace požadovaných nastavení* a pak zvolte **Vytvoření nového serveru**. Alternativně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot pro nový sekundární server vyberte **Vybrat**. 
        - **Název serveru**: Zadejte jedinečný název sekundárního serveru, například `mysqlsecondary`. 
        - **Přihlašovací jméno správce serveru**: zadejte `azureuser`
        - **Heslo**: zadejte komplexní heslo, které splňuje požadavky na heslo.
        - **Umístění**: vyberte umístění z rozevíracího seznamu, například `East US`. Toto umístění nemůže být stejné jako primární server.

       > [!NOTE]
       > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru. 
    
       ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Vyberte **databáze ve skupině** a potom vyberte elastický fond, který jste vytvořili v části 2. Mělo by se zobrazit upozornění, které vás vyzve k vytvoření elastického fondu na sekundárním serveru. Vyberte upozornění a pak vyberte **OK** a vytvořte elastický fond na sekundárním serveru. 
        
    ![Přidat elastický fond do skupiny převzetí služeb při selhání](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Vyberte **možnost vyberte** , pokud chcete nastavení elastického fondu použít pro skupinu převzetí služeb při selhání, a pak vyberte **vytvořit** a vytvořte skupinu převzetí služeb při selhání. Přidáním elastického fondu do skupiny převzetí služeb při selhání se automaticky spustí proces geografické replikace.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání pomocí PowerShellu. 

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický Server. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází SQL Azure do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam Azure SQL Database skupin převzetí služeb při selhání. |

---


## <a name="4---test-failover"></a>4\. testovací převzetí služeb při selhání 
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 


# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Otestujte převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí Azure Portal. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte elastický fond vytvořený v předchozí části, například `myElasticPool`. 
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

    ![Otevřít server pro elastický fond](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. V podokně úloh vyberte **převzetí služeb při** selhání pro skupinu převzetí služeb při selhání, která obsahuje váš elastický fond. 
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** . 

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaše databáze SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně převedlo, musí mít dva servery zaměnitelné role. 
1. Znovu vyberte **převzetí služeb při** selhání, abyste skupinu převzetí služeb při selhání mohli obnovit původní nastavení. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Testovací převzetí služeb při selhání ve skupině převzetí služeb při selhání pomocí PowerShellu 

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

Převzetí služeb při selhání pro skupinu převezmete do sekundárního serveru a pak navrácení služeb po obnovení pomocí PowerShellu. 

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam Azure SQL Database skupin převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny převzetí služeb při selhání Azure SQL Database. |


---

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Vyčistěte prostředky odstraněním skupiny prostředků. 


# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)


1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com).
1. Vyberte **Odstranit skupinu prostředků** a odstraňte všechny prostředky ve skupině a také samotnou skupinu prostředků. 
1. Do textového pole zadejte název skupiny prostředků, `myResourceGroup`, a pak výběrem **Odstranit** odstraňte skupinu prostředků. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Vyčistěte prostředky pomocí PowerShellu. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```
---

Tato část kurzu používá následující rutinu PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

## <a name="full-script"></a>Celý skript

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický Server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou Azure SQL Database jedinou databázi. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Vytvoří fond elastické databáze pro Azure SQL Database.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Nastaví vlastnosti pro databázi nebo přesune existující databázi do elastického fondu. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází SQL Azure do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam Azure SQL Database skupin převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny převzetí služeb při selhání Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 


# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Pro Azure Portal nejsou k dispozici žádné skripty.

---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do skupiny převzetí služeb při selhání přidali elastický fond Azure SQL Database a otestujete převzetí služeb při selhání. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi.
> - Přidejte do elastického fondu jednu databázi. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro dva elastické fondy mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

Přejděte k dalšímu kurzu migrace pomocí DMS.

> [!div class="nextstepaction"]
> [Kurz: migrace SQL Server do fondu databáze pomocí DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
