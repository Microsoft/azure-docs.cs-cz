---
title: 'Rychlý Start: vytvoření datového skladu (PowerShell)'
description: K rychlému vytvoření logického serveru datového skladu Azure synapse Analytics použijte pravidlo brány firewall na úrovni serveru s použitím Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9df9b4b1bdb33a856d9e31d65981e8654af049d2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199984"
---
# <a name="quickstart-create--query-a-data-warehouse-with-azure-powershell"></a>Rychlý Start: vytvoření & dotazování datového skladu pomocí Azure PowerShell

Vytvořte datový sklad Azure synapse Analytics tím, že zřídíte fond SQL pomocí Azure PowerShell.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

> [!NOTE]
> Vytvoření datového skladu může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

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
> Koncových bodů SQL komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nebudete se moct připojit k serveru SQL Azure, dokud vaše IT oddělení neotevře port 1433.
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
* **DatabaseName**: název datového skladu, který vytváříte.
* **Název_serveru**: název serveru, který používáte pro vytváření.
* **ResourceGroupName**: Skupina prostředků, kterou používáte. K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edice**: pro vytvoření datového skladu musí být "datový sklad".

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

Nyní jste vytvořili datový sklad, vytvořili jste pravidlo brány firewall připojené k vašemu datovému skladu a spustili několik dotazů. Pokud se chcete dozvědět víc, přejděte k kurzu načtení dat.
> [!div class="nextstepaction"]
>[Načtení dat do datového skladu](load-data-from-azure-blob-storage-using-polybase.md)
