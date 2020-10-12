---
title: Omezení webového provozu pomocí prostředí PowerShell
titleSuffix: Azure Web Application Firewall
description: Přečtěte si, jak omezit webový provoz pomocí brány firewall webových aplikací na aplikační bránu pomocí Azure PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/31/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 1e2fb98d83d1246c54ccb37d68d9b4282701677e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89225182"
---
# <a name="enable-web-application-firewall-using-azure-powershell"></a>Povolení firewallu webových aplikací pomocí Azure PowerShell

Můžete omezit provoz brány Application Gateway pomocí [brány firewall webových aplikací](ag-overview.md) (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací. 

V tomto článku získáte informace o těchto tématech:

* Nastavit síť
* Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
* Vytvoření škálovací sady virtuálních počítačů
* Vytvoření účtu úložiště a konfigurace diagnostiky

![Příklad firewallu webových aplikací](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Pokud budete chtít, můžete tento článek dokončit pomocí [Azure Portal](application-gateway-web-application-firewall-portal.md) nebo rozhraní příkazového [řádku Azure CLI](tutorial-restrict-web-traffic-cli.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Vytvořte konfigurace podsítě s názvem *myBackendSubnet* a *myAGSubnet* pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Vytvořte virtuální síť s názvem *myVNet* pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) s konfiguracemi podsítí. Nakonec vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress* pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Pomocí těchto prostředků se bude poskytovat síťové připojení k bráně Application Gateway a jejím přidruženým prostředkům.

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

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

V této části vytvoříte prostředky, které podporují aplikační bránu, a nakonec je vytvoříte a WAF. K vytvořeným prostředkům patří:

- *Konfigurace IP adres a front-endový port* – přidruží vytvořenou podsíť k aplikační bráně a přiřadí jí přístupový port.
- *Výchozí fond* – všechny aplikační brány musí mít aspoň jeden back-endový fond serverů.
- *Výchozí naslouchací proces a pravidlo* – výchozí naslouchací proces naslouchá provozu na přiřazeném portu a výchozí pravidlo odesílá provoz výchozímu fondu.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

Přidružte k aplikační bráně *myAGSubnet* , kterou jste dříve vytvořili, pomocí [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Přiřaďte *myAGPublicIPAddress* k aplikační bráně pomocí [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

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

### <a name="create-the-backend-pool-and-settings"></a>Vytvoření back-endového fondu a nastavení

Vytvořte back-end fond s názvem *appGatewayBackendPool* pro aplikační bránu pomocí [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Nakonfigurujte nastavení pro fondy back-end adres pomocí [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Vytvoření výchozího naslouchacího procesu a pravidla

Naslouchací proces je potřeba k tomu, aby brána Application Gateway mohla správně směrovat provoz na back-endové fondy adres. V tomto příkladu vytvoříte základní naslouchací proces, který naslouchá provozu na kořenové adrese URL. 

Pomocí [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) vytvořte naslouchací proces s názvem *mydefaultListener* s konfigurací front-endu a dříve vytvořeným portem front-endu. Aby naslouchací proces věděl, který back-endový fond se má použít pro příchozí provoz, potřebuje pravidlo. Vytvořte základní pravidlo s názvem *rule1* pomocí [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
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

### <a name="create-the-application-gateway-with-the-waf"></a>Vytvoření brány Application Gateway s WAF

Teď, když jste vytvořili potřebné podpůrné prostředky, zadejte parametry pro aplikační bránu pomocí [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku). Zadejte zásady brány firewall pomocí [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy). A pak vytvořte Aplikační bránu s názvem *myAppGateway* pomocí [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$policySetting = New-AzApplicationGatewayFirewallPolicySetting -Mode Prevention -State Enabled -MaxRequestBodySizeInKb 100 -MaxFileUploadInMb 256

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafpolicyNew -ResourceGroup $rgname -Location $location -PolicySetting $PolicySetting

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
  -Sku $sku `
  -FirewallPolicy $wafPolicy
```

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně bude poskytovat servery pro back-endový fond. Škálovací sadu přiřadíte back-endovému fondu při konfiguraci nastavení IP adres.

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

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

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
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Instalace služby IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfigurace diagnostiky

V tomto článku používá Aplikační brána účet úložiště k ukládání dat pro účely detekce a prevence. K zaznamenávání dat můžete použít také protokoly Azure Monitor nebo centra událostí.

### <a name="create-the-storage-account"></a>Vytvoření účtu úložiště

Vytvořte účet úložiště s názvem *myagstore1* pomocí [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Nakonfigurujte diagnostiku pro zaznamenávání dat do protokolů ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog pomocí [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy služby Application Gateway můžete použít [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) . Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testování základní adresy URL v aplikační bráně](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, aplikační bránu a všechny související prostředky pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

[Přizpůsobení pravidel firewallu webových aplikací](application-gateway-customize-waf-rules-portal.md)
