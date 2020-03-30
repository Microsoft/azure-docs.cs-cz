---
title: PowerShell pro alias DNS
description: Rutiny prostředí PowerShell, jako je například New-AzSqlServerDNSAlias umožňují přesměrovat nová připojení klientů na jiný server Azure SQL Database, aniž byste se museli dotýkat jakékoli konfigurace klienta.
keywords: DNS SQL databáze
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420397"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell pro alias DNS do databáze Azure SQL

Tento článek obsahuje skript prostředí PowerShell, který ukazuje, jak můžete spravovat alias DNS pro Azure SQL Database.

> [!NOTE]
> Tento článek byl aktualizován tak, aby používal modul Azure PowerShell Az nebo Azure CLI. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020.
>
> Další informace o modulu Az a kompatibilitě AzureRM najdete [v tématu Představení modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az). Pokyny k instalaci najdete [v tématu Instalace Azure PowerShellu](/powershell/azure/install-az-ps) nebo [Instalace azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias DNS v připojovacím řetězci

Chcete-li připojit konkrétní server Azure SQL Database, klient, jako je SQL Server Management Studio (SSMS) může poskytnout název aliasu DNS namísto skutečného názvu serveru. V následujícím řetězci serveru příkladu nahradí alias *libovolný jedinečný alias-name* první uzel oddělený tečkami v řetězci serveru se čtyřmi uzlemi:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit ukázkový skript Prostředí PowerShell uvedený v tomto článku, platí následující požadavky:

- Předplatné Azure a účet pro bezplatnou zkušební verzi najdete v [tématu Zkušební verze Azure](https://azure.microsoft.com/free/)
- Dva databázové servery Azure SQL

## <a name="example"></a>Příklad

Následující příklad kódu začíná přiřazením hodnot literálu několika proměnným.

Chcete-li kód spustit, upravte zástupné hodnoty tak, aby odpovídaly skutečným hodnotám ve vašem systému.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použité rutiny jsou následující:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Vytvoří alias DNS v systému služby Azure SQL Database. Alias odkazuje na databázový server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získejte a seznam všech aliasů přiřazených serveru SQL DB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Upraví název serveru, na který je alias nakonfigurován, ze serveru 1 na server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odebrat alias z databázového serveru 2 pomocí názvu aliasu.

Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Použijte `Get-Module -ListAvailable Az` v *powershellu\_ise.exe*, chcete-li najít verzi.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Použité příkazy jsou následující:

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Vytvoří alias DNS v systému služby Azure SQL Database. Alias odkazuje na databázový server 1.
- [az sql server dns-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získejte a uveďte seznam všech aliasů přiřazených serveru SQL DB 1.
- [Az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Upraví název serveru, na který je alias nakonfigurován, ze serveru 1 na server 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odebrat alias z databázového serveru 2 pomocí názvu aliasu.

Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Další kroky

Úplné vysvětlení funkce aliasu DNS pro databázi SQL najdete v [tématu ALIAS DNS pro databázi Azure SQL](dns-alias-overview.md).
