---
title: Konfigurace vlastních pravidel firewallu webových aplikací v2 pomocí Azure PowerShell
description: Přečtěte si, jak nakonfigurovat vlastní pravidla firewallu webových aplikací v2 pomocí Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263744"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Konfigurace vlastních pravidel firewallu webových aplikací v2 pomocí Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Pomocí vlastních pravidel můžete vytvořit vlastní pravidla, která se vyhodnotí pro každý požadavek, který projde Firewall webových aplikací (WAF). Tato pravidla mají vyšší prioritu než zbývající pravidla ve spravovaných sadách pravidel. Pro povolení úplného přizpůsobení mají vlastní pravidla akci (pro povolení nebo blokování), podmínku shody a operátor.

Tento článek vytvoří službu Azure Application Gateway WAF v2, která používá vlastní pravidlo. Vlastní pravidlo blokuje provoz, pokud Hlavička požadavku obsahuje User-Agent *evilbot*.

Další příklady vlastních pravidel najdete v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

Pokud chcete kód Azure PowerShell v tomto článku spustit v jednom souvislém skriptu, který můžete kopírovat, vkládat a spouštět, podívejte se na [ukázky Azure Application Gateway PowerShellu](powershell-samples.md).

## <a name="prerequisites"></a>Požadavky
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Potřebujete modul Azure PowerShell. Pokud se rozhodnete nainstalovat a používat Azure PowerShell místně, vyžaduje tento skript Azure PowerShell modulu verze 2.1.0 nebo novější. Postupujte následovně:

  1. Pokud chcete zjistit verzi, spusťte `Get-Module -ListAvailable Az`. Pokud potřebujete provést upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).
  2. Pokud chcete vytvořit připojení k Azure, spusťte `Connect-AzAccount`.

## <a name="example-script"></a>Ukázkový skript

### <a name="set-up-variables"></a>Nastavení proměnných

Spusťte následující kód:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Spusťte následující kód:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Spusťte následující kód:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Vytvoření statické veřejné virtuální IP adresy

Spusťte následující kód:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Vytvoření fondu a front-end portu

Spusťte následující kód:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Vytvoření naslouchacího procesu, nastavení HTTP, pravidla a automatického škálování

Spusťte následující kód:

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Vytvoření vlastního pravidla a jeho použití na zásady WAF

Spusťte následující kód:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Vytvoření aplikační brány

Spusťte následující kód:

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Další kroky

[Další informace o firewallu webových aplikací](waf-overview.md)
