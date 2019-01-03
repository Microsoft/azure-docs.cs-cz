---
title: Přepsání hlavičky protokolu HTTP ve službě Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak vytvořit služby Azure Application Gateway a přepište hlavičky protokolu HTTP pomocí Azure Powershellu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 8bc0d53080d0653ae630765d8a656cbe8d50b24c
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971565"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Kurz: Vytvoření služby application gateway a přepište hlavičky protokolu HTTP

Prostředí Azure PowerShell můžete použít ke konfiguraci [pravidla pro přepsání hlavičky požadavku a odpovědi HTTP](rewrite-http-headers.md) při vytváření nového [automatické škálování a zónově redundantní služba application gateway SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření virtuální sítě automatického škálování
> * Vytvoření vyhrazené veřejné IP adresy
> * Nastavení infrastruktury application gateway
> * Zadat záhlaví http přepsat konfiguraci pravidel
> * Určení automatického škálování
> * Vytvoření služby Application Gateway
> * Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje místní použití Azure PowerShellu. Musí mít Az modulu verze 1.0.0 nebo novější. Spustit `Import-Module Az` a potom`Get-Module Az` k vyhledání verze. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

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

Vytvoření virtuální sítě s jednou podsítí vyhrazenou pro službu application gateway automatického škálování. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Zadejte metodu přidělování PublicIPAddress jako **statické**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Načtení podrobností

Načtěte podrobnosti o skupině prostředků, podsíti a IP v místní objekt k vytvoření podrobnosti konfigurace IP adresy pro službu application gateway.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurace infrastruktury

Nakonfigurujte IP konfigurace, front-endové konfigurace protokolu IP, back endového fondu, HTTP nastavení, certifikát, port a naslouchací proces ve formátu identické ke stávající standardní application gateway. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Zadat záhlaví HTTP přepsat konfiguraci pravidel

Konfigurace nové objekty požadované pro přepsání hlavičky protokolu http:

- **RequestHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky požadavku a nové hodnoty, které musí být přepsány, aby původní záhlaví.
- **ResponseHeaderConfiguration**: Tento objekt se používá k určení, které chcete přepsat pole hlavičky odpovědi a nové hodnoty, které musí být přepsány, aby původní záhlaví.
- **ActionSet**: Tento objekt obsahuje konfigurace z výše uvedených hlaviček žádostí a odpovědí. 
- **RewriteRule**: Tento objekt obsahuje všechny *actionSets* uvedeno výše. 
- **RewriteRuleSet**– tento objekt obsahuje všechny *rewriteRules* a musí být připojené k pravidlo směrování požadavku – basic nebo na základě cest.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Zadejte pravidlo směrování

Vytvořte pravidlo směrování požadavku. Po vytvoření této revize konfigurace je připojen k zdroj naslouchací proces prostřednictvím pravidla směrování. Při použití základních pravidel směrování, konfigurace přepsání hlavičky je přidružený zdroj naslouchací proces a je globální záhlaví revize. Při použití pravidel směrování na základě cest, přepište Konfigurace hlavičky je definován na mapě cestu adresy URL. Ano platí jen pro konkrétní cesty oblasti lokality. Níže vytvořili základní pravidlo směrování a přepište sada pravidel je připojen.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Určení automatického škálování

Nyní můžete zadat konfiguraci automatického škálování pro službu application gateway. Podporují se dva typy konfigurace automatického škálování:

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

Vytvoření služby application gateway a zahrnují konfiguraci automatického škálování a redundanci zón.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Get-AzureRmPublicIPAddress použijte k získání veřejné IP adresy služby application gateway. Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejprve si projděte prostředky, které byly vytvořeny ve službě application gateway. Pokud jste už nepotřebujete, můžete použít `Remove-AzResourceGroup` příkazu k odebrání skupiny prostředků, služba application gateway a všechny související prostředky.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
