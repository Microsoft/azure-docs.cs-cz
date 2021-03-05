---
title: 'Kurz: Přidání databáze do skupiny převzetí služeb při selhání'
description: Do skupiny převzetí služeb při selhání přidejte Azure SQL Database databázi pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 68fa089713c3dd89b4699011ded7d667bca6f73f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178065"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Kurz: Přidání Azure SQL Database do skupiny převzetí služeb při selhání
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Skupina převzetí služeb při selhání](auto-failover-group-overview.md) je deklarativní vrstva abstrakce, která umožňuje seskupit více geograficky replikovaných databází. Naučte se konfigurovat skupinu převzetí služeb při selhání pro Azure SQL Database a testovací převzetí služeb při selhání pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.  V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření databáze v Azure SQL Database
> - Vytvořte skupinu převzetí služeb při selhání pro databázi mezi dvěma servery.
> - Testovací převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

# <a name="the-portal"></a>[Portál](#tab/azure-portal)

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

K dokončení tohoto kurzu se ujistěte, že máte následující položky:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) .
- Nejnovější verze rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="1---create-a-database"></a>1. vytvoření databáze

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – Vytvoření skupiny převzetí služeb při selhání

V tomto kroku vytvoříte [skupinu převzetí služeb při selhání](auto-failover-group-overview.md) mezi existujícím serverem a novým serverem v jiné oblasti. Pak přidejte ukázkovou databázi do skupiny převzetí služeb při selhání.

# <a name="the-portal"></a>[Portál](#tab/azure-portal)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní svou databázi pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud v seznamu není **Azure SQL** , vyberte **všechny služby** a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte databázi vytvořenou v části 1, například `mySampleDatabase` .
1. Skupiny převzetí služeb při selhání je možné nakonfigurovat na úrovni serveru. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

   ![Otevřít server pro databázi](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte **Přidat skupinu** a vytvořte novou skupinu převzetí služeb při selhání.

   ![Přidat novou skupinu převzetí služeb při selhání](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Na stránce **Skupina převzetí služeb při selhání** zadejte nebo vyberte následující hodnoty a pak vyberte **vytvořit**:

   - **Název skupiny převzetí služeb při selhání**: Zadejte jedinečný název skupiny převzetí služeb při selhání, například `failovergrouptutorial` .
   - **Sekundární server**: vyberte možnost *Konfigurace požadovaných nastavení* a pak zvolte **Vytvoření nového serveru**. Alternativně můžete zvolit již existující server jako sekundární server. Po zadání následujících hodnot vyberte **Vybrat**.
      - **Název serveru**: Zadejte jedinečný název sekundárního serveru, například `mysqlsecondary` .
      - **Přihlašovací jméno správce serveru**: typ `azureuser`
      - **Heslo**: zadejte komplexní heslo, které splňuje požadavky na heslo.
      - **Umístění**: vyberte umístění z rozevíracího seznamu, například `East US` . Toto umístění nemůže být stejné jako primární server.

     > [!NOTE]
     > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru.

     ![Vytvoření sekundárního serveru pro skupinu převzetí služeb při selhání](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Databáze v rámci skupiny**: po výběru sekundárního serveru se tato možnost odemkne. Vyberte ho a vyberte databáze, které **chcete přidat** , a pak vyberte databázi, kterou jste vytvořili v části 1. Když přidáte databázi do skupiny převzetí služeb při selhání, automaticky se spustí proces geografické replikace.

   ![Přidat SQL Database do skupiny převzetí služeb při selhání](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní svou databázi pomocí PowerShellu.

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
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server v Azure SQL Database, který hostuje samostatné databáze a elastické fondy. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server v Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou izolovanou databázi v Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání v Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází v Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází do skupiny převzetí služeb při selhání v Azure SQL Database. |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vytvořte skupinu převzetí služeb při selhání a přidejte do ní svou databázi pomocí Azure CLI.

   > [!NOTE]
   > Přihlašovací údaje serveru a firewall se musí shodovat s nastavením vašeho primárního serveru.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Tato část kurzu používá následující rutiny rozhraní příkazového řádku Azure CLI:

| Příkaz | Poznámky |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server, který je hostitelem databází a elastických fondů. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. |
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. |

---

## <a name="3---test-failover"></a>3. testování převzetí služeb při selhání

V tomto kroku navedete selhání skupiny převzetí služeb při selhání na sekundární server a pak se znovu navrátíte pomocí Azure Portal.

# <a name="the-portal"></a>[Portál](#tab/azure-portal)

Testovací převzetí služeb při selhání pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud v seznamu není **Azure SQL** , vyberte **všechny služby** a do vyhledávacího pole zadejte Azure SQL. Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu.
1. Vyberte databázi vytvořenou v části 2, například `mySampleDatbase` .
1. Kliknutím na název serveru v části **název serveru** otevřete nastavení serveru.

   ![Otevřít server pro databázi](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. V podokně **Nastavení** vyberte **skupiny převzetí služeb při selhání** a pak vyberte skupinu převzetí služeb při selhání, kterou jste vytvořili v části 2.
  
   ![Výběr skupiny převzetí služeb při selhání z portálu](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Zkontrolujte, který server je primární a který server je sekundární.
1. V podokně úloh vyberte **převzetí služeb při** selhání pro skupinu převzetí služeb při selhání, která obsahuje ukázkovou databázi.
1. U upozornění, které vás upozorní na to, že relace TDS budou odpojeny, vyberte **Ano** .

   ![Převzetí služeb při selhání ve skupině, která obsahuje vaši databázi](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Zkontrolujte, který server je teď primární a který server je sekundární. Pokud se převzetí služeb při selhání úspěšně převedlo, musí mít dva servery zaměnitelné role.
1. Znovu vyberte **převzetí služeb při selhání** , aby se servery znovu nezměnily na původní role.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Testovací převzetí služeb při selhání pomocí Azure CLI.

Ověřte, který server je sekundární:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Převzetí služeb při selhání sekundárním serverem:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Vraťte skupinu převzetí služeb při selhání zpátky na primární server:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Tato část kurzu používá následující rutiny rozhraní příkazového řádku Azure CLI:

| Příkaz | Poznámky |
|---|---|
| [AZ SQL Failover-Group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání pomocí převzetí služeb při selhání všemi databázemi z aktuálního primárního serveru. |

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky odstraněním skupiny prostředků.

# <a name="the-portal"></a>[Portál](#tab/azure-portal)

Odstraňte skupinu prostředků pomocí Azure Portal.

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com).
1. Vyberte  **Odstranit skupinu prostředků** a odstraňte všechny prostředky ve skupině a také samotnou skupinu prostředků.
1. Zadejte název skupiny prostředků, `myResourceGroup` v textovém poli a pak vyberte **Odstranit** a odstraňte skupinu prostředků.  

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

Tato část kurzu používá následující rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků. |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Odstraňte skupinu prostředků pomocí Azure CLI.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Tato část kurzu používá následující rutiny rozhraní příkazového řádku Azure CLI:

| Příkaz | Poznámky |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

---

> [!IMPORTANT]
> Pokud chcete zachovat skupinu prostředků, ale odstranit sekundární databázi, odeberte ji ze skupiny převzetí služeb při selhání a teprve potom ji odstraňte. Odstranění sekundární databáze před jejím odebráním ze skupiny převzetí služeb při selhání může způsobit nepředvídatelné chování.

## <a name="full-scripts"></a>Úplné skripty

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server hostující jednotlivé databáze a elastické fondy v Azure SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro server v Azure SQL Database. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří novou databázi v Azure SQL Database. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Vytvoří novou skupinu převzetí služeb při selhání v Azure SQL Database. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Získá jednu nebo více databází v Azure SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Přidá jednu nebo více databází do skupiny převzetí služeb při selhání v Azure SQL Database. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Načte nebo vypíše skupiny převzetí služeb při selhání v Azure SQL Database. |
| [Switch – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Provede převzetí služeb při selhání skupiny převzetí služeb při selhání v Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků v Azure SQL Database.|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Account set](/cli/azure/account#az-account-set) | Nastaví předplatné jako aktuální aktivní předplatné. |
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server hostující jednotlivé databáze a elastické fondy v Azure SQL Database. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall protokolu IP na úrovni serveru v Azure SQL Database. |
| [az sql db create](/cli/azure/sql/db) | Vytvoří databázi v Azure SQL Database. |
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání v Azure SQL Database. |
| [AZ SQL Failover-Group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Vypíše skupiny převzetí služeb při selhání na serveru v Azure SQL Database. |
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání pomocí převzetí služeb při selhání všemi databázemi z aktuálního primárního serveru. |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

# <a name="the-portal"></a>[Portál](#tab/azure-portal)

Pro Azure Portal nejsou k dispozici žádné skripty.

---

Další Azure SQL Database skripty můžete najít tady: [Azure PowerShell](powershell-script-content-guide.md) a [Azure CLI](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do skupiny převzetí služeb při selhání přidali Azure SQL Database databázi a otestujete převzetí služeb při selhání. Naučili jste se:

> [!div class="checklist"]
>
> - Vytvoření databáze v Azure SQL Database
> - Vytvořte skupinu převzetí služeb při selhání pro databázi mezi dvěma servery.
> - Testovací převzetí služeb při selhání.

Přejděte k dalšímu kurzu, jak přidat elastický fond do skupiny převzetí služeb při selhání.

> [!div class="nextstepaction"]
> [Kurz: Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání](failover-group-add-elastic-pool-tutorial.md)