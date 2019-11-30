---
title: 'Rychlý Start: Vytvoření skladu – Azure PowerShell'
description: Rychle vytvořte SQL Database logický Server, pravidlo brány firewall na úrovni serveru a datový sklad s Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 44abf23ce632aa1ac630eab3313ffd675eef1a0b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559158"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Rychlý Start: vytvoření a dotazování Azure SQL Data Warehouse pomocí Azure PowerShell

Azure SQL Data Warehouse pomocí Azure PowerShell rychle vytvořit.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další informace najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud chcete zjistit, které předplatné používáte, spusťte rutinu [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
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

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Vytvoření logického serveru

Pomocí příkazu [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) vytvořte [logický Server Azure SQL](../sql-database/sql-database-logical-servers.md) . Logický server obsahuje soubor databází spravovaných jako skupina. Následující příklad vytvoří ve skupině prostředků náhodně pojmenovaný Server s uživatelem s právy pro správu s názvem `ServerAdmin` a heslem `ChangeYourAdminPassword1`. Podle potřeby tyto předdefinované hodnoty nahraďte.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Pomocí příkazu [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) vytvořte [pravidlo brány firewall na úrovni serveru SQL Azure](../sql-database/sql-database-firewall-configure.md) . Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například SQL Server Management Studio nebo nástroj SQLCMD, pro připojení k SQL Data Warehouse prostřednictvím brány firewall služby SQL Data Warehouse. V následujícím příkladu je brána firewall otevřená pouze pro ostatní prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database a SQL Data Warehouse komunikují přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nebudete se moct připojit k serveru SQL Azure, dokud vaše IT oddělení neotevře port 1433.
>


## <a name="create-a-data-warehouse"></a>Vytvoření datového skladu
Tento příklad vytvoří datový sklad pomocí dříve definovaných proměnných.  Určuje cíl služby jako DW100c, což je výchozí bod pro přenos dat datového skladu nižších nákladů. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Požadované parametry jsou:

* **RequestedServiceObjectiveName**: množství [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) , které požadujete. Zvýšením této hodnoty se zvýší náklady na výpočetní výkon. Seznam podporovaných hodnot naleznete v tématu limity pro [paměť a souběžnost](memory-concurrency-limits.md).
* **DatabaseName**: název SQL Data Warehouse, který vytváříte.
* **Název_serveru**: název serveru, který používáte pro vytváření.
* **ResourceGroupName**: Skupina prostředků, kterou používáte. K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edition:** Aby bylo možné vytvořit SQL Data Warehouse, je nutné nastavit edici DataWarehouse.

Volitelné parametry jsou:

- **CollationName:** Pokud není uvedeno, je výchozí kolace SQL_Latin1_General_CP1_CI_AS. V databázi nelze změnit kolaci.
- **MaxSizeBytes**: výchozí maximální velikost databáze je 240TB. Maximální velikost rowstore data. Pro sloupcová data existuje neomezené úložiště.

Další informace o možnostech parametru naleznete v tématu [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy Rychlý start v této kolekci vycházejí z tohoto rychlého startu. 

> [!TIP]
> Pokud budete chtít pokračovat v práci s novějšími kurzy rychlý Start, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili datový sklad, vytvořili jste pravidlo brány firewall připojené k vašemu datovému skladu a spustili několik dotazů. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.
> [!div class="nextstepaction"]
>[Načtení dat do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
