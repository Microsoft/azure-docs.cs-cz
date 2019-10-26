---
title: 'Kurz: Přidání jedné databáze Azure SQL Database do skupiny převzetí služeb při selhání | Microsoft Docs'
description: Přidejte do skupiny převzetí služeb při selhání jednu databázi Azure SQL Database pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 106351487980d2f76e9122bc7423114e65593b15
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933254"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Kurz: Přidání samostatné databáze Azure SQL Database do skupiny převzetí služeb při selhání

Nakonfiguruje skupinu převzetí služeb při selhání pro Azure SQL Database izolovanou databázi a testovací převzetí služeb při selhání pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.  V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi.
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro jednu databázi mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

## <a name="prerequisites"></a>Předpoklady

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- Nejnovější verze rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1\. vytvoření samostatné databáze 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – Vytvoření skupiny převzetí služeb při selhání 
V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem SQL Azure a novým serverem SQL Azure v jiné oblasti. Pak přidejte ukázkovou databázi do skupiny převzetí služeb při selhání. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Vytvořte skupinu převzetí služeb při selhání a přidejte do ní jednu databázi pomocí Azure Portal. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte jednu databázi vytvořenou v oddílu 1, například `mySampleDatabase`. 
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

   ![Otevřít server pro jednu databázi](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **vytvořit**:
    - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny převzetí služeb při selhání, například `failovergrouptutorial`. 
    - **Sekundární server**: vyberte možnost *Konfigurace požadovaných nastavení* a pak zvolte **Vytvoření nového serveru**. Alternativně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot vyberte **Vybrat**. 
        - **Název serveru**: Zadejte jedinečný název sekundárního serveru, například `mysqlsecondary`. 
        - **Přihlašovací jméno správce serveru**: zadejte `azureuser`
        - **Heslo**: zadejte komplexní heslo, které splňuje požadavky na heslo.
        - **Umístění**: vyberte umístění z rozevíracího seznamu, například `East US`. Toto umístění nemůže být stejné jako primární server.

    > [!NOTE]
    > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru. 
    
      ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databáze v rámci skupiny**: po výběru sekundárního serveru se tato možnost odemkne. Vyberte ho a vyberte databáze, které **chcete přidat** , a pak vyberte databázi, kterou jste vytvořili v části 1. Když přidáte databázi do skupiny převzetí služeb při selhání, automaticky se spustí proces geografické replikace. 
        
    ![Přidat databázi SQL do skupiny převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte skupinu převzetí služeb při selhání a přidejte do ní jednu databázi pomocí PowerShellu. 

   > [!NOTE]
   > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický Server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou Azure SQL Database jedinou databázi. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází SQL Azure do skupiny převzetí služeb při selhání. |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Vytvořte skupinu převzetí služeb při selhání a přidejte do ní samostatnou databázi pomocí AZ CLI. 

   > [!NOTE]
   > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $drServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

Tato část kurzu používá následující příkaz AZ CLI rutin:

| Příkaz | Poznámky |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. | 
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. | 

---

## <a name="3---test-failover"></a>3\. testování převzetí služeb při selhání 
V tomto kroku dojde k selhání skupiny převzetí služeb při selhání pro sekundární server a následnému navrácení služeb po obnovení pomocí Azure Portal. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Testovací převzetí služeb při selhání pomocí Azure Portal. 

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
1. Vyberte jednu databázi vytvořenou v části 2, například `mySampleDatbase`. 
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

   ![Otevřít server pro jednu databázi](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. V podokně úloh vyberte **převzetí** služeb při selhání pro skupinu převzetí služeb při selhání, která obsahuje vaši ukázkovou jedinou databázi. 
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** . 

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaše databáze SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je teď primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně zdařilo, dva servery by měly mít zaměnitelné role. 
1. Znovu vyberte **převzetí služeb při selhání** , aby se servery převedly zpátky na původní role. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Testovací převzetí služeb při selhání pomocí PowerShellu 


Ověřte roli sekundární repliky: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Převzetí služeb při selhání sekundárním serverem: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam Azure SQL Database skupin převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny převzetí služeb při selhání Azure SQL Database. |



# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Testovací převzetí služeb při selhání pomocí AZ CLI. 

Ověřte, který server je sekundární:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Převzetí služeb při selhání sekundárním serverem: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

Tato část kurzu používá následující příkaz AZ CLI rutin:

| Příkaz | Poznámky |
|---|---|
| [AZ SQL Failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání pomocí převzetí služeb při selhání všemi databázemi z aktuálního primárního serveru. | 

---

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Vyčistěte prostředky odstraněním skupiny prostředků. 

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Odstraňte skupinu prostředků pomocí Azure Portal. 

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com).
1. Vyberte **Odstranit skupinu prostředků** a odstraňte všechny prostředky ve skupině a také samotnou skupinu prostředků. 
1. Do textového pole zadejte název skupiny prostředků, `myResourceGroup`, a pak výběrem **Odstranit** odstraňte skupinu prostředků.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Odstraňte skupinu prostředků pomocí PowerShellu. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Odstraňte skupinu prostředků pomocí AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

Tato část kurzu používá následující příkaz AZ CLI rutin:

| Příkaz | Poznámky |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

---


## <a name="full-scripts"></a>Úplné skripty

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický Server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou Azure SQL Database jedinou databázi. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází SQL Azure do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam Azure SQL Database skupin převzetí služeb při selhání. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny převzetí služeb při selhání Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Nastaví předplatné jako aktuální aktivní předplatné. | 
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Vytvoří databázi. | 
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. | 
| [AZ SQL Failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání pomocí převzetí služeb při selhání všemi databázemi z aktuálního primárního serveru. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Pro Azure Portal nejsou k dispozici žádné skripty. 
 
---

Další Azure SQL Database skripty můžete najít tady: [Azure PowerShell](sql-database-powershell-samples.md) a [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do skupiny převzetí služeb při selhání přidali izolovanou databázi Azure SQL Database a otestujete převzetí služeb při selhání. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> - Vytvořte Azure SQL Database izolovanou databázi. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro jednu databázi mezi dvěma logickými servery SQL.
> - Testovací převzetí služeb při selhání.

Přejděte k dalšímu kurzu, jak přidat elastický fond do skupiny převzetí služeb při selhání. 

> [!div class="nextstepaction"]
> [Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md)
