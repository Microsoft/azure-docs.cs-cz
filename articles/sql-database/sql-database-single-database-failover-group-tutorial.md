---
title: 'Kurz: Přidání jedné databáze do skupiny převzetí služeb při selhání'
description: Přidejte jednu databázi Azure SQL Database do skupiny s podporou převzetí služeb při selhání pomocí portálu Azure, PowerShellu nebo nastavení příkazového příkazu k Azure.
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
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061696"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Kurz: Přidání jedné databáze Azure SQL Database do skupiny převzetí služeb při selhání

[Skupina převzetí služeb při selhání](sql-database-auto-failover-group.md) je deklarativní vrstva abstrakce, která umožňuje seskupit více geograficky replikovaných databází. Naučte se nakonfigurovat skupinu převzetí služeb při selhání pro jednu databázi Azure SQL Database a otestujte převzetí služeb při selhání pomocí portálu Azure, PowerShellu nebo rozhraní příkazového příkazu k Azure.  V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořte jednu databázi Azure SQL Database.
> - Vytvořte skupinu převzetí služeb při selhání pro jednu databázi mezi dvěma logickými servery SQL.
> - Otestujte převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Portál](#tab/azure-portal)
Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující: 

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Chcete-li dokončit kurz, ujistěte se, že máte následující položky:

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Chcete-li dokončit kurz, ujistěte se, že máte následující položky:

- Předplatné Azure. [Vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) pokud ho ještě nemáte.
- Nejnovější verze [azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Vytvoření jedné databáze 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Vytvoření skupiny převzetí služeb při selhání 
V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) mezi existujícím serverem Azure SQL a novým serverem Azure SQL v jiné oblasti. Potom přidejte ukázkovou databázi do skupiny převzetí služeb při selhání. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte jednu databázi vytvořenou `mySampleDatabase`v oddíle 1, například . 
1. Skupiny převzetí služeb při selhání lze konfigurovat na úrovni serveru. Vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.

   ![Otevřít server pro jeden db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu,** abyste vytvořili novou skupinu převzetí služeb při selhání. 

    ![Přidat novou skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **Vytvořit**:
    - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny s podporou převzetí služeb při selhání, například `failovergrouptutorial`. 
    - **Sekundární server**: Vyberte možnost *konfigurace požadovaných nastavení* a pak zvolte vytvořit nový **server**. Případně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot vyberte **vybrat**. 
        - **Název serveru**: Zadejte jedinečný název sekundárního `mysqlsecondary`serveru, například . 
        - **Přihlášení správce serveru**: Zadejte`azureuser`
        - **Heslo**: Zadejte složité heslo, které splňuje požadavky na heslo.
        - **Umístění**: Z rozbalovací nabídky zvolte `East US`umístění, například . Toto umístění nemůže být stejné jako primární server.

    > [!NOTE]
    > Nastavení přihlášení a brány firewall serveru se musí shodovat s nastavením primárního serveru. 
    
      ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databáze v rámci skupiny**: Po výběru sekundárního serveru se tato možnost odemkne. Vyberte ji na **Vybrat databáze, které chcete přidat,** a pak zvolte databázi, kterou jste vytvořili v oddílu 1. Přidání databáze do skupiny převzetí služeb při selhání automaticky spustí proces geografické replikace. 
        
    ![Přidání sql db do skupiny převzetí služeb při selhání](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí PowerShellu. 

   > [!NOTE]
   > Nastavení přihlášení a brány firewall serveru se musí shodovat s nastavením primárního serveru. 

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

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický server. | 
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou databázi Azure SQL Database. | 
| [Nová-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází Azure SQL do skupiny převzetí služeb při selhání. |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Vytvořte skupinu s podporou převzetí služeb při selhání a přidejte do ní jednu databázi pomocí cli AZ. 

   > [!NOTE]
   > Nastavení přihlášení a brány firewall serveru se musí shodovat s nastavením primárního serveru. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Tato část kurzu používá následující rutiny Az CLI:

| Příkaz | Poznámky |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Vytvoření pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. | 
| [az sql převzetí služeb při selhání vytvořit skupiny](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. | 

---

## <a name="3---test-failover"></a>3 - Zkušební převzetí služeb při selhání 
V tomto kroku se nezdaří vaše skupina převzetí služeb při selhání přejdete na sekundární server a potom poselháníní pomocí portálu Azure. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Otestujte převzetí služeb při selhání pomocí portálu Azure. 

1. V levém menu [portálu Azure](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte Všechny **služby**, zadejte Azure SQL do vyhledávacího pole. (Nepovinné) Vyberte hvězdičku vedle **Azure SQL,** kterou chcete uvěznit a přidejte ji jako položku v levé navigaci. 
1. Vyberte jednu databázi vytvořenou `mySampleDatbase`v oddílu 2, například . 
1. Vyberte název serveru v části **Název serveru** a otevřete nastavení serveru.

   ![Otevřít server pro jeden db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny s podporou převzetí služeb při selhání** a pak zvolte skupinu s podporou převzetí služeb při selhání, kterou jste vytvořili v části 2. 
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární. 
1. Vyberte **převzetí služeb při selhání** z podokna úloh, chcete-li převzetí služeb při selhání skupiny převzetí služeb při selhání obsahující ukázkovou jednu databázi. 
1. Vyberte **Ano** na upozornění, které vás upozorní, že relace TDS budou odpojeny. 

   ![Převzetí služeb při selhání skupiny převzetí služeb při selhání obsahující databázi SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je nyní primární a který server je sekundární. Pokud převzetí služeb při selhání proběhlo úspěšně, měly mít dva servery vyměněné role. 
1. Chcete-li servery vrátit zpět do původních rolí, vyberte znovu **možnost Převzetí služeb při selhání.** 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Otestujte převzetí služeb při selhání pomocí prostředí PowerShell. 


Zkontrolujte roli sekundární repliky: 

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

Převzetí služeb při selhání na sekundární server: 

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

Vrátit skupinu převzetí služeb při selhání zpět na primární server:

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

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam skupin y převzetí služeb při selhání Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny Azure SQL Database převzetí služeb při selhání. |



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Otestujte převzetí služeb při selhání pomocí cli AZ. 

Ověřte, který server je sekundární:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Převzetí služeb při selhání na sekundární server: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Vrátit skupinu převzetí služeb při selhání zpět na primární server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Tato část kurzu používá následující rutiny Az CLI:

| Příkaz | Poznámky |
|---|---|
| [seznam skupin převzetí služeb při selhání az SQL](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Zobrazí seznam skupin převzetí služeb při selhání na serveru. |
| [az sql převzetí služeb při selhání skupina-primární](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání převzetí služeb při selhání přes všechny databáze z aktuálního primárního serveru. | 

---

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Vyčistěte prostředky odstraněním skupiny prostředků. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Odstraňte skupinu prostředků pomocí portálu Azure. 

1. Přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com).
1. Vyberte **Odstranit skupinu prostředků,** chcete-li odstranit všechny prostředky ve skupině, stejně jako samotnou skupinu prostředků. 
1. Do textového pole zadejte název skupiny prostředků `myResourceGroup`, a pak vyberte **Odstranit,** chcete-li skupinu prostředků odstranit.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Odstraňte skupinu prostředků pomocí PowerShellu. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Tato část kurzu používá následující rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Odstraňte skupinu prostředků pomocí příkazového příkazu k odstranění az. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Tato část kurzu používá následující rutiny Az CLI:

| Příkaz | Poznámky |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

---


> [!IMPORTANT]
> Pokud chcete zachovat skupinu prostředků, ale odstranit sekundární databázi, odeberte ji ze skupiny převzetí služeb při selhání před odstraněním. Odstranění sekundární databáze před odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování. 


## <a name="full-scripts"></a>Úplné skripty

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický server. | 
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou databázi Azure SQL Database. | 
| [Nová-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání. |
| [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází Azure SQL do skupiny převzetí služeb při selhání. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Získá nebo zobrazí seznam skupin y převzetí služeb při selhání Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny Azure SQL Database převzetí služeb při selhání. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az účet nastaven](/cli/azure/account?view=azure-cli-latest#az-account-set) | Nastaví odběr jako aktuální aktivní předplatné. | 
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Vytvoření pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Vytvoří databázi. | 
| [az sql převzetí služeb při selhání vytvořit skupiny](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. | 
| [seznam skupin převzetí služeb při selhání az SQL](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Zobrazí seznam skupin převzetí služeb při selhání na serveru. |
| [az sql převzetí služeb při selhání skupina-primární](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání převzetí služeb při selhání přes všechny databáze z aktuálního primárního serveru. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

# <a name="portal"></a>[Portál](#tab/azure-portal)
Pro portál Azure portal nejsou k dispozici žádné skripty. 
 
---

Další skripty Azure SQL Database najdete tady: [Azure PowerShell](sql-database-powershell-samples.md) a [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do skupiny převzetí služeb při selhání přidali jednu databázi azure SQL database a otestovali jste převzetí služeb při selhání. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> - Vytvořte jednu databázi Azure SQL Database. 
> - Vytvořte [skupinu převzetí služeb při selhání](sql-database-auto-failover-group.md) pro jednu databázi mezi dvěma logickými servery SQL.
> - Otestujte převzetí služeb při selhání.

Převést na další kurz o tom, jak přidat elastický fond do skupiny s podporou převzetí služeb při selhání. 

> [!div class="nextstepaction"]
> [Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání](sql-database-elastic-pool-failover-group-tutorial.md)
