---
title: Prostředí PowerShell pro koncové body virtuální sítě a pravidla pro jednu a sdruženou databázi
description: Poskytuje skripty PowerShellu pro vytváření a správu koncových bodů virtuální služby pro azure SQL database a Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124706"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: Vytvoření koncového bodu virtuální služby a pravidla virtuální sítě pro SQL

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která určuje, zda databázový server pro jednotlivé databáze a elastické fondy v Azure [SQL Database](sql-database-technical-overview.md) nebo pro vaše databáze v [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) přijímá komunikaci, která jsou odesílána z konkrétních podsítí ve virtuálních sítích.

> [!IMPORTANT]
> Tento článek se vztahuje na Azure SQL server a sql database a datový sklad v Azure Synapse, které jsou vytvořené na serveru Azure SQL. Pro jednoduchost SQL Database se používá při odkazování na SQL Database a Azure Synapse. Tento článek se *nevztahuje* na nasazení **spravované instance** v Azure SQL Database, protože nemá koncový bod služby s ním spojené.

Tento článek obsahuje a vysvětluje skript prostředí PowerShell, který provádí následující akce:

1. Vytvoří koncový *bod virtuální služby* Microsoft Azure ve vaší podsíti.
2. Přidá koncový bod do brány firewall serveru Azure SQL Database, aby se *vytvořilo pravidlo virtuální sítě*.

Vaše motivace k vytvoření pravidla jsou vysvětleny v: [Koncové body virtuální služby pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Pokud vše, co potřebujete, je posoudit nebo přidat *název typu* koncového bodu virtuální služby pro databázi SQL do podsítě, můžete přeskočit na náš [přímější skript prostředí PowerShell](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

## <a name="major-cmdlets"></a>Hlavní rutiny

Tento článek zdůrazňuje rutinu **New-AzSqlServerVirtualNetworkRule,** která přidá koncový bod podsítě do seznamu řízení přístupu (ACL) serveru Azure SQL Database, čímž vytvoří pravidlo.

V následujícím seznamu je uvedena posloupnost dalších *hlavních* rutin, které je nutné spustit, aby bylo nutné připravit volání na **New-AzSqlServerVirtualNetworkRule**. V tomto článku se tato volání vyskytují ve [skriptu 3 "Pravidlo virtuální sítě"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Vytvoří objekt podsítě.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Vytvoří virtuální síť, která jí podsíť.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Přiřadí koncový bod virtuální služby podsíti.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Zachová aktualizace provedené ve vaší virtuální síti.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Poté, co je vaše podsíť koncovým bodem, přidá podsíť jako pravidlo virtuální sítě do acl serveru Azure SQL Database.
   - Tato rutina nabízí parametr **-IgnoreMissingVNetServiceEndpoint**, počínaje modulem Azure RM PowerShell verze 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Požadavky pro spuštění Prostředí PowerShell

- Už se můžete přihlásit k Azure, například prostřednictvím [portálu Azure][http-azure-portal-link-ref-477t].
- Skripty prostředí PowerShell už můžete spustit.

> [!NOTE]
> Ujistěte se, že koncové body služby jsou zapnuté pro virtuální síť nebo podsíť, kterou chcete přidat na server, jinak se nezdaří vytvoření pravidla brány firewall virtuální sítě.

## <a name="one-script-divided-into-four-chunks"></a>Jeden skript rozdělen do čtyř bloků

Naše demonstrační powershellový skript je rozdělen na posloupnost menších skriptů. Divize usnadňuje učení a poskytuje flexibilitu. Skripty musí být spuštěny v uvedeném pořadí. Pokud nemáte čas na spuštění skriptů, náš skutečný testovací výstup se zobrazí po skriptu 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Skript 1: Proměnné

Tento první skript prostředí PowerShell přiřazuje hodnoty proměnným. Následující skripty závisí na těchto proměnných.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu můžete upravit hodnoty, pokud chcete. Pokud například již skupinu prostředků máte, můžete upravit název skupiny prostředků jako přiřazenou hodnotu.
>
> Název předplatného by měl být upraven do skriptu.

### <a name="powershell-script-1-source-code"></a>Zdrojový kód skriptu PowerShellu 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Skript 2: Požadavky

Tento skript se připraví na další skript, kde je akce koncového bodu. Tento skript vytvoří pro vás následující uvedené položky, ale pouze v případě, že ještě neexistují. Skript 2 můžete přeskočit, pokud jste si jisti, že tyto položky již existují:

- Skupina prostředků Azure
- Databázový server Azure SQL

### <a name="powershell-script-2-source-code"></a>Zdrojový kód skriptu PowerShellu 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Vytvoření koncového bodu a pravidla

Tento skript vytvoří virtuální síť s podsítí. Skript pak přiřadí typ koncového bodu **Microsoft.Sql** do podsítě. Nakonec skript přidá podsíť do seznamu řízení přístupu (ACL) serveru SQL Database, čímž vytvoří pravidlo.

### <a name="powershell-script-3-source-code"></a>Zdrojový kód skriptu PowerShellu 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skript 4: Vyčištění

Tento konečný skript odstraní prostředky, které předchozí skripty vytvořené pro demonstraci. Skript však požádá o potvrzení před odstraněním následující:

- Databázový server Azure SQL
- Skupina prostředků Azure

Skript 4 můžete spustit kdykoli po dokončení skriptu 1.

### <a name="powershell-script-4-source-code"></a>Zdrojový kód skriptu PowerShellu 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Ověření, že podsíť je koncový bod

Je možné, že máte podsíť, které již byl přiřazen název typu **Microsoft.Sql,** což znamená, že je již koncovýbod virtuální služby. Portál [Azure][http-azure-portal-link-ref-477t] můžete použít k vytvoření pravidla virtuální sítě z koncového bodu.

Nebo si pravděpodobně nejste jisti, zda má vaše podsíť název typu **Microsoft.Sql.** Můžete spustit následující skript Prostředí PowerShell, abyste mohli provést tyto akce:

1. Zkontrolujte, zda má podsíť název typu **Microsoft.Sql.**
2. Volitelně můžete přiřadit název typu, pokud chybí.
    - Skript vás požádá o *potvrzení*, než použije chybějící název typu.

### <a name="phases-of-the-script"></a>Fáze skriptu

Tady jsou fáze skriptu Prostředí PowerShell:

1. PŘIHLASTE se ke svému účtu Azure, který je potřeba pouze jednou v relaci PS.  Přiřaďte proměnné.
2. Vyhledejte virtuální síť a potom podsíť.
3. Je vaše podsíť označena jako typ koncového serveru **Microsoft.Sql?**
4. Do podsítě přidejte koncový bod virtuální služby s názvem typu **Microsoft.Sql**.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu je nutné upravit hodnoty přiřazené proměnným $-variables v horní části skriptu.

### <a name="direct-powershell-source-code"></a>Zdrojový kód Direct PowerShellu

Tento skript prostředí PowerShell nic neaktualizuje, pokud neodpovíte ano, pokud je vás požádá o potvrzení. Skript může do podsítě přidat název typu **Microsoft.Sql.** Skript se však pokusí přidat pouze v případě, že podsíť postrádá název typu.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
