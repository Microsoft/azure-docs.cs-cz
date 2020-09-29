---
title: Alias DNS (PowerShell & Azure CLI)
description: Rutiny PowerShellu a Azure CLI umožňují přesměrovat nová připojení klientů na jiný SQL Server v Azure, aniž by se museli dotknout jakékoli konfigurace klienta.
keywords: databáze SQL DNS
ms.custom: seo-lt-2019 sqldbrb=1
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: c1006cce4125adf67b9e3009bf8e27c0123acfd1
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443635"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell pro Azure SQL Database aliasu DNS
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Tento článek poskytuje skript prostředí PowerShell, který ukazuje, jak můžete spravovat alias DNS pro [SQL Server](logical-servers.md) hostující vaše Azure SQL Database.

> [!NOTE]
> Tento článek byl aktualizován tak, aby používal Azure PowerShell AZ Module nebo Azure CLI. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020.
>
> Další informace o kompatibilitě modulu AZ Module a AzureRM najdete v tématu [představení modulu Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az). Pokyny k instalaci najdete v tématu [instalace Azure PowerShell](/powershell/azure/install-az-ps) nebo instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Alias DNS v připojovacím řetězci

K připojení [logického SQL serveru](logical-servers.md)může klient, jako je například SQL Server Management Studio (SSMS), poskytovat název aliasu DNS místo skutečného názvu serveru. V následujícím příkladu řetězce serveru nahradí alias *Any-Unique-alias-Name* první uzel oddělený tečkou v řetězci čtyř serverového serveru:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit ukázkový skript PowerShellu, který je uvedený v tomto článku, platí následující předpoklady:

- Předplatné Azure a účet pro bezplatnou zkušební verzi najdete v tématu [zkušební verze Azure](https://azure.microsoft.com/free/) .
- Dva servery

## <a name="example"></a>Příklad

Následující příklad kódu začíná přiřazením literálových hodnot k několika proměnným.

Chcete-li spustit kód, upravte hodnoty zástupných symbolů tak, aby odpovídaly skutečným hodnotám ve vašem systému.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Používají se následující rutiny:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): vytvoří alias DNS v systému služby Azure SQL Database. Alias odkazuje na server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získá a vypíše všechny aliasy přiřazené k serveru 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): upraví název serveru, pro který je nakonfigurován odkaz na, ze serveru 1 na server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias ze serveru 2 pomocí názvu aliasu.

Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

`Get-Module -ListAvailable Az`K nalezení verze použijte * \_ise.exePowerShellu *.

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

Používají se následující příkazy:

- [AZ SQL Server DNS-alias Create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): vytvoří alias DNS pro server. Alias odkazuje na server 1.
- [AZ SQL Server DNS-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získá a vypíše všechny aliasy přiřazené k serveru 1.
- [AZ SQL Server DNS-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): upraví název serveru, na který je alias nakonfigurován, aby odkazoval na, ze serveru 1 na server 2.
- [AZ SQL Server DNS-alias Delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias ze serveru 2 pomocí názvu aliasu.

Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

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

Úplné vysvětlení funkce aliasu DNS pro SQL Database najdete v tématu [alias DNS pro Azure SQL Database](../../sql-database/dns-alias-overview.md).
