---
title: PowerShell pro koncové body a pravidla virtuální sítě pro databáze s jednou a fondem
description: Poskytuje skripty PowerShellu pro vytváření a správu koncových bodů virtuální služby pro váš Azure SQL Database a Azure synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: ae92d2000bb2c0dfd7e7a42c6070c143e5b787e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84170864"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>PowerShell: Vytvoření koncového bodu virtuální služby a pravidla virtuální sítě pro Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*Pravidla virtuální sítě* jsou jedna funkce zabezpečení brány firewall, která určuje, jestli [logický SQL server](../logical-servers.md) pro databáze [Azure SQL Database](../sql-database-paas-overview.md) , elastické fondy nebo databáze v [Azure synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) přijímají komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích.

> [!IMPORTANT]
> Tento článek se týká Azure SQL Database, včetně Azure synapse (dřív SQL DW). Termínem Azure SQL Database v tomto článku se pro jednoduchost vztahuje i na databáze patřící do Azure SQL Database nebo Azure synapse. Tento článek se *nevztahuje na* SPRAVOVANOU instanci SQL Azure, protože k ní není přidružen koncový bod služby.

Tento článek ukazuje skript prostředí PowerShell, který provede následující akce:

1. Vytvoří ve vaší podsíti *koncový bod virtuální služby* Microsoft Azure.
2. Přidá koncový bod do brány firewall serveru pro vytvoření *pravidla virtuální sítě*.

Další informace najdete v tématu [virtuální koncové body služby pro Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Pokud potřebujete jenom vyhodnotit nebo přidat *název typu* koncového bodu virtuální služby pro Azure SQL Database do vaší podsítě, můžete přejít k naší [přímější skriptu PowerShellu](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu se pořád podporuje v Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro [ `Az.Sql` rutiny](/powershell/module/az.sql). Starší modul naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

## <a name="major-cmdlets"></a>Hlavní rutiny

Tento článek zvýrazňuje [rutinu **New-AzSqlServerVirtualNetworkRule** ](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule) , která přidá koncový bod podsítě do seznamu řízení přístupu (ACL) vašeho serveru a vytvoří tak pravidlo.

Následující seznam obsahuje posloupnost dalších *hlavních* rutin, které je třeba spustit pro přípravu volání rutiny **New-AzSqlServerVirtualNetworkRule**. V tomto článku se tato volání vyskytují ve [skriptu 3 "pravidlo virtuální sítě"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): vytvoří objekt podsítě.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): vytvoří virtuální síť a tím ji přidělí podsíti.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): přiřadí ke své podsíti koncový bod virtuální služby.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): zachovává aktualizace provedené ve vaší virtuální síti.
5. [New-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): Jakmile je podsíť koncovým bodem, přidá vaši podsíť jako pravidlo virtuální sítě do seznamu ACL serveru.
   - Tato rutina nabízí parametr **-IgnoreMissingVNetServiceEndpoint**počínaje modulem Azure RM PowerShell verze 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Předpoklady pro spuštění PowerShellu

- K Azure se už můžete přihlásit, například prostřednictvím [Azure Portal][http-azure-portal-link-ref-477t].
- PowerShellové skripty už můžete spouštět.

> [!NOTE]
> Zajistěte, aby byly pro virtuální síť nebo podsíť, které chcete přidat k serveru, zapnuté koncové body služby. v opačném případě se vytvoření pravidla brány firewall virtuální sítě nezdaří.

## <a name="one-script-divided-into-four-chunks"></a>Jeden skript dělený do čtyř bloků dat

Náš ukázkový skript PowerShellu je rozdělen do sekvence menších skriptů. Rozdělení usnadňuje učení a poskytuje flexibilitu. Skripty musí být spuštěny v uvedeném pořadí. Pokud nemáte čas spustit skripty, náš skutečný výstup testu se zobrazí za skriptem 4.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>Skript 1: proměnné

Tento první skript prostředí PowerShell přiřadí hodnoty proměnným. Následující skripty závisí na těchto proměnných.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu můžete upravovat hodnoty, pokud chcete. Například pokud už máte skupinu prostředků, můžete chtít upravit název skupiny prostředků jako přiřazenou hodnotu.
>
> Název vašeho předplatného by měl být upravený do skriptu.

### <a name="powershell-script-1-source-code"></a>Zdrojový kód PowerShellového skriptu 1

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

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>Skript 2: předpoklady

Tento skript se připraví na další skript, ve kterém je akce koncového bodu. Tento skript vytvoří následující položky, které jsou zde uvedeny, ale pouze v případě, že ještě neexistují. Můžete přeskočit skript 2, pokud jste si jisti, že tyto položky již existují:

- Skupina prostředků Azure
- Logický server SQL

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
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create a server.";

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

    Write-Host "Create your server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your server already exists - $SqlDbServerName.";
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Vytvoření koncového bodu a pravidla

Tento skript vytvoří virtuální síť s podsítí. Pak skript přiřadí do vaší podsítě typ koncového bodu **Microsoft. SQL** . Nakonec skript přidá vaši podsíť do seznamu řízení přístupu (ACL), čímž vytvoří pravidlo.

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

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL Database ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>Skript 4: vyčištění

Tento finální skript odstraní prostředky, které byly vytvořeny z předchozích skriptů pro ukázku. Skript ale vyzve k potvrzení před tím, než odstraní následující:

- Logický server SQL
- Skupina prostředků Azure

Můžete spustit skript 4 kdykoli po dokončení skriptu 1.

### <a name="powershell-script-4-source-code"></a>Zdrojový kód skriptu PowerShellu 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL.";

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
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the server.";

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

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Ověření, jestli je podsíť koncovým bodem

Můžete mít podsíť, která už má přiřazený název typu **Microsoft. SQL** , což znamená, že se už jedná o koncový bod virtuální služby. K vytvoření pravidla virtuální sítě z koncového bodu můžete použít [Azure Portal][http-azure-portal-link-ref-477t] .

Nebo si možná nejste jisti, jestli má vaše podsíť název typu **Microsoft. SQL** . K provedení těchto akcí můžete spustit následující skript prostředí PowerShell:

1. Zjistíte, jestli má vaše podsíť název typu **Microsoft. SQL** .
2. Volitelně můžete přiřadit název typu, pokud chybí.
    - Skript vás vyzve k *potvrzení*, než použije chybějící název typu.

### <a name="phases-of-the-script"></a>Fáze skriptu

Tady jsou fáze skriptu PowerShellu:

1. Přihlaste se k účtu Azure, který je potřeba jenom jednou pro každou relaci PS.  Přiřaďte proměnné.
2. Vyhledejte svou virtuální síť a potom pro vaši podsíť.
3. Je vaše podsíť označená jako typ serveru **Microsoft. SQL** Endpoint?
4. Do podsítě přidejte koncový bod virtuální služby typu name **Microsoft. SQL**.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu musíte upravit hodnoty přiřazené k proměnným $-v horní části skriptu.

### <a name="direct-powershell-source-code"></a>Přímý zdrojový kód PowerShellu

Tento skript PowerShellu neaktualizuje žádné aktualizace, Pokud neodpovíte na Ano, pokud se zobrazí výzva k potvrzení. Skript může do podsítě přidat název typu **Microsoft. SQL** . Skript se ale pokusí přidat jenom v případě, že v dané podsíti chybí název typu.

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
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
