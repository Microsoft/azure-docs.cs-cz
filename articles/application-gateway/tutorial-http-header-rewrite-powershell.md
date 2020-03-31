---
title: Vytvoření brány aplikace Azure & přepsání hlaviček HTTP
description: Tento článek obsahuje informace o tom, jak vytvořit aplikační bránu Azure a přepsat hlavičky HTTP pomocí Azure PowerShellu
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173721"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Vytvoření aplikační brány a přepsání hlaviček HTTP

Azure PowerShell můžete použít ke konfiguraci [pravidel pro přepsání hlavičky požadavků HTTP a odpovědí](rewrite-http-headers.md) při vytváření nové [hodovací jednotky automatického škálování a zónově redundantní aplikační brány.](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvoření virtuální sítě automatického škálování
> * Vytvoření vyhrazené veřejné IP adresy
> * Nastavení infrastruktury aplikační brány
> * Určení konfigurace pravidla přepisu záhlaví http
> * Určení automatického škálování
> * Vytvoření služby Application Gateway
> * Testování brány Application Gateway

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste azure powershell uběhli místně. Musíte mít nainstalovaný modul Az verze 1.0.0 nebo novější. `Import-Module Az` Spusťte`Get-Module Az` a pak najít verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

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

Vytvořte virtuální síť s jednou vyhrazenou podsítí pro aplikační bránu automatického škálování. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Zadejte metodu přidělení PublicIPAddress jako **Statické**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Načtení podrobností

Načtěte podrobnosti o skupině prostředků, podsíti a IP v místním objektu a vytvořte podrobnosti konfigurace IP pro aplikační bránu.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurace infrastruktury

Nakonfigurujte konfiguraci protokolu IP, front-endovou konfiguraci IP, back-endový fond, nastavení PROTOKOLU HTTP, certifikát, port a naslouchací proces ve stejném formátu jako stávající standardní aplikační brána. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Určení konfigurace pravidla přepisu hlavičky HTTP

Nakonfigurujte nové objekty potřebné k přepsání záhlaví http:

- **RequestHeaderConfiguration**: Tento objekt se používá k určení polí hlavičky požadavku, která chcete přepsat, a nové hodnoty, do které je třeba přepsat původní záhlaví.
- **ResponseHeaderConfiguration**: Tento objekt se používá k určení polí hlavičky odpovědi, která chcete přepsat, a nové hodnoty, do které je třeba přepsat původní záhlaví.
- **ActionSet**: tento objekt obsahuje konfigurace výše uvedených hlavíček požadavků a odpovědí. 
- **Přepsání:** tento objekt obsahuje všechny výše uvedené *sady akcí.* 
- **RewriteRuleSet**- tento objekt obsahuje všechny *přepsatRules* a bude muset být připojen k pravidlu směrování požadavku - základní nebo na základě cesty.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Určení pravidla směrování

Vytvořte pravidlo směrování požadavků. Po vytvoření je tato konfigurace přepsání připojena ke zdrojovému naslouchací procesu prostřednictvím pravidla směrování. Při použití základního pravidla směrování je konfigurace přepsání záhlaví přidružena ke zdrojovému naslouchací procesu a je přepsáním globální hlavičky. Při použití pravidla směrování založeného na cestě je konfigurace přepsání záhlaví definována na mapě cesty URL. Takže se vztahuje pouze na konkrétní oblast cesty webu. Níže je vytvořeno základní pravidlo směrování a je připojena sada pravidel pro přepsání.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Určení automatického škálování

Nyní můžete určit konfiguraci automatického škálování pro aplikační bránu. Podporují se dva typy konfigurace automatického škálování:

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

Vytvořte aplikační bránu a zahrňte zóny redundance a konfiguraci automatického škálování.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

Pomocí služby Get-AzPublicIPAddress získáte veřejnou IP adresu aplikační brány. Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejprve prozkoumejte prostředky, které byly vytvořeny pomocí brány aplikace. Když už je nepotřebujete, můžete pomocí `Remove-AzResourceGroup` příkazu odebrat skupinu prostředků, bránu aplikace a všechny související prostředky.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Další kroky

- [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
