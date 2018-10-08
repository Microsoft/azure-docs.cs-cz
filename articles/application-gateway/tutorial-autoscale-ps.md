---
title: Vytvoření automaticky škálované zónově redundantní Application Gateway s vyhrazenou IP adresou – Azure PowerShell
description: Zjistěte, jak vytvořit automaticky škálovanou zónově redundantní Application Gateway s vyhrazenou IP adresou pomocí Azure PowerShellu.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6c54706f45653c43e6b41d0adb3132583079e6b6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167522"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>Kurz: Vytvoření automaticky škálované zónově redundantní Application Gateway s vyhrazenou IP adresou pomocí Azure PowerShellu

Tento kurz popisuje, jak vytvořit Azure Application Gateway pomocí rutin Azure PowerShellu a modelu nasazení Azure Resource Manageru. Tento kurz se zaměřuje na rozdíly v nové automaticky škálované skladové položce v porovnání s existující standardní skladovou položkou. Konkrétně na funkce pro podporu automatického škálování a redundanci zón a vyhrazené virtuální IP adresy (statické IP adresy).

Další informace o automatickém škálování a redundanci zón Application Gateway najdete v článku o [automaticky škálované a zónově redundantní Application Gateway (Public Preview)](application-gateway-autoscaling-zone-redundant.md).

> [!IMPORTANT]
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení parametru konfigurace automatického škálování
> * Použití parametru zón
> * Použití statické virtuální IP adresy
> * Vytvoření služby Application Gateway


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento kurz vyžaduje místní použití Azure PowerShellu. Musíte mít nainstalovaný modul Azure PowerShell verze 6.9.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzureRmAccount`, abyste vytvořili připojení k Azure.

## <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

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

## <a name="create-a-vnet"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť s jednou vyhrazenou podsítí pro automaticky škálovanou Application Gateway. Aktuálně je možné v každé vyhrazené podsíti nasadit jenom jednu automaticky škálovanou Application Gateway.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Vytvoření vyhrazené veřejné IP adresy

Určete metodu přidělování PublicIPAddress jako **Statická**. Virtuální IP adresa automaticky škálované Application Gateway může být jenom statická. Dynamické IP adresy nejsou podporované. Podporovaná je jenom standardní skladová položka PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Načtení podrobností

Načtěte podrobnosti o skupině prostředků, podsíti a IP v místním objektu k vytvoření podrobností konfigurace IP Application Gateway.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>Konfigurace infrastruktury Application Gateway
Nakonfigurujte konfiguraci IP, konfigurace IP front-endu, back-endový fond, nastavení protokolu http, certifikát, port, naslouchací proces a pravidlo ve stejném formátu jako ve stávající standardní Application Gateway. Nová skladová položka se řídí stejným modelem objektu jako standardní skladová položka.

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

Teď můžete zadat konfiguraci automatického škálování pro Application Gateway. Podporují se dva typy konfigurace automatického škálování:

- **Režim pevné kapacity**. V tomto režimu Application Gateway neprovádí automatické škálování a funguje s pevnou kapacitou jednotky škálování.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
- **Režim automatického škálování**. V tomto režimu provádí Application Gateway automatické škálování podle vzoru provozu aplikací.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte Application Gateway a zahrňte zóny redundance. 

Konfigurace zón je podporovaná jenom v oblastech, kde jsou k dispozici zóny Azure. V oblastech, kde zóny Azure k dispozici nejsou, by se parametr zón používat neměl. Application Gateway je možné nasadit také v jedné zóně, dvou zónách nebo všech třech zónách. Parametr PublicIPAddress pro Application Gateway s jednou zónou musí být svázaný se stejnou zónou. Pro Application Gateway se dvěma nebo třemi redundantními zónami musí být parametr PublicIPAddress také zónově redundantní, takže bez zadané zóny.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány použijte rutinu [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress). Zkopírujte veřejnou IP adresu nebo název DNS a pak vložte do adresního řádku prohlížeče.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Vyčištění prostředků
Nejdřív prozkoumejte prostředky vytvořené pomocí Application Gateway a pak, až je nebudete potřebovat, odstraňte pomocí příkazu `Remove-AzureRmResourceGroup` skupinu prostředků, Application Gateway a všechny související prostředky.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použití statické virtuální IP adresy
> * Nastavení parametru konfigurace automatického škálování
> * Použití parametru zón
> * Vytvoření služby Application Gateway

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
