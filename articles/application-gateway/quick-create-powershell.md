---
title: 'Rychlý Start: přímý webový provoz pomocí prostředí PowerShell'
titleSuffix: Azure Application Gateway
description: Naučte se, jak pomocí Azure PowerShell vytvořit Azure Application Gateway, který směruje webový provoz do virtuálních počítačů v back-endu fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d5b0ebc2d1b64dd4be677c38de30af7f7a954637
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075102"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Rychlý Start: směrování webového provozu pomocí Azure Application Gateway pomocí Azure PowerShell

V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShell rychle vytvořit Aplikační bránu.  Po vytvoření služby Application Gateway ji otestujete a ujistěte se, že funguje správně. V případě Azure Application Gateway nasměrujete webový provoz aplikace na konkrétní prostředky přiřazením posluchačů k portům, vytvořením pravidel a přidáním prostředků do back-endového fondu. V zájmu zjednodušení Tento článek používá jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v této aplikační bráně, dva virtuální počítače používané pro back-end fond a pravidlo základního směrování požadavků.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-powershell-module"></a>Modul Azure PowerShellu

Pokud se rozhodnete nainstalovat a používat Azure PowerShell v místním prostředí, vyžaduje tento kurz verzi modulu Azure PowerShell 1.0.0 nebo novější.

1. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). 
2. Pokud chcete vytvořit připojení k Azure, spusťte `Login-AzAccount`.

### <a name="resource-group"></a>Skupina prostředků

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou. V tomto příkladu vytvoříte novou skupinu prostředků pomocí rutiny [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) , jak je znázorněno níže: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Požadované síťové prostředky

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.  Můžete buď vytvořit novou podsíť pro Application Gateway nebo použít stávající. V tomto příkladu vytvoříte v tomto příkladu dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery. IP adresu front-endu Application Gateway můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.

1. Vytvořte konfiguraci podsítě voláním [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Vytvořte virtuální síť s konfiguracemi podsítě voláním [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Vytvořte veřejnou IP adresu voláním [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

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
  -AllocationMethod Static `
  -Sku Standard
```
### <a name="backend-servers"></a>Back-endové servery

Back-end se může skládat z síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače pro Azure, které budou používat jako servery back-end pro službu Application Gateway. Nainstalujete také službu IIS na virtuální počítače a ověříte tak, že Azure úspěšně vytvořil Aplikační bránu.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

1. Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Vytvořte konfiguraci virtuálního počítače pomocí [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Když spustíte následující ukázku kódu pro vytvoření virtuálních počítačů, Azure vás vyzve k zadání přihlašovacích údajů. Zadejte *azureuser* jako uživatelské jméno a *Azure123456!* pro heslo:
    
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
  Set-AzVMBootDiagnostic `
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

1. Pomocí [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) vytvořte konfiguraci, která přidruží podsíť, kterou jste vytvořili s aplikační bránou. 
2. Pomocí [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) vytvořte konfiguraci, která přiřadí veřejnou IP adresu, kterou jste předtím vytvořili ve službě Application Gateway. 
3. Pomocí [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) přiřaďte port 80 pro přístup k aplikační bráně.

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

1. Pomocí [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) vytvořte fond back-end pro aplikační bránu. 
2. Nakonfigurujte nastavení pro fond back-end pomocí [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Vytvoření naslouchacího procesu a přidání pravidla

Azure vyžaduje naslouchací proces, aby mohla služba Application Gateway povolit směrování provozu odpovídajícím způsobem do back-endového fondu. Azure také vyžaduje pravidlo pro naslouchací proces, aby věděli, který back-end fond chcete použít pro příchozí provoz. 

1. Vytvořte naslouchací proces pomocí [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) s dříve vytvořeným portem front-end a front-endu. 
2. Pomocí [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) vytvořte pravidlo s názvem *rule1*. 

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

Teď, když jste vytvořili potřebné podpůrné prostředky, vytvořte Aplikační bránu:

1. Pomocí [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) Určete parametry pro aplikační bránu.
2. Pomocí [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) vytvořte Aplikační bránu.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
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

I když služba IIS není nutná k vytvoření aplikační brány, nainstalovali jste se v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Použijte službu IIS k otestování služby Application Gateway:

1. Spuštěním rutiny [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) Získejte veřejnou IP adresu služby Application Gateway. 
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče. Když aktualizujete prohlížeč, měl by se zobrazit název virtuálního počítače. Platná odpověď ověří, že se služba Application Gateway úspěšně vytvořila, a může se úspěšně připojit k back-endu.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Otestování aplikační brány](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků. Odebráním skupiny prostředků odeberete také aplikační bránu a všechny související prostředky. 

Pokud chcete odebrat skupinu prostředků, zavolejte rutinu [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) následujícím způsobem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány v Azure PowerShellu](./tutorial-manage-web-traffic-powershell.md)

