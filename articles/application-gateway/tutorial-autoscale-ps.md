---
title: 'Kurz: Vytvoření automaticky škálované zónově redundantní Application Gateway s vyhrazenou IP adresou – Azure PowerShell'
description: V tomto kurzu se naučíte se vytvořit automatickým Škálováním, zónově redundantní služba application gateway s vyhrazenou IP adresu pomocí Azure Powershellu.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323196"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Kurz: Vytvoření služby application gateway, která zvyšuje přístup k webové aplikaci

Pokud jste obeznámeni s zlepšení přístup k webové aplikaci správce IT, můžete optimalizovat vaše brána application gateway škálování zákazníků na základě poptávky a span několika zónami dostupnosti. Tento kurz vám pomůže s konfigurací funkce Azure Application Gateway, které to: automatické škálování, zóna redundance a vyhrazené virtuální IP adresy (statické IP adresy). Chcete-li vyřešit tento problém budete používat rutin Azure Powershellu a modelu nasazení Azure Resource Manageru.

> [!IMPORTANT] 
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě automatického škálování
> * Vytvoření vyhrazené veřejné IP adresy
> * Nastavení infrastruktury application gateway
> * Určení automatického škálování
> * Vytvoření služby Application Gateway
> * Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje místní použití Azure PowerShellu. Musíte mít nainstalovaný modul Azure PowerShell verze 6.9.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzureRmAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků v jednom z dostupných umístění.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě s jednou podsítí vyhrazenou pro službu application gateway automatického škálování. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Zadejte metodu přidělování PublicIPAddress jako **statické**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Načtení podrobností

Načtěte podrobnosti o skupině prostředků, podsíti a IP v místní objekt k vytvoření podrobnosti konfigurace IP adresy pro službu application gateway.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurace infrastruktury

Nakonfigurujte IP konfigurace, front-endové konfigurace protokolu IP, back endového fondu, HTTP nastavení, certifikát, port, naslouchací proces a pravidlo ve formátu identické ke stávající standardní application gateway. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Určení automatického škálování

Nyní můžete zadat konfiguraci automatického škálování pro službu application gateway. Podporují se dva typy konfigurace automatického škálování:

* **Režim pevné kapacity**. V tomto režimu Application Gateway neprovádí automatické škálování a funguje s pevnou kapacitou jednotky škálování.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Režim automatického škálování**. V tomto režimu provádí Application Gateway automatické škálování podle vzoru provozu aplikací.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Vytvoření služby application gateway a zahrnují konfiguraci automatického škálování a redundanci zón.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Get-AzureRmPublicIPAddress použijte k získání veřejné IP adresy služby application gateway. Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejprve si projděte prostředky, které byly vytvořeny ve službě application gateway. Pokud jste už nepotřebujete, můžete použít `Remove-AzureRmResourceGroup` příkazu k odebrání skupiny prostředků, služba application gateway a všechny související prostředky.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
