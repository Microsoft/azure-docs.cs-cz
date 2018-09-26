---
title: Prostředí PowerShell pro koncové body služeb virtuální sítě a pravidla v Azure SQL | Dokumentace Microsoftu
description: Poskytuje skripty prostředí PowerShell k vytváření a správě koncových bodů služby virtuální pro Azure SQL Database a SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: DhruvMsft
ms.author: dmalik
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 50e88dd11b8a883a4d2999ad2d0419cbf7176078
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161144"
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database-and-sql-data-warehouse"></a>Použití Powershellu k vytvoření koncového bodu služby virtuální a pravidla pro Azure SQL Database a SQL Data Warehouse

Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) podporu koncových bodů služby virtuální. 

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

Tento článek obsahuje a vysvětluje Powershellový skript, který provede následující akce:

1. Microsoft Azure vytvoří *koncový bod služby virtuální* na vaší podsítě.
2. Přidá koncový bod do brány firewall vašeho serveru Azure SQL Database, chcete-li vytvořit *pravidlo virtuální sítě*.

Vaše podněty pro vytvoření pravidla jsou vysvětlené v: [koncové body služby virtuální pro službu Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Pokud všechno, co potřebujete k vyhodnocení nebo přidat koncový bod služby virtuální *název typu* pro službu SQL Database pro vaši podsíť, můžete přeskočit přímo na naše více [přímé skript prostředí PowerShell](#a-verify-subnet-is-endpoint-ps-100).

#### <a name="major-cmdlets"></a>Hlavní rutiny

V tomto článku klade důraz rutinu s názvem **New-AzureRmSqlServerVirtualNetworkRule**, přidává koncový bod podsítě do seznamu řízení přístupu (ACL) vašeho serveru Azure SQL Database, a tím vytváření pravidla.

Následující seznam obsahuje řadu dalších *hlavní* rutiny, které je nutné spustit Příprava volání **New-AzureRmSqlServerVirtualNetworkRule**. V tomto článku se tato volání probíhá [skript 3 "pravidlo virtuální sítě"](#a-script-30):

1. [Nový-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): vytvoří objekt podsítě.

2. [Nový-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): vytvoří virtuální síť, že mu poskytneme podsíť.

3. [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): přiřadí koncového bodu služby virtuální podsítě.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): nevyřeší aktualizace provedené k virtuální síti.

5. [Nový-AzureRmSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlservervirtualnetworkrule): po vaší podsítě je koncový bod, přidá vaší podsítě jako pravidlo virtuální sítě do seznamu ACL vašeho serveru Azure SQL Database.
    - Nabízí parametr **- IgnoreMissingVnetServiceEndpoint**začíná v modulu Azure RM Powershellu verze 5.1.1.

#### <a name="prerequisites-for-running-powershell"></a>Požadavky pro spuštění Powershellu

- Můžete už přihlášení do Azure, jako například prostřednictvím [webu Azure portal][http-azure-portal-link-ref-477t].
- Již můžete spustit skripty prostředí PowerShell.

> [!NOTE]
> Ujistěte se prosím, že jsou zapnuté koncových bodů služby pro virtuální síť/podsíť, kterou chcete přidat k vašemu serveru v opačném případě, že vytvoření pravidla brány Firewall virtuální sítě se nezdaří.

#### <a name="one-script-divided-into-four-chunks"></a>Jeden skript rozdělit do čtyř bloků dat

Náš ukázkový skript Powershellu se dělí posloupnost menší skripty. Rozdělení usnadňuje učení a poskytuje flexibilitu. Skripty musí být spuštěn v jejich uvedeném pořadí. Pokud nemáte čas na spouštění skriptů, náš skutečný test výstupu se zobrazí po skriptu 4.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Skript 1: proměnné

Tento první skript prostředí PowerShell přiřadí hodnoty proměnné. Následující skripty závisí na těchto proměnných.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu můžete upravit hodnoty, pokud chcete, můžete. Pokud již máte skupinu prostředků, můžete chtít upravit název skupiny prostředků jako hodnota.
>
>  Do skriptu by měl být upraven název vašeho odběru.

#### <a name="powershell-script-1-source-code"></a>Zdrojový kód skriptu 1 prostředí PowerShell

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

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

## <a name="script-2-prerequisites"></a>Skript 2: požadavky

Připraví tento skript pro další skript, kde je tato akce koncový bod. Tento skript vytvoří za vás následující uvedené položky, ale pouze pokud ještě neexistují. Skript 2 můžete přeskočit, pokud jste si jistí, že už tyto položky:

- Skupina prostředků Azure
- Server služby Azure SQL Database

#### <a name="powershell-script-2-source-code"></a>Zdrojový kód skriptu 2 prostředí PowerShell

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
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

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
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

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Vytvoření koncového bodu a pravidla

Tento skript vytvoří virtuální síť s podsítí. Potom tento skript přiřadí **Microsoft.Sql** typ koncového bodu pro vaši podsíť. Nakonec skript přidá vaší podsítě do seznamu řízení přístupu (ACL) serveru služby SQL Database a vytvoření pravidla.

#### <a name="powershell-script-3-source-code"></a>Zdrojový kód skriptu 3 prostředí PowerShell

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skript 4: Čisticí

Tento poslední skript odstraní prostředky, které předchozí skripty vytvořené pro ukázky. Ale skript vyzve k zadání potvrzení před odstraní následující:

- Server služby Azure SQL Database
- Skupina prostředků Azure

Skript 4 můžete spustit kdykoli po dokončení skriptu 1.

#### <a name="powershell-script-4-source-code"></a>Zdrojový kód skriptu 4 prostředí PowerShell

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
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
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Aktuální výstup ze skriptů 1 až 4

Výstup z našich testů se zobrazí v dalším kroku ve zkráceném formátu. Výstup může být užitečný v případě, že nechcete skutečně nyní spouštět skripty prostředí PowerShell.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Toto je konec naší hlavní skript prostředí PowerShell.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Ověřte, že vaše podsíť je koncový bod

Můžete mít podsíť, která již byla přiřazena **Microsoft.Sql** název typu, což znamená, je již koncového bodu služby virtuální. Můžete použít [webu Azure portal] [ http-azure-portal-link-ref-477t] k vytvoření pravidla virtuální sítě z koncového bodu.

Nebo, můžete být jisti, jestli má vaše podsíť **Microsoft.Sql** název typu. Můžete spustit následující skript Powershellu pro provedení těchto akcí:

1. Zjistit, jestli má vaše podsíť **Microsoft.Sql** název typu.
2. Volitelně můžete přiřadíte název typu, pokud je zcela chybět.
    - Skript vyzve k *potvrďte*, než použije typ chybí název.

#### <a name="phases-of-the-script"></a>Fáze skriptu

Zde jsou uvedeny jednotlivé fáze skriptu prostředí PowerShell:

1. PŘIHLASTE se ke svému účtu Azure potřeba jenom jednou za Vstupte do relace PSSession.  Přiřaďte proměnné.
2. Hledat pro vaši virtuální síť a pak pro vaši podsíť.
3. Je vaše podsíť označené jako **Microsoft.Sql** typ koncového bodu serveru?
4. Přidat koncový bod služby virtuální názvu typu **Microsoft.Sql**, na vaší podsítě.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu je nutné upravit hodnoty přiřazené k $– proměnné, v horní části skriptu.

#### <a name="direct-powershell-source-code"></a>Přímé zdrojový kód Powershellu

Toto prostředí PowerShell skriptu neaktualizuje nic, Pokud odpovíte Ano, pokud se zobrazí výzvu k potvrzení. Skript můžete přidat název typu **Microsoft.Sql** pro vaši podsíť. Ale jenom v případě, že vaše podsíť chybí název typu se pokusí přidat skript.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Aktuální výstup

Následující blok zobrazuje náš skutečný zpětné vazby (pomocí kosmetické změny).

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

