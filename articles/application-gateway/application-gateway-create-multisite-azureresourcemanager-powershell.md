---
title: Vytvoření aplikační brány s hostováním více lokalit – Azure PowerShell | Microsoft Docs
description: Naučte se, jak vytvořit Aplikační bránu, která hostuje víc lokalit pomocí Azure PowerShellu.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: e96579d52752f75b864c430ea0334a544606998c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835581"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Vytvoření aplikační brány s hostováním více lokalit pomocí Azure PowerShell

Prostředí Azure PowerShell můžete použít ke konfiguraci [hostování více webů](application-gateway-multi-site-overview.md) při vytváření [aplikační brány](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí sad škálování virtuálních počítačů. Pak na základě domén, které vám patří, nakonfigurujete naslouchací procesy a pravidla, aby se webový provoz přesměroval na příslušné servery ve fondech. V tomto kurzu se předpokládá, že vlastníte několik domén, a jako příklady se používají domény *www.contoso.com* a *www.fabrikam.com*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření Application Gateway
> * Vytvoření naslouchacího procesu a pravidel směrování
> * Vytvořit z back-endových fondů škálovací sadu virtuálních počítačů
> * Vytvořit záznam CNAME v doméně

![Příklad směrování na více webů](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz verzi modulu Azure PowerShell 1.0.0 nebo novější. Pokud chcete zjistit verzi, spusťte příkaz `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Nakonfigurujte podsítě s názvem *myBackendSubnet* a *myAGSubnet* pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Vytvořte virtuální síť s názvem *myVNet* pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) s konfiguracemi podsítí. Nakonec vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress* pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Pomocí těchto prostředků se bude poskytovat síťové připojení k bráně Application Gateway a jejím přidruženým prostředkům.

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

## <a name="create-an-application-gateway"></a>Vytvoření Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

Přidružte k aplikační bráně *myAGSubnet* , kterou jste dříve vytvořili, pomocí [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Přiřaďte *myAGPublicIPAddress* k aplikační bráně pomocí [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

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

Vytvořte back-endové fondy s názvem *contosoPool* a *fabrikamPool* pro aplikační bránu pomocí [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Nakonfigurujte nastavení pro fond pomocí [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

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

Naslouchací proces je nutný k tomu, aby služba Application Gateway mohla směrovat přenosy odpovídajícím způsobem do back-endového fondu. V tomto kurzu vytvoříte naslouchací procesy pro každou ze dvou domén. V tomto příkladu vytvoříte naslouchací procesy pro domény *www.contoso.com* a *www.fabrikam.com*.

Vytvořte naslouchací procesy s názvem *contosoListener* a *fabrikamListener* pomocí [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) s konfigurací front-end a portem front-endu, který jste vytvořili dříve. K tomu, aby naslouchací procesy věděli, který fond back-endu se má použít pro příchozí provoz, jsou nutná pravidla. Vytvořte základní pravidla s názvem *contosoRule* a *fabrikamRule* pomocí [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

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

Teď, když jste vytvořili potřebné podpůrné prostředky, určete parametry služby Application Gateway s názvem *myAppGateway* pomocí [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)a pak ji vytvořte pomocí [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

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
    -ImageReferenceVersion latest
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

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS služby Application Gateway můžete použít [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) . Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Do adresního řádku prohlížeče zadejte název domény. Příklad: https://www.contoso.com.

![Testování webu Contoso v aplikační bráně](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

Změňte adresu na jinou ze svých domén. Měli byste vidět něco podobného jako v následujícím příkladu:

![Testování webu Fabrikam v aplikační bráně](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření Application Gateway
> * Vytvoření naslouchacího procesu a pravidel směrování
> * Vytvořit z back-endových fondů škálovací sadu virtuálních počítačů
> * Vytvořit záznam CNAME v doméně

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](application-gateway-introduction.md)
