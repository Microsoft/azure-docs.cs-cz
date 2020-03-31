---
title: Směrování webového provozu podle adresy URL – Azure PowerShell
description: Informace o tom, jak pomocí Azure PowerShellu směrovat webový provoz podle adresy URL na konkrétní škálovatelné fondy serverů
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8ca5c411db5644182f7e87f1ee1c63f3cbc4d2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73200404"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Směrování webového provozu podle adresy URL pomocí Azure PowerShellu

Pomocí Azure PowerShellu můžete nakonfigurovat směrování webového provozu na konkrétní škálovatelné fondy serverů podle adresy URL, přes kterou se přistupuje k vaší aplikaci. V tomto článku vytvoříte aplikační [bránu Azure](application-gateway-introduction.md) se třemi back-endovými fondy pomocí [škálovacích sad virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Každý back-endový fond má určitý účel, třeba běžná data, obrázky nebo videa.  Směrování provozu na samostatné fondy zajistí, aby zákazníci získali požadované informace v okamžiku, kdy je potřebují.

Pokud chcete směrování provozu povolit, vytvořte [pravidla směrování](application-gateway-url-route-overview.md) přiřazená naslouchacím procesům, které naslouchají na konkrétních portech. Tím zajistíte, že se webový provoz dostane na správné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořit naslouchací procesy, mapu cest adres URL a pravidla
> * Vytvořit škálovatelné back-endové fondy

![Příklad směrování na základě adresy URL](./media/tutorial-url-route-powershell/scenario.png)

Pokud chcete, můžete tento postup provést pomocí [azure cli](tutorial-url-route-cli.md) nebo [portálu Azure](create-url-route-portal.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Login-AzAccount` spustit k vytvoření připojení s Azure.

Vzhledem k době potřebné k vytvoření prostředků může dokončení tohoto postupu trvat až 90 minut.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků, která obsahuje všechny prostředky pro vaši aplikaci. 

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Bez ohledu na to, jestli už máte k dispozici stávající virtuální síť, nebo vytváříte novou, je nezbytné, aby obsahovala podsíť, která se používá jen pro brány Application Gateway. V tomto článku vytvoříte podsíť pro aplikační bránu a podsíť pro škálovací sady. Vytvoříte veřejnou IP adresu, která umožní přístup k prostředkům v bráně Application Gateway.

Vytvořte konfigurace podsítě *myAGSubnet* a *myBackendSubnet* pomocí [new-azVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Vytvořte virtuální síť s názvem *myVNet* pomocí [nové virtuální sítě](/powershell/module/az.network/new-azvirtualnetwork) s konfiguracemi podsítě. A nakonec vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress* pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Pomocí těchto prostředků se bude poskytovat síťové připojení k bráně Application Gateway a jejím přidruženým prostředkům.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

V této části vytvoříte prostředky, které podporují bránu Application Gateway, a nakonec vytvoříte bránu samotnou. Mezi prostředky, které vytvoříte, patří:

- *Konfigurace IP adres a front-end port* – Přidruží podsíť, kterou jste dříve vytvořili, k aplikační bráně a přiřadí port, který bude používat pro přístup k ní.
- *Výchozí fond* – všechny aplikační brány musí mít aspoň jeden back-endový fond serverů.
- *Výchozí naslouchací proces a pravidlo* – výchozí naslouchací proces naslouchá provozu na přiřazeném portu a výchozí pravidlo odesílá provoz výchozímu fondu.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

Přidružte *myAGSubnet,* který jste dříve vytvořili, k bráně aplikace pomocí [nové azapplicationgatewayIPconfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Přiřaďte *aplikaci myAGPublicIPAddress* pomocí [funkce New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Vytvoření výchozího fondu a nastavení

Vytvořte výchozí back-endový fond s názvem *appGatewayBackendPool* pro aplikační bránu pomocí [fondu New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Nakonfigurujte nastavení back-endového fondu pomocí [new-azapplicationgatewaybackendhttpsetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Vytvoření výchozího naslouchacího procesu a pravidla

Naslouchací proces je potřeba k tomu, aby brána Application Gateway mohla správně směrovat provoz na back-endový fond. V tomto článku vytvoříte dva naslouchací procesy. První základní naslouchací proces, který vytvoříte, naslouchá provozu na kořenové adrese URL. Druhý naslouchací proces, který vytvoříte, naslouchá provozu na konkrétních adresách URL.

Vytvořte výchozí naslouchací proces s názvem *myDefaultListener* pomocí [funkce New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) s konfigurací front-endu a portem front-endu, který jste dříve vytvořili. 

Aby naslouchací proces věděl, který back-endový fond se má použít pro příchozí provoz, potřebuje pravidlo. Vytvořte základní pravidlo s názvem *rule1* pomocí [new-azapplicationgatewayrequestroutingrule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď, když jste vytvořili potřebné podpůrné prostředky, zadejte parametry pro aplikační bránu s názvem *myAppGateway* pomocí [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)a pak ji vytvořte pomocí [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
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

Vytvoření brány aplikace může trvat až 30 minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část. 

V tomto okamžiku máte aplikační bránu, která naslouchá provozu na portu 80 a odesílá tento provoz do výchozího fondu serverů.

### <a name="add-image-and-video-backend-pools-and-port"></a>Přidání back-endových fondů a portu pro obrázky a video

Přidejte back-endové fondy s názvem *imagesBackendPool* a *videoBackendPool* do[aplikace, add-azApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Přidejte port front-end pro fondy pomocí [doplňku AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Odešlete změny do brány aplikace pomocí [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Aktualizace brány Application Gateway může taky trvat až 20 minut.

### <a name="add-backend-listener"></a>Přidání back-endového naslouchacího procesu

Přidejte back-endový naslouchací proces s názvem *backendListener,* který je potřeba ke směrování provozu pomocí [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Přidání mapy cest adres URL

Mapy cest adres URL zajišťují, že adresy URL odeslané do vaší aplikace se přesměrují na konkrétní back-endové fondy. Vytvořte mapy cesty URL s názvem *imagePathRule* a *videoPathRule* pomocí [New-AzApplicationGatewayPathRuleConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) a [Add-AzApplicationGatewayUrlPathConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Přidání pravidla směrování

Pravidlo směrování přidružuje mapu adres URL k naslouchacímu procesu, který jste vytvořili. Přidejte pravidlo s názvem *rule2* pomocí [add-azApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte tři škálovací sady virtuálních počítačů, které podporují tři vytvořené back-endové fondy. Vytvořené škálovací sady se budou jmenovat *myvmss1*, *myvmss2* a *myvmss3*. Škálovací sadu přiřadíte back-endovému fondu při konfiguraci nastavení IP adres.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
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

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalace služby IIS

Každá škálovací sada obsahuje dvě instance virtuálních počítačů, na které nainstalujete službu IIS.  Ukázková stránka je vytvořena k testování, zda funguje aplikační brána.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

Pomocí [služby Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) získáte veřejnou IP adresu aplikační brány. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm`, `http://52.168.55.24:8080/video/test.htm`nebo .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testování základní adresy URL v aplikační bráně](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Změňte adresu&lt;URL na&gt;http:// ip-adresu :8080/images/test.htm, která nahradí ip adresu pro &lt;IP adresu&gt;, a měli byste vidět něco jako v následujícím příkladu:

![Testování adresy URL obrázků v aplikační bráně](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Změňte adresu&lt;URL na&gt;http:// ip-adresu :8080/video/test.htm, která nahradí ip adresu pro &lt;IP adresu&gt;, a měli byste vidět něco jako v následujícím příkladu:

![Testování adresy URL videa v aplikační bráně](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odeberte skupinu prostředků, bránu aplikace a všechny související prostředky pomocí [remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

[Přesměrování webového provozu podle adresy URL](./tutorial-url-redirect-powershell.md)
