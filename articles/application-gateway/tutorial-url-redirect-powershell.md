---
title: Vytvoření služby application gateway pomocí adresy URL na základě cestu přesměrování - prostředí Azure PowerShell | Microsoft Docs
description: Postup vytvoření služby application gateway pomocí adresy URL na základě cesty přesměrovaného přenosů dat pomocí Azure PowerShell.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 85a684d4d988bdc01acdb3af3ddce028270cf105
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-azure-powershell"></a>Vytvoření služby application gateway pomocí adresy URL na základě cestu přesměrování pomocí Azure PowerShell

Prostředí Azure PowerShell můžete použít ke konfiguraci [pravidla směrování na základě adresy URL](application-gateway-url-route-overview.md) při vytváření [Aplikační brána](application-gateway-introduction.md). V tomto kurzu vytvoříte pomocí back-endové fondy [sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Pak vytvoříte pravidla směrování URL, která Ujistěte se, že webový provoz bude přesměrován na příslušné back-endový fond.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Přidejte naslouchací procesy a pravidla směrování
> * Vytvoření sady škálování virtuálního počítače pro back-endové fondy

Následující příklad ukazuje webový provoz přicházející ze porty 8080 a 8081 a směrovat do stejné back-endové fondy:

![Příklad směrování URL](./media/tutorial-url-redirect-powershell/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 3.6 nebo novější. Chcete-li najít verzi, spusťte ` Get-Module -ListAvailable AzureRM` . Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořit skupinu prostředků Azure pomocí [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Vytvoření konfigurací podsítě pro *myBackendSubnet* a *myAGSubnet* pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Vytvořit virtuální síť s názvem *myVNet* pomocí [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) s konfigurací podsítě. A nakonec vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress* pomocí [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Tyto prostředky se používají k poskytování síťové připojení k službě application gateway a její přidružené prostředky.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurace protokolu IP a port front-endu

Přidružení *myAGSubnet* aplikace brány pomocí dříve vytvořeného [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Přiřadit *myAGPublicIPAddress* do aplikace pomocí brány [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). A pak můžete vytvořit pomocí portu HTTP [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$pip = Get-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Vytvoření výchozího fondu a nastavení

Vytvořit výchozí fond back-end s názvem *appGatewayBackendPool* pro aplikace pomocí brány [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Nakonfigurujte nastavení fondu back-end pomocí [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Vytvořte naslouchací proces výchozí a pravidla

Naslouchací proces je nutný pro povolení služby application gateway pro směrování provozu správně do fondu back-end. V tomto kurzu vytvoříte více naslouchací procesy. První základní naslouchací proces očekává přenosy na adresy URL kořenového adresáře. Další moduly naslouchání očekávat provoz na konkrétní adresy URL, například *http://52.168.55.24:8080/images/* nebo *http://52.168.55.24:8081/video/*.

Vytvořte naslouchací proces s názvem *defaultListener* pomocí [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) s front-endovou konfiguraci a front-endový port, který jste dříve vytvořili. Pravidlo je vyžadována pro naslouchací proces vědět, kterému fondu back-end pro příchozí provoz. Vytvořte základní pravidlo s názvem *rule1 New* pomocí [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď, když jste vytvořili nezbytné doprovodné materiály, zadejte parametry pro službu application gateway s názvem *myAppGateway* pomocí [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku)a pak vytvořit pomocí [ Nový AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-backend-pools-and-ports"></a>Přidat back-endové fondy a porty

Back-endové fondy můžete přidat aplikační brány pomocí [přidat AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool). V tomto příkladu *imagesBackendPool* a *videoBackendPool* se vytvářejí. Přidávání portu front-endu pro fondy pomocí [přidat AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport). Potom odešlete změny do aplikace pomocí brány [Set-AzureRmApplicationGateway](/powershell/module/azurerm.network/set-azurermapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool 
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080
Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport `
  -Port 8081
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="add-listeners-and-rules"></a>Přidejte naslouchací procesy a pravidla

### <a name="add-listeners"></a>Přidejte naslouchací procesy

Přidejte naslouchací procesy s názvem *backendListener* a *redirectedListener* , jsou potřeba ke směrování provozu pomocí [přidat AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport
$redirectPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $redirectPort  
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-default-url-path-map"></a>Přidejte výchozí cestu mapování adresy URL

Adresa URL cesty mapy Ujistěte se, že konkrétní adresy URL jsou směrované na konkrétní back-endové fondy. Můžete vytvořit mapování cesty adresy URL, s názvem *imagePathRule* a *videoPathRule* pomocí [New-AzureRmApplicationGatewayPathRuleConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewaypathruleconfig) a [ Přidat AzureRmApplicationGatewayUrlPathMapConfig](/powershell/module/azurerm.network/add-azurermapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings
$imagePool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings
$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
  -Paths "/video/*" `
  -BackendAddressPool $videoPool `
  -BackendHttpSettings $poolSettings
Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-redirection-configuration"></a>Přidat konfiguraci přesměrování

Můžete konfigurovat přesměrování pro naslouchací proces pomocí [přidat AzureRmApplicationGatewayRedirectConfiguration](/powershell/module/azurerm.network/add-azurermapplicationgatewayredirectconfiguration).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendListener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener 
$redirectConfig = Add-AzureRmApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig `
  -RedirectType Found `
  -TargetListener $backendListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-url-path-map"></a>Přidat mapu cestu adresy URL přesměrování

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings
$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool
$redirectConfig = Get-AzureRmApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig
$redirectPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name redirectPathRule `
  -Paths "/images/*" `
  -RedirectConfiguration $redirectConfig
Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap `
  -PathRules $redirectPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rules"></a>Přidat pravidla směrování

Pravidla směrování URL mapy přidružit naslouchací procesy, které jste vytvořili. Můžete přidat pravidla s názvem *defaultRule* a *redirectedRule* pomocí [přidat AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).


```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener
$redirectlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener
$urlPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap
$redirectPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name defaultRule `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name redirectedRule `
  -RuleType PathBasedRouting `
  -HttpListener $redirectlistener `
  -UrlPathMap $redirectPathMap
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření sady škálování virtuálního počítače

V tomto příkladu můžete vytvořit tři sady škálování virtuálního počítače, které podporují tři back-endové fondy, které jste vytvořili. Jsou pojmenované sady škálování, které vytvoříte *myvmss1*, *myvmss2*, a *myvmss3*. Každá sada škálování obsahuje dvě instance virtuálního počítače, na kterých je nainstalovat službu IIS. Můžete přiřadit měřítka nastaven fond back-end při konfiguraci nastavení IP adresy.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$imagesPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }
  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
  $vmssConfig = New-AzureRmVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic
  Set-AzureRmVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
  Set-AzureRmVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i
  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalace služby IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Můžete použít [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) získat veřejnou IP adresu aplikační brány. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například *http://52.168.55.24*, *http://52.168.55.24:8080/images/test.htm*, *http://52.168.55.24:8080/video/test.htm*, nebo *http://52.168.55.24:8081/images/test.htm*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Otestovat základní adresu URL v aplikační brány](./media/tutorial-url-redirect-powershell/application-gateway-iistest.png)

Změňte adresu URL k http://&lt;ip adresu&gt;: 8080/video/test.htm, nahraďte IP adres pro &lt;ip adresu&gt;, a měli byste vidět něco podobného jako v následujícím příkladu:

![Testovací adresu URL bitové kopie v aplikační brány](./media/tutorial-url-redirect-powershell/application-gateway-iistest-images.png)

Změňte adresu URL k http://&lt;ip adresu&gt;: 8080/video/test.htm, nahraďte IP adres pro &lt;ip adresu&gt;, a měli byste vidět něco podobného jako v následujícím příkladu:

![Adresa URL videa testu v aplikační brány](./media/tutorial-url-redirect-powershell/application-gateway-iistest-video.png)

Nyní, změňte adresu URL k http://&lt;ip adresu&gt;: 8081/images/test.htm, nahraďte IP adres pro &lt;ip adresu&gt;, a měli byste vidět provoz přesměrován zpět do fondu back-end bitové kopie na http://&lt;ip adresu&gt;: 8080/bitové kopie.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Přidejte naslouchací procesy a pravidla směrování
> * Vytvoření sady škálování virtuálního počítače pro back-endové fondy

> [!div class="nextstepaction"]
> [Další informace o co můžete dělat s aplikační brány](application-gateway-introduction.md)
