---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure PowerShell | Microsoft Docs
description: Přečtěte si o tom, jak pomocí Azure PowerShellu vytvořit aplikační bránu Azure Application Gateway, která bude směrovat webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d41480def95137e1dc938c845f8cec1d59e26036
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521780"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Rychlý start: Přímé webového provozu s využitím Azure Application Gateway – Azure PowerShell

V tomto rychlém startu se dozvíte, jak rychle vytvořit službu application gateway pomocí Azure Powershellu.  Po vytvoření aplikační brány se potom otestovat a ujistit se, že funguje správně. Pomocí služby Azure Application Gateway řízení provozu webové aplikace ke konkrétním prostředkům prostřednictvím přiřazení naslouchacích procesů k portům, vytváření pravidel a přidávání prostředků do back-endový fond. Z důvodu zjednodušení tento článek používá jednoduché uspořádání s veřejnou IP adresu front-endu, základní naslouchací proces pro hostování jedné lokalitě v této brány application gateway, dva virtuální počítače pro back-endového fondu a pravidel směrování základní požadavek.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-powershell-module"></a>Modul Azure PowerShellu

Pokud se rozhodnete nainstalovat a používat prostředí Azure PowerShell místně, v tomto kurzu potřebovat modul Azure PowerShell verze 1.0.0 nebo novějším.

1. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). 
2. Chcete-li vytvořit připojení k Azure, spusťte `Login-AzAccount`.

### <a name="resource-group"></a>Skupina prostředků

V Azure přidělení související prostředky do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte novou. V tomto příkladu vytvoříme novou skupinu prostředků s použitím [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) rutiny následujícím způsobem: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Požadovaným síťovým zdrojům

U Azure ke komunikaci mezi prostředky, že vytvoříte potřebuje virtuální sítě.  Podsítě služby application gateway může obsahovat jenom aplikační brány. Jsou povoleny žádné další prostředky.  Můžete vytvořit novou podsíť pro aplikační bránu, nebo použijte již existující. V tomto příkladu vytvoříte v tomto příkladu dvě podsítě: jednu pro application gateway a jinou pro back-end serverů. Můžete nakonfigurovat IP front-endu služby Application Gateway jako veřejné a privátní podle vašemu případu použití. V tomto příkladu vybereme možnost veřejná IP adresa front-endu.

1. Vytvořte Konfigurace podsítí voláním [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Vytvoření virtuální sítě s konfigurací podsítě voláním [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Vytvoření veřejné IP adresy pomocí volání [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Back-end serverů

Back-end může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače pro Azure a použít jako servery back-end pro službu application gateway. Také nainstalujte IIS na virtuální počítače k ověření úspěšného vytvoření aplikační brány Azure.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

1. Vytvořte síťové rozhraní s [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Vytvořte konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Vytvoření virtuálního počítače s [rutiny New-AzVM](/powershell/module/Az.compute/new-Azvm).

Při spuštění následující vzorový kód k vytvoření virtuálních počítačů Azure vás vyzve k zadání přihlašovacích údajů. Zadejte *azureuser* jako uživatelské jméno a *Azure123456!* pro heslo:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

1. Použití [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) pro vytvoření konfigurace, které přidružuje podsítě, který jste vytvořili ve službě application gateway. 
2. Použití [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) pro vytvoření konfigurace, které přiřadí veřejnou IP adresu, kterou jste dříve vytvořili ke službě application gateway. 
3. Použití [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) přiřadit port 80 pro přístup k službě application gateway.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Vytvoření back-endového fondu

1. Použití [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) k vytvoření fondu back-endu pro službu application gateway. 
2. Nakonfigurujte nastavení pro back-endový fond s [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Vytvoření naslouchacího procesu a přidání pravidla

Azure vyžaduje naslouchací proces umožňuje aplikační brány pro směrování provozu odpovídajícím způsobem pro back-endový fond. Azure také vyžaduje pravidlo pro naslouchací proces vědět, kterému fondu back-endu pro příchozí provoz. 

1. Vytvořit naslouchací proces s použitím [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) s front-endová konfigurace a front-endový port, který jste předtím vytvořili. 
2. Použití [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) vytvořit pravidlo s názvem *pravidla 1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď, když jste vytvořili potřebné podpůrné prostředky, vytvoření služby application gateway:

1. Použití [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) k zadání parametrů pro službu application gateway.
2. Použití [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) k vytvoření služby application gateway.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když služba IIS není nutné k vytvoření služby application gateway, nainstalovaná v tomto rychlém startu k ověření úspěšného vytvoření aplikační brány Azure. Služba IIS použijte k otestování application gateway:

1. Spustit [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) k získání veřejné IP adresy služby application gateway. 
2. Zkopírujte a vložte veřejnou IP adresu do adresního řádku prohlížeče. Když aktualizujete prohlížeč, měli byste vidět název virtuálního počítače. Platné odpovědi ověří, jestli application gateway se úspěšně vytvořil, se může úspěšně připojit s back-endu.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Otestování aplikační brány](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili ve službě application gateway, odeberte skupinu prostředků. Odstraněním skupiny prostředků je taky odebrat application gateway a všechny související prostředky. 

Chcete-li odebrat skupinu prostředků, zavolejte [odebrat AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) rutiny následujícím způsobem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány v Azure PowerShellu](./tutorial-manage-web-traffic-powershell.md)

