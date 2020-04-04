---
title: Vytvoření a dotazování fondu Synapse SQL pomocí Azure PowerShellu
description: Pomocí Azure PowerShellu můžete rychle vytvořit logický server fondu SQL synapse s pravidlem brány firewall na úrovni serveru.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 57564e9dffd6022e1e4fe464b4b26a5bb8eb318b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631330"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Úvodní příručka: Vytvoření a dotazování fondu Synapse SQL s Azure PowerShellem

Vytvořte fond Synapse SQL (datový sklad) v Azure Synapse Analytics pomocí Azure PowerShellu.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

> [!IMPORTANT]
> Vytvoření fondu SQL může mít za následek novou fakturovatelnou službu.  Další informace najdete v [tématu Azure Synapse Analytics ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

Chcete-li zjistit, které předplatné používáte, spusťte [get-azsubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Pokud potřebujete použít jiné předplatné než výchozí, spusťte [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Vytvoření logického serveru

Vytvořte [logický server Azure SQL](../../sql-database/sql-database-logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pomocí příkazu [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Logický server obsahuje soubor databází spravovaných jako skupina. Následující příklad vytvoří náhodně pojmenovaný server ve skupině prostředků `ServerAdmin` s názvem `ChangeYourAdminPassword1`správce a heslem aplikace . Podle potřeby tyto předdefinované hodnoty nahraďte.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Vytvořte [pravidlo brány firewall na úrovni serveru Azure SQL](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pomocí příkazu [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, například SQL Server Management Studio nebo nástroj SQLCMD, připojit se k fondu SQL prostřednictvím brány firewall služby fondu SQL.

V následujícím příkladu je brána firewall otevřená pouze pro ostatní prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Koncové body SQL komunikují přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 1433 nemusí být povoleno bránou firewall vaší sítě. Pokud ano, nebudete se moct připojit k serveru Azure SQL, pokud vaše IT oddělení neotevře port 1433.
>

## <a name="create-a-sql-pool"></a>Vytvoření fondu SQL

Následující příklad vytvoří fond SQL pomocí dříve definovaných proměnných.  Určuje cíl služby jako DW100c, což je výchozí bod s nižšími náklady pro fond SQL.

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

* **RequestedServiceObjectiveName**: Množství [jednotek datového skladu,](what-is-a-data-warehouse-unit-dwu-cdwu.md) které požadujete. Zvýšení této částky zvyšuje náklady na výpočetní prostředky. Seznam podporovaných hodnot naleznete v [tématu limity paměti a souběžnosti](memory-concurrency-limits.md).
* **Název databáze**: Název fondu SQL, který vytváříte.
* **Název_serveru**: Název serveru, který používáte pro vytvoření.
* **ResourceGroupName**: Skupina prostředků, kterou používáte. K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edition**: Musí být "DataWarehouse" vytvořit fond SQL.

Volitelné parametry jsou:

* **CollationName:** Pokud není uvedeno, je výchozí kolace SQL_Latin1_General_CP1_CI_AS. Řazení nelze v databázi změnit.
* **MaxSizeBytes**: Výchozí maximální velikost databáze je 240 TB. Maximální velikost omezuje data rowstore. K dispozici je neomezené úložiště pro sloupcová data.

Další informace o možnostech parametrů naleznete v tématu [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy Rychlý start v této kolekci vycházejí z tohoto rychlého startu.

> [!TIP]
> Pokud máte v plánu pokračovat v práci s pozdějšími kurzy rychlého startu, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud nemáte v plánu pokračovat, použijte následující kroky k odstranění všech prostředků vytvořených tímto rychlým startem na webu Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili fond SQL, vytvořili pravidlo brány firewall, připojili se k fondu SQL a spusťte několik dotazů. Další informace, pokračujte [načíst data do](load-data-from-azure-blob-storage-using-polybase.md) článku fondu SQL.
