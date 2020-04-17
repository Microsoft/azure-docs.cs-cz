---
title: Migrace zásad WAF pro azure aplikační bránu
description: Zjistěte, jak migrovat zásady Azure Web Application Firewall pomocí Azure PowerShellu.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 04/16/2020
ms.author: ant
ms.openlocfilehash: fb3b922b753b9696aa26ea189597589ecc5772db
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536620"
---
# <a name="migrate-web-application-firewall-policies-using-azure-powershell"></a>Migrace zásad brány firewall webových aplikací pomocí Azure PowerShellu

Tento skript usnadňuje přechod z konfigurace WAF nebo zásadwaf pouze pro vlastní pravidla na úplnou zásadu WAF. Na portálu se může zobrazit upozornění, které říká, že *migrujte do zásad WAF*, nebo můžete chtít nové funkce WAF ve verzi Public Preview, jako jsou vlastní pravidla geomatch, zásady WAF pro web a URI nebo sada pravidel pro zmírnění rizik a robotů. Chcete-li použít některou z těchto funkcí, potřebujete úplné zásady WAF přidružené k vaší aplikační bráně. 

Další informace o vytvoření nové zásady WAF naleznete v [tématu Vytvoření zásad brány firewall webových aplikací pro aplikaci Gateway](create-waf-policy-ag.md). Informace o migraci naleznete v [tématu Migrace do zásad WAF](create-waf-policy-ag.md#migrate-to-waf-policy).

## <a name="to-migrate-to-waf-policy-using-the-migration-script"></a>Migrace do zásad WAF pomocí skriptu pro migraci

Ke spuštění skriptu pro migraci použijte následující kroky: 

1. Otevřete následující okno prostředí cloud nebo otevřete jedno z portálu.
2. Zkopírujte skript do okna prostředí cloudu a spusťte jej.
3. Skript požádá o ID předplatného, název skupiny prostředků, název aplikační brány, ke které je přidružena konfigurace WAF, a název nové zásady WAF, které chcete vytvořit. Jakmile zadáte tyto vstupy, skript se spustí a vytvoří nové zásady WAF
4. Přidružte nové zásady WAF k bráně aplikace. Přejděte na zásadu WAF na portálu a vyberte kartu **Přidružené aplikační brány.** **Associate an Application Gateway**

> [!NOTE]
> Skript nedokončí migraci, pokud existují následující podmínky:
> - Celé pravidlo je zakázáno. Chcete-li dokončit migraci, ujistěte se, že není zakázána celá skupina pravidel.
> - Vyloučení položky s *rovná se libovolný* operátor. Chcete-li dokončit migraci, ujistěte se, že *položky* vyloučení s Rovná se Všechny operátor není k dispozici.
>
> Další informace naleznete v tématu *ValidateInput* funkce ve skriptu.

```azurepowershell-interactive
<#PSScriptInfo
.DESCRIPTION
Will be used to migrate to the application-gateway to a top level waf policy experience.

.VERSION 1.0

.GUID b6fedd43-ebd0-41ed-9847-4f1c1c43be22

.AUTHOR Venkat.Krishnan

.PARAMETER subscriptionId 
Subscription Id of where the resources are present.
.PARAMETER resourceGroupName
Resource-group where the resources are present.
.PARAMETER applicationGatewayName
Application-Gateway name
.PARAMETER wafPolicyName
Name of the web application firewall policy

.EXAMPLE
./migrateToWafPolicy.ps1 -subscriptionId  <your-subscription-id> -applicationGatewayName <your-appgw-name> -resourceGroupName <your-resource-group-name> -wafPolicyName <new-waf-policy-name>
#>

param(
    [Parameter(Mandatory=$true)]
    [string] $subscriptionId,
    [Parameter(Mandatory=$true)]
    [string] $resourceGroupName,
    [Parameter(Mandatory=$true)]
    [string] $applicationGatewayName,
    [Parameter(Mandatory=$true)]
    [string] $wafPolicyName
)

function ValidateInput ($appgwName, $resourceGroupName) {
    # Obtain the application-gateway
    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName
    if (-not $appgw) {
        Write-Error "ApplicationGateway: $applicationGatewayName is not present in ResourceGroup: $resourceGroupName"
        return $false
    }

    # Check if already have a global firewall policy
    if ($appgw.FirewallPolicy) {
        $fp = Get-AzResource -ResourceId $appgw.FirewallPolicy.Id
        if ($fp.PolicySettings) {
            Write-Error "ApplicationGateway: $applicationGatewayName already has a global firewall policy: $fp.Name. Please use portal for changing the policy."
            return $false
        }
    }

    if ($appgw.WebApplicationFirewallConfiguration) {
        # Throw an error, since ruleGroup disabled case can't be migrated now.
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                if ($disabled.Rules.Count -eq 0) {
                    $ruleGroupName = $disabled.RuleGroupName
                    Write-Error "The ruleGroup '$ruleGroupName' is disabled. Currently we can't migrate to a firewall policy when an entire ruleGroup is disabled. This feature will be delivered shortly. To continue, kindly ensure the entire rulegroups are not disabled. "
                    return $false
                }
            }
        }

        # Throw an error when exclusion entry with 'EqualsAny' operator is present
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($null -ne $excl.MatchVariable -and $null -eq $excl.SelectorMatchOperator -and $null -eq $excl.Selector) {
                    Write-Error " You have an exclusion entry(s) with the 'Equals any' operator. Currently we can't migrate to a firewall policy with 'Equals Any' operator. This feature will be delivered shortly. To continue, kindly ensure exclusion entries with 'Equals Any' operator is not present. "
                    return $false
                }
            }
        }
    }

    if ($appgw.Sku.Name -ne "WAF_v2" -or $appgw.Sku.Tier -ne "WAF_v2") {
        Write-Error " Cannot associate a firewall policy to application gateway :$applicationGatewayName since the Sku is not on WAF_v2"
        return $false
    }

    return $true
}

function Login() {
    $context = Get-AzContext
    if ($null -eq $context -or $null -eq $context.Account) {
        Login-AzAccount
    }
}

function createNewTopLevelWafPolicy ($subscriptionId, $resourceGroupName, $applicationGatewayName, $wafPolicyName) {
    Select-AzSubscription -Subscription $subscriptionId
    $retVal = ValidateInput -appgwName $applicationGatewayName -resourceGroupName $resourceGroupName
    if (!$retVal) {
        return
    }

    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName

    # Get the managedRule and PolicySettings
    $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule
    $policySetting = New-AzApplicationGatewayFirewallPolicySetting
    if ($appgw.WebApplicationFirewallConfiguration) {
        $ruleGroupOverrides = [System.Collections.ArrayList]@()
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                $rules = [System.Collections.ArrayList]@()
                if ($disabled.Rules.Count -gt 0) {
                    foreach ($rule in $disabled.Rules) {
                        $ruleOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleOverride -RuleId $rule
                        $_ = $rules.Add($ruleOverride)              
                    }
                }
                
                $ruleGroupOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleGroupOverride -RuleGroupName $disabled.RuleGroupName -Rule $rules
                $_ = $ruleGroupOverrides.Add($ruleGroupOverride)
            }
        }

        $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion 
        if ($ruleGroupOverrides.Count -ne 0) {
            $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion -RuleGroupOverride $ruleGroupOverrides 
        }
    
        $exclusions = [System.Collections.ArrayList]@()  
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($excl.MatchVariable -and $excl.SelectorMatchOperator -and $excl.Selector) {
                    $exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion -MatchVariable  $excl.MatchVariable -SelectorMatchOperator $excl.SelectorMatchOperator -Selector $excl.Selector
                    $_ = $exclusions.Add($exclusionEntry)               
                }
            }
        }
    
        $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet
        $exclCount = $exclusions.Count
        if ($exclCount -ne 0) {
            $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet -Exclusion $exclusions
        }

        
        $policySetting = New-AzApplicationGatewayFirewallPolicySetting -MaxFileUploadInMb $appgw.WebApplicationFirewallConfiguration.FileUploadLimitInMb -MaxRequestBodySizeInKb $appgw.WebApplicationFirewallConfiguration.MaxRequestBodySizeInKb -Mode Detection -State Disabled
        if ($appgw.WebApplicationFirewallConfiguration.FirewallMode -eq "Prevention") {
            $policySetting.Mode = "Prevention"
        }

        if ($appgw.WebApplicationFirewallConfiguration.Enabled) {
            $policySetting.State = "Enabled"
        }

        $policySetting.RequestBodyCheck = $appgw.WebApplicationFirewallConfiguration.RequestBodyCheck;
    }

    if ($appgw.FirewallPolicy) {
        $customRulePolicyId = $appgw.FirewallPolicy.Id
        $rg = Get-AzResourceGroup -Id $customRulePolicyId
        $crPolicyName = $customRulePolicyId.Substring($customRulePolicyId.LastIndexOf("/") + 1)
        $customRulePolicy = Get-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $crPolicyName
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $wafPolicyName -CustomRule $customRulePolicy.CustomRules -ManagedRule $managedRule -PolicySetting $policySetting -Location $appgw.Location
    } else { 
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name $wafPolicyName -ResourceGroupName $resourceGroupName -PolicySetting $policySetting -ManagedRule $managedRule -Location $appgw.Location
    }

    if (!$wafPolicy) {
        return
    }

    $appgw.FirewallPolicy = $wafPolicy
    $appgw = Set-AzApplicationGateway -ApplicationGateway $appgw
    Write-Host " firewallPolicy: $wafPolicyName has been created/updated successfully and applied to applicationGateway: $applicationGatewayName!"
    return $wafPolicy
}

function Main() {
    Login
    $policy = createNewTopLevelWafPolicy -subscriptionId $subscriptionId -resourceGroupName $resourceGroupName -applicationGatewayName $applicationGatewayName -wafPolicyName $wafPolicyName
    return $policy
}

Main
```
## <a name="next-steps"></a>Další kroky

Další informace o [skupinách a pravidlech](application-gateway-crs-rulegroups-rules.md)serveru FIREWALL brány firewall webových aplikací .
