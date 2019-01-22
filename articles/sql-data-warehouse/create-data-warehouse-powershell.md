---
title: 'Rychlý start: Vytvoření služby Azure SQL Data Warehouse – Azure Powershell | Dokumentace Microsoftu'
description: Rychle vytvořte logický server služby SQL Database, pravidlo brány firewall na úrovni serveru a data warehouse pomocí Azure Powershellu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/15/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 22ccc11d1eb74fd868fd740f02311baeb5ac5987
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433607"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Rychlý start: Vytvořit a dotazovat službu Azure SQL data warehouse pomocí Azure Powershellu

Rychle vytvořte službu Azure SQL data warehouse pomocí Azure Powershellu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento kurz vyžaduje modul Azure PowerShell verze 5.1.1 nebo novější. To, jakou verzi aktuálně používáte, zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). 


> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další informace najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k předplatnému Azure pomocí [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) příkaz a postupujte podle pokynů na obrazovce pokynů.

```powershell
Add-AzureRmAccount
```

Chcete-li zjistit, které předplatné používáte, spusťte [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Pokud budete muset použít jiné předplatné než výchozí, spusťte [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Vytvoření proměnných

Definujte proměnné, které se použijí ve skriptech v tomto rychlém startu.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Vytvoření logického serveru

Vytvoření [logického serveru Azure SQL](../sql-database/sql-database-logical-servers.md) pomocí [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) příkazu. Logický server obsahuje soubor databází spravovaných jako skupina. Následující příklad vytvoří náhodně pojmenovaný server ve vaší skupině prostředků s uživateli s právy s názvem `ServerAdmin` a heslem `ChangeYourAdminPassword1`. Podle potřeby tyto předdefinované hodnoty nahraďte.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Vytvoření [pravidlo brány firewall na úrovni serveru Azure SQL](../sql-database/sql-database-firewall-configure.md) pomocí [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) příkazu. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například SQL Server Management Studio nebo Nástroj SQLCMD, připojení k SQL data warehouse prostřednictvím brány firewall služby SQL Data Warehouse. V následujícím příkladu je brána firewall otevřená pouze pro ostatní prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database a SQL Data Warehouse komunikují přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nebudete moct připojit k serveru Azure SQL, dokud vaše IT oddělení neotevře port 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Vytvořte datový sklad s ukázkovými daty
Tento příklad vytvoří datový sklad pomocí dříve definované proměnné.  Určuje cíl služby jako DW400, což je výchozí bod pro váš datový sklad nižšími náklady. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Požadované parametry jsou:

* **RequestedServiceObjectiveName**: Množství [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) se požaduje. Zvýšení tato částka zvyšuje náklady na výpočetní prostředky. Seznam podporovaných hodnot naleznete v tématu [omezení paměti a souběžnosti](memory-and-concurrency-limits.md).
* **DatabaseName**: Název služby SQL Data Warehouse, kterou vytváříte.
* **ServerName**: Název serveru, který používáte pro vytváření.
* **ResourceGroupName**: Skupina prostředků, které používáte. K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edice**: Musí být "Datových skladů" k vytvoření SQL Data Warehouse.

Volitelné parametry jsou:

- **%{Collationname/**: Výchozí kolace, pokud není zadán, je SQL_Latin1_General_CP1_CI_AS. Kolaci nejde změnit na databázi.
- **MaxSizeBytes**: Výchozí maximální velikost databáze je 10 GB.

Další informace o možných parametrech najdete v tématu [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy Rychlý start v této kolekci vycházejí z tohoto rychlého startu. 

> [!TIP]
> Pokud budete chtít pokračovat v práci s dalšími kurzy rychlý start, není nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Další postup

Právě jste vytvořili datový sklad a pravidlo brány firewall, připojení k vašemu datovému skladu a spustit pár dotazů. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.
> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
