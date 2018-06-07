---
title: Prostředí PowerShell pro službu DNS alias Azure SQL | Microsoft Docs
description: Rutiny prostředí PowerShell, jako je například nový AzureRMSqlServerDNSAlias umožňují přesměrování nová připojení klienta k jinému serveru Azure SQL Database, aniž by museli touch žádnou konfiguraci klienta.
keywords: databáze sql služby DNS
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/05/2018
ms.reviewer: genemi;amagarwa;maboja
ms.author: dmalik
ms.openlocfilehash: 0353f503ea099b1355b6879efe0748a377115474
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644310"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Prostředí PowerShell pro Alias DNS do Azure SQL Database

Tento článek obsahuje skript prostředí PowerShell, které ukazuje, jak můžete spravovat aliasu DNS pro databázi SQL Azure. Skript se spustí následující rutiny, které provede následující akce:


Rutiny používané v příkladu kódu jsou následující:
- [Nové AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na serveru Azure SQL Database 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): získání a zobrazit všechny aliasy DNS, které jsou přiřazeny k databázi SQL serveru 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): upraví název serveru nakonfigurovaný tak, aby alias odkazovat na umístění, ze serveru 1 k databázi SQL serveru 2.
- [Odebrat AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Odeberte DNS alias z databáze SQL serveru 2, pomocí název aliasu.


Předchozí rutiny prostředí PowerShell byly přidány do **AzureRm.Sql** modulu počínaje verzí 5.1.1.




#### <a name="dns-alias-in-connection-string"></a>Alias DNS v připojovacím řetězci

Připojit určitý server Azure SQL Database, můžete klienta jako je například SQL Server Management Studio (SSMS) zadejte název aliasu DNS místo názvu true serveru. V následujícím příkladu řetězci serveru, alias *any jedinečné alias-name* nahrazuje prvního uzlu oddělený tečkou v řetězci se čtyřmi uzly serveru:
- Řetězec serveru příklad: `any-unique-alias-name.database.windows.net`.



## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit ukázkový skript prostředí PowerShell uvedených v tomto článku, platí následující požadavky:

- Předplatné Azure a účet. Bezplatnou zkušební verzi, klikněte na tlačítko [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].

- Modulu Azure PowerShell, pomocí rutiny **New-AzureRMSqlServerDNSAlias**.
    - K instalaci nebo upgradu, najdete v části [modul nainstalovat Azure PowerShell][install-azurerm-ps-84p].
    - Spustit `Get-Module -ListAvailable AzureRM;` v prostředí powershell\_ise.exe, jak najít verzi.

- Dva servery Azure SQL Database.

## <a name="code-example"></a>Příklad kódu

Následující prostředí PowerShell, které ukázkový kód spustí pomocí literálových hodnot přiřadit několik proměnné. Spustit kód, je nejprve nutné upravit zástupné hodnoty tak, aby odpovídaly skutečné hodnoty v systému. Nebo můžete právě prostudovali kód. A také poskytuje výstup konzoly kódu.


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
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```


#### <a name="actual-console-output-from-the-powershell-example"></a>Výstup konzoly skutečné z příkladu prostředí PowerShell

Následující výstup konzoly zkopírovat a vložit ze skutečného spouštění.


```
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

## <a name="next-steps"></a>Další postup

Úplné vysvětlení funkci aliasu DNS pro databázi SQL, najdete v části [aliasu DNS pro Azure SQL Database][dns-alias-overview-37v].



<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md

