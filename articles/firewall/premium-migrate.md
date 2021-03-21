---
title: Migrace na Azure Firewall Premium Preview
description: Naučte se migrovat z Azure Firewall Standard na Azure Firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549622"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrace na Azure Firewall Premium Preview

Můžete migrovat Azure Firewall Standard na Azure Firewall Premium Preview, abyste mohli využít výhod nových prémiových funkcí. Další informace o funkcích Azure Firewall Premium ve verzi Preview najdete v tématu [funkce Azure firewall Premium Preview](premium-features.md).

Následující dva příklady ukazují:
- Migrace existujících standardních zásad pomocí Azure PowerShell
- Migrujte stávající standardní bránu firewall (s klasickými pravidly) na Azure Firewall Premium se zásadami Premium.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrace existujících zásad pomocí Azure PowerShell

`Transform-Policy.ps1` je skript Azure PowerShell, který vytvoří novou zásadu Premium z existujících standardních zásad.

Skript pro standardní ID zásad brány firewall ho transformuje na zásadu Premium Azure Firewall. Skript se nejdřív připojí k vašemu účtu Azure, vyžádá si zásadu, transformuje/přidá různé parametry a pak nahraje nové zásady Premium. Nové zásady Premium se nazývají `<previous_policy_name>_premium` .

Příklad použití:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Skript nemigruje nastavení analýzy hrozeb. Tato nastavení budete muset před pokračováním a ruční migrací poznamenat.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrace stávající standardní brány firewall pomocí Azure Portal

Tento příklad ukazuje, jak použít Azure Portal k migraci standardní brány firewall (klasická pravidla) na Azure Firewall Premium se zásadami Premium.

1. Z Azure Portal vyberte standardní bránu firewall. Na stránce **Přehled** vyberte možnost **migrovat na zásadu brány firewall**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrace na zásady brány firewall":::

1. Na stránce **migrace na zásadu brány firewall** vyberte **zkontrolovat + vytvořit**.
1. Vyberte **Vytvořit**.

   Dokončení nasazení trvá několik minut.
1. Použijte `Transform-Policy.ps1` [skript Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) k transformaci těchto nových standardních zásad na zásadu Premium.
1. Na portálu vyberte svůj standardní prostředek brány firewall. 
1. V části **Automatizace** vyberte **Exportovat šablonu**. Tuto kartu prohlížeče nechte otevřenou. Později se k ní vrátíte.
   > [!TIP]
   > Abyste se ujistili, že šablonu neztratíte, stáhněte ji a uložte v případě, že se vaše karta prohlížeče zavře nebo aktualizuje.
1. Otevřete novou kartu prohlížeče, přejděte do Azure Portal a otevřete skupinu prostředků, která obsahuje vaši bránu firewall.
1. Odstraňte existující standardní instanci brány firewall.

   Tato akce trvá několik minut.

1. Vraťte se na kartu prohlížeče s vyexportovanou šablonou.
1. Vyberte **nasadit** a pak na stránce **vlastní nasazení** vyberte **Upravit šablonu**.
1. Upravte text šablony:
   
   1. V části `Microsoft.Network/azureFirewalls` prostředek v části `Properties` , `sku` změňte hodnotu `tier` z "standardní" na "Premium".
   1. V šabloně `Parameters` změňte na `defaultValue` `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` z:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      na

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Vyberte **Uložit**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

Po dokončení nasazení teď můžete nakonfigurovat všechny nové funkce verze Preview Azure Firewall Premium.

## <a name="next-steps"></a>Další kroky

- [Další informace o funkcích Azure Firewall Premium](premium-features.md)