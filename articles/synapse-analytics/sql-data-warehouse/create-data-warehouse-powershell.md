---
title: 'Rychlý Start: vytvoření vyhrazeného fondu SQL (dřív SQL DW) pomocí Azure PowerShell'
description: Pomocí Azure PowerShell je rychlé vytvoření vyhrazeného fondu SQL (dříve SQL DW) s pravidlem brány firewall na úrovni serveru.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 18fba46e4e8de14d9b3ec43455ba2abcc4218dc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98120185"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Rychlý Start: vytvoření vyhrazeného fondu SQL (dřív SQL DW) pomocí Azure PowerShell

Vytvořte vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics pomocí Azure PowerShell.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!IMPORTANT]
> Vytvoření vyhrazeného fondu SQL (dříve SQL DW) může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Pokud chcete zjistit, které předplatné používáte, spusťte rutinu [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Vytvoření proměnných

Definujte proměnné, které se použijí ve skriptech v tomto rychlém startu.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Skupina prostředků je kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Vytvoření serveru

Pomocí příkazu [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vytvořte [logický SQL Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Server obsahuje soubor databází spravovaných jako skupina. Následující příklad vytvoří ve skupině prostředků náhodně pojmenovaný Server s uživatelem s oprávněními správce s názvem `ServerAdmin` a heslem `ChangeYourAdminPassword1` . Podle potřeby tyto předdefinované hodnoty nahraďte.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Pomocí příkazu [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vytvořte [pravidlo brány firewall na úrovni serveru](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například SQL Server Management Studio nebo nástroj SQLCMD, pro připojení k vyhrazenému fondu SQL (dřív SQL DW) prostřednictvím vyhrazené brány firewall služby fondu SQL.

V následujícím příkladu je brána firewall otevřená pouze pro ostatní prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Koncových bodů SQL komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nebudete se moct připojit k serveru, dokud vaše IT oddělení neotevře port 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Vytvoření vyhrazeného fondu SQL (dřív SQL DW)

Následující příklad vytvoří vyhrazený fond SQL (dřív SQL DW) pomocí dříve definovaných proměnných.  Určuje cíl služby jako DW100c, což je výchozí bod nižší ceny pro vyhrazený fond SQL (dřív SQL DW).

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
* **DatabaseName**: název vyhrazeného fondu SQL (dříve SQL DW), který vytváříte.
* **Název_serveru**: název serveru, který používáte pro vytváření.
* **ResourceGroupName**: Skupina prostředků, kterou používáte. K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edice**: musí být "DataWarehouse", aby bylo možné vytvořit vyhrazený fond SQL (dřív SQL DW).

Volitelné parametry jsou:

* **CollationName:** Pokud není uvedeno, je výchozí kolace SQL_Latin1_General_CP1_CI_AS. V databázi nelze změnit kolaci.
* **MaxSizeBytes**: výchozí maximální velikost databáze je 240TB. Maximální velikost rowstore data. Pro sloupcová data existuje neomezené úložiště.

Další informace o možnostech parametru naleznete v tématu [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy Rychlý start v této kolekci vycházejí z tohoto rychlého startu.

> [!TIP]
> Pokud budete chtít pokračovat v práci s novějšími kurzy rychlý Start, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem v Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili vyhrazený fond SQL (dřív SQL DW), vytvořili jste pravidlo brány firewall a připojili jste se k vyhrazenému fondu SQL. Pokud se chcete dozvědět víc, přejděte k článku [načtení dat do vyhrazeného fondu SQL](./load-data-from-azure-blob-storage-using-copy.md) .