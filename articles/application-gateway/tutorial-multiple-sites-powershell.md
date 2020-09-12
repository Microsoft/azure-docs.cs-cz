---
title: Hostování více webů pomocí prostředí PowerShell
titleSuffix: Azure Application Gateway
description: Přečtěte si, jak vytvořit v Azure PowerShellu aplikační bránu, která hostuje více webů.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/20/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd73dc69fc2d40a0b4c24739dca6ad8174ad1047
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595836"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-azure-powershell"></a>Vytvoření aplikační brány v Azure PowerShellu, která hostuje více webů

Při vytvoření [aplikační brány](multiple-site-overview.md) můžete Azure PowerShell použít ke [konfiguraci hostování více webů](overview.md). V tomto článku definujete fondy back-end adres pomocí sad škálování virtuálních počítačů. Pak na základě domén, které vám patří, nakonfigurujete naslouchací procesy a pravidla, aby se webový provoz přesměroval na příslušné servery ve fondech. V tomto článku se předpokládá, že vlastníte více domén a používáte příklady *www.contoso.com* a *www.fabrikam.com*.

V tomto článku získáte informace o těchto tématech:

* Nastavit síť
* Vytvoření brány Application Gateway
* Vytvořit back-endové naslouchací procesy
* Vytvořit pravidla směrování
* Vytvořit z back-endových fondů škálovací sadu virtuálních počítačů
* Vytvoření záznamu CNAME v doméně

:::image type="content" source="./media/tutorial-multiple-sites-powershell/scenario.png" alt-text="Application Gateway více lokalit":::

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Vytvořte konfigurace podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) s konfiguracemi podsítí. A nakonec Vytvořte veřejnou IP adresu pomocí příkazu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Pomocí těchto prostředků se bude poskytovat síťové připojení k bráně Application Gateway a jejím přidruženým prostředkům.

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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

Přidružte podsíť, kterou jste dříve vytvořili ve službě Application Gateway pomocí [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Přiřaďte k aplikační bráně veřejnou IP adresu pomocí [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

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

### <a name="create-the-backend-pools-and-settings"></a>Vytvoření back-endového fondu a nastavení

Vytvořte první back-end fond adres pro aplikační bránu pomocí [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Nakonfigurujte nastavení pro fond pomocí [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool

$fabrikamPool = New-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Vytvoření naslouchacích procesů a pravidel

Naslouchací procesy jsou potřeba k tomu, aby aplikační brána správně směrovala provoz na back-endové fondy adres. V tomto článku vytvoříte dva naslouchací procesy pro vaše dvě domény. Pro domény *contoso.com* a *fabrikam.com* se vytvářejí naslouchací procesy.

Vytvořte první naslouchací proces pomocí [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) s konfigurací front-end a dříve vytvořeným portem front-endu. Aby naslouchací proces věděl, který back-endový fond se má použít pro příchozí provoz, potřebuje pravidlo. Vytvořte základní pravidlo s názvem *contosoRule* pomocí [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

>[!NOTE]
> Pomocí Application Gateway nebo WAF v2 SKU můžete také nakonfigurovat až 5 názvů hostitelů na naslouchací proces a v názvu hostitele můžete použít zástupné znaky. Další informace najdete [v tématu názvy hostitelů se zástupnými znaky v naslouchací](multiple-site-overview.md#wildcard-host-names-in-listener-preview) službě.
>Chcete-li použít více názvů hostitelů a zástupných znaků v naslouchací službě pomocí Azure PowerShell, je nutné použít `-HostNames` místo `-HostName` . S názvy hostitelů můžete uvést až 5 názvů hostitelů jako hodnoty oddělené čárkami. Například `-HostNames "*.contoso.com,*.fabrikam.com"`.

```azurepowershell-interactive
$contosolistener = New-AzApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"

$fabrikamlistener = New-AzApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"

$contosoRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings

$fabrikamRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď, když jste vytvořili potřebné podpůrné prostředky, zadejte parametry pro aplikační bránu pomocí [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)a pak ji vytvořte pomocí [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte dvě škálovací sady virtuálních počítačů, které podporují dva vytvořené back-endové fondy. Vytvořené škálovací sady se jmenují *myvmss1* a *myvmss2*. Každá škálovací sada obsahuje dvě instance virtuálních počítačů, na které nainstalujete službu IIS. Škálovací sadu přiřadíte back-endovému fondu při konfiguraci nastavení IP adres.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$contosoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw

$fabrikamPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
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

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i

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

## <a name="create-cname-record-in-your-domain"></a>Vytvoření názvu CNAME v doméně

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS služby Application Gateway můžete použít [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) . Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. Použití záznamů A se nedoporučuje, protože virtuální IP adresa se může změnit při restartování služby Application Gateway v SKU v1.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Do adresního řádku prohlížeče zadejte název domény. Například http: \/ /www.contoso.com.

![Testování webu Contoso v aplikační bráně](./media/tutorial-multiple-sites-powershell/application-gateway-iistest.png)

Změňte adresu na jinou ze svých domén. Měli byste vidět něco podobného jako v následujícím příkladu:

![Testování webu Fabrikam v aplikační bráně](./media/tutorial-multiple-sites-powershell/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, aplikační bránu a všechny související prostředky pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

[Vytvoření aplikační brány s pravidly směrování založenými na cestě URL](./tutorial-url-route-powershell.md)
