---
title: Prostředí PowerShell pro službu DNS alias Azure SQL | Dokumentace Microsoftu
description: Rutiny Powershellu, jako je například nový-AzureRMSqlServerDNSAlias umožňují přesměrování nových připojení klienta k jinému serveru Azure SQL Database, aniž byste museli touch žádnou konfiguraci klienta.
keywords: databáze sql DNS
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja, jrasnick
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 115c4a290bc6dfba049e0ce49498877c1a2466b6
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388785"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Prostředí PowerShell pro Alias DNS do služby Azure SQL Database

Tento článek obsahuje Powershellový skript, který ukazuje, jak můžete spravovat aliasu DNS pro službu Azure SQL Database. Skript se spustí následující rutiny, která provede následující akce:

Rutiny používané v příkladu kódu jsou následující:

- [New-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Vytvoří nový alias DNS v systému služby Azure SQL Database. Alias odkazuje na serveru Azure SQL Database 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Získání a seznam všechny aliasy DNS, které jsou přiřazeny k serveru SQL DB 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Upraví název serveru alias nakonfigurovaný tak, aby si ze serveru 1 do databáze SQL serveru 2.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): Odebrání DNS alias serveru SQL DB 2, pomocí názvu aliasu.

Předchozí rutiny prostředí PowerShell, které byly přidány do **AzureRm.Sql** od verze 5.1.1 modulu.

## <a name="dns-alias-in-connection-string"></a>Alias DNS v připojovacím řetězci

Připojit konkrétní server Azure SQL Database, můžete klienta jako je například SQL Server Management Studio (SSMS) zadejte název DNS alias místo názvu serveru true. V následujícím příkladu řetězci serveru, alias *jakýkoli jedinečný alias název--* nahradí první uzel oddělené tečkou v řetězci čtyři uzly serveru:

- Příklad serveru řetězce: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit ukázku skript prostředí PowerShell uvedený v tomto článku, platí následující požadavky:

- Předplatné Azure a účet. Pro bezplatnou zkušební verzi, klikněte na tlačítko [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].
- Modul Azure Powershellu pomocí rutiny **New-AzureRMSqlServerDNSAlias**.
  - Pro instalaci nebo upgrade, naleznete v tématu [instalace modulu Azure PowerShell][install-azurerm-ps-84p].
  - Spustit `Get-Module -ListAvailable AzureRM;` v prostředí powershell\_ise.exe, pokud chcete zjistit verzi.
- Dva servery Azure SQL Database.

## <a name="code-example"></a>Příklad kódu

Následující příkaz Powershellu spuštění příkladu kódu pomocí literálových hodnot přiřadit několik proměnných. Kód spustit, musíte nejprve upravit všechny zástupné hodnoty tak, aby odpovídala skutečné hodnoty v systému. Nebo můžete stačí zkoumání kód. A výstup konzoly z kódu je také k dispozici.

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

### <a name="actual-console-output-from-the-powershell-example"></a>Skutečné konzoly výstup z příkladu Powershellu

Následující výstup konzoly se zkopírovat a vložit z skutečný spuštění.

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

## <a name="next-steps"></a>Další postup

Úplné vysvětlení funkci DNS Alias pro službu SQL Database, najdete v části [aliasu DNS pro Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
