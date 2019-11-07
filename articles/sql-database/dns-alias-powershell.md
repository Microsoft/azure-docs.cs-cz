---
title: PowerShell pro alias DNS Azure SQL
description: Rutiny PowerShellu, jako je New-AzSqlServerDNSAlias, vám umožní přesměrovat nová připojení klientů na jiný Azure SQL Database Server, aniž byste museli nic upravovat.
keywords: databáze SQL DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: ebbca309b7f6acd071c7075e63e670a8efa49f4e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685325"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell pro Azure SQL Database aliasu DNS

Tento článek poskytuje skript prostředí PowerShell, který ukazuje, jak můžete spravovat alias DNS pro Azure SQL Database. Skript spustí následující rutiny, které provede následující akce:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rutiny používané v tomto příkladu kódu jsou následující:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Získejte a vypíše všechny aliasy DNS, které jsou přiřazené k serveru SQL DB Server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): upraví název serveru, na který je alias nakonfigurovaný, ze serveru 1 na SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Odeberte alias DNS ze serveru SQL DB Server 2 pomocí názvu aliasu.

## <a name="dns-alias-in-connection-string"></a>Alias DNS v připojovacím řetězci

K připojení konkrétního Azure SQL Databaseho serveru může klient, jako je například SQL Server Management Studio (SSMS), poskytovat název aliasu DNS místo skutečného názvu serveru. V následujícím příkladu řetězce serveru nahradí alias *Any-Unique-alias-Name* první uzel oddělený tečkou v řetězci čtyř serverového serveru:

- Příklad řetězce serveru: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit ukázkový skript PowerShellu, který je uvedený v tomto článku, platí následující předpoklady:

- Předplatné Azure a účet. Bezplatnou zkušební verzi získáte kliknutím na [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/].
- Azure PowerShell modul s rutinou **New-AzSqlServerDNSAlias**.
  - Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell][install-Az-ps-84p].
  - Pokud chcete zjistit verzi, spusťte `Get-Module -ListAvailable Az;` v PowerShellu\_ISE. exe.
- Dva Azure SQL Database servery.

## <a name="code-example"></a>Příklad kódu

Následující příklad kódu prostředí PowerShell začíná přiřazením hodnot literálů k několika proměnným. Chcete-li spustit kód, musíte nejprve upravit všechny zástupné hodnoty tak, aby odpovídaly skutečným hodnotám ve vašem systému. Nebo můžete pouze prozkoumat kód. A výstup konzoly je také k dispozici.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Skutečný výstup konzoly z příkladu PowerShellu

Následující výstup konzoly byl zkopírován a vložen ze skutečného spuštění.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Další kroky

Úplné vysvětlení funkce aliasu DNS pro SQL Database najdete v tématu [alias DNS pro Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
