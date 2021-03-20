---
title: Vytvoření Application Gateway Azure & přepište hlavičky HTTP
description: Tento článek poskytuje informace o tom, jak vytvořit Application Gateway Azure a jak přepsat hlavičky HTTP pomocí Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396851"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Vytvořit Aplikační bránu a přepsat hlavičky HTTP

Pomocí Azure PowerShell můžete nakonfigurovat [pravidla pro přepis hlaviček požadavků a odpovědí HTTP](rewrite-http-headers.md) při vytváření nového automatického [ŠKÁLOVÁNÍ a redundantní SKU aplikační brány Application Gateway](./application-gateway-autoscaling-zone-redundant.md) .

V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě automatického škálování
* Vytvoření vyhrazené veřejné IP adresy
* Nastavení infrastruktury služby Application Gateway
* Zadejte konfiguraci pravidla přepisu hlaviček protokolu HTTP.
* Určení automatického škálování
* Vytvoření služby Application Gateway
* Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste spustili Azure PowerShell místně. Musíte mít nainstalovanou verzi AZ Module verze 1.0.0 nebo novější. Spusťte `Import-Module Az` a pak `Get-Module Az` vyhledejte verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků v jednom z dostupných umístění.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s jednou vyhrazenou podsítí pro automatické škálování aplikační brány. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Zadejte metodu alokace PublicIPAddress as **static**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Načtení podrobností

Načte podrobnosti o skupině prostředků, podsíti a IP adrese v místním objektu, aby se vytvořily podrobnosti konfigurace protokolu IP pro aplikační bránu.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurace infrastruktury

Nakonfigurujte konfiguraci protokolu IP, front-endové konfiguraci IP adres, back-end fond, nastavení HTTP, certifikát, port a naslouchací proces ve stejném formátu na stávající standardní Aplikační bránu. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Zadejte konfiguraci pravidla přepisu hlaviček protokolu HTTP.

Nakonfigurujte nové objekty vyžadované pro přepsání hlaviček protokolu http:

- **RequestHeaderConfiguration**: Tento objekt slouží k zadání polí hlavičky požadavku, která mají být přepsána, a nové hodnoty, do které je třeba přepsat původní záhlaví.
- **ResponseHeaderConfiguration**: Tento objekt slouží k zadání polí hlavičky odpovědi, která mají být přepsána, a nové hodnoty, do které je třeba přepsat původní záhlaví.
- **ActionSet**: Tento objekt obsahuje konfigurace výše uvedených hlaviček požadavků a odpovědí. 
- **RewriteRule**: Tento objekt obsahuje všechny *actionSets* uvedené výše. 
- **RewriteRuleSet**– tento objekt obsahuje všechny *rewriteRules* a bude nutné ho připojit k pravidlu směrování požadavku – Basic nebo na základě cesty.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Zadat pravidlo směrování

Vytvořte pravidlo směrování požadavků. Po vytvoření se tato konfigurace přepsání připojí ke zdrojovému naslouchacího procesu prostřednictvím pravidla směrování. Při použití pravidla základního směrování je konfigurace přepsání hlaviček přidružená ke zdrojovému naslouchacího procesu a je to globální přepisování hlaviček. Při použití pravidla směrování na základě cesty je konfigurace opětovného zápisu hlaviček definovaná na mapě cesty URL. Platí to proto pouze pro konkrétní oblast cesty lokality. Níže je vytvořeno pravidlo základního směrování a je připojená sada pravidel přepsání.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Určení automatického škálování

Nyní můžete zadat konfiguraci automatického škálování pro aplikační bránu. Podporují se dva typy konfigurace automatického škálování:

* **Režim pevné kapacity**. V tomto režimu Application Gateway neprovádí automatické škálování a funguje s pevnou kapacitou jednotky škálování.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Režim automatického škálování**. V tomto režimu provádí Application Gateway automatické škálování podle vzoru provozu aplikací.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte Aplikační bránu a zahrňte do ní záložní zóny a konfiguraci automatického škálování.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy služby Application Gateway použijte Get-AzPublicIPAddress. Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejprve Prozkoumejte prostředky, které byly vytvořeny pomocí aplikační brány. Pokud už je nepotřebujete, můžete `Remove-AzResourceGroup` k odebrání skupiny prostředků, služby Application Gateway a všech souvisejících prostředků použít příkaz.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Další kroky

- [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)