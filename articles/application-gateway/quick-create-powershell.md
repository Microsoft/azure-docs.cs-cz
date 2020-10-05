---
title: 'Rychlý Start: přímý webový provoz pomocí prostředí PowerShell'
titleSuffix: Azure Application Gateway
description: V tomto rychlém startu se naučíte, jak pomocí Azure PowerShell vytvořit službu Azure Application Gateway, která směruje webový provoz do virtuálních počítačů v back-endu fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3f64086ed97594416b5964cf648c857c2f271480
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91331093"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Rychlý Start: směrování webového provozu pomocí Azure Application Gateway pomocí Azure PowerShell

V tomto rychlém startu použijete Azure PowerShell k vytvoření aplikační brány. Pak ho otestujete, abyste se ujistili, že funguje správně. 

Aplikační brána směruje webový provoz aplikace do konkrétních prostředků ve fondu back-end. Posluchačům přiřadíte porty, vytvoříte pravidla a přidáte prostředky do back-endového fondu. V zájmu zjednodušení Tento článek používá jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v aplikační bráně, pravidlo základního směrování požadavku a dva virtuální počítače ve fondu back-end.

Tento rychlý Start můžete také dokončit pomocí [Azure CLI](quick-create-cli.md) nebo [Azure Portal](quick-create-portal.md).

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell verze 1.0.0 nebo novější](/powershell/azure/install-az-ps) (Pokud Azure PowerShell spustíte místně).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Připojení k Azure

Pokud se chcete připojit k Azure, spusťte `Connect-AzAccount` .

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.

Pokud chcete vytvořit novou skupinu prostředků, použijte `New-AzResourceGroup` rutinu: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.  Můžete buď vytvořit novou podsíť pro Application Gateway nebo použít stávající. V tomto příkladu vytvoříte v tomto příkladu dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery. IP adresu front-endu Application Gateway můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.

1. Vytvořte konfigurace podsítě pomocí `New-AzVirtualNetworkSubnetConfig` .
2. Vytvořte virtuální síť s konfiguracemi podsítí pomocí `New-AzVirtualNetwork` . 
3. Vytvořte veřejnou IP adresu pomocí `New-AzPublicIpAddress` . 
> [!NOTE]
> [Zásady koncového bodu služby virtuální sítě](../virtual-network/virtual-network-service-endpoint-policies-overview.md) se v Application Gateway podsíti v tuto chvíli nepodporují.

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
## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

1. Použijte `New-AzApplicationGatewayIPConfiguration` k vytvoření konfigurace, která přidruží podsíť, kterou jste vytvořili s aplikační bránou. 
2. Použijte `New-AzApplicationGatewayFrontendIPConfig` k vytvoření konfigurace, která přiřadí veřejnou IP adresu, kterou jste předtím vytvořili ve službě Application Gateway. 
3. Použijte `New-AzApplicationGatewayFrontendPort` k přiřazení portu 80 pro přístup k aplikační bráně.

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

1. Slouží `New-AzApplicationGatewayBackendAddressPool` k vytvoření back-end fondu pro aplikační bránu. Back-end fond bude nyní prázdný. Při vytváření síťových karet serveru back-end v následující části je přidáte do fondu back-end.
2. Nakonfigurujte nastavení pro fond back-end pomocí `New-AzApplicationGatewayBackendHttpSetting` .

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Vytvoření naslouchacího procesu a přidání pravidla

Azure vyžaduje naslouchací proces, aby mohla služba Application Gateway povolit směrování provozu odpovídajícím způsobem do back-endového fondu. Azure také vyžaduje pravidlo pro naslouchací proces, aby věděli, který back-end fond chcete použít pro příchozí provoz. 

1. Vytvořte naslouchací proces pomocí `New-AzApplicationGatewayHttpListener` s konfigurací front-end a portem front-endu, který jste vytvořili dříve. 
2. Použijte `New-AzApplicationGatewayRequestRoutingRule` k vytvoření pravidla s názvem *rule1*. 

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

1. Slouží `New-AzApplicationGatewaySku` k zadání parametrů pro aplikační bránu.
2. Použijte `New-AzApplicationGateway` k vytvoření aplikační brány.

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

### <a name="backend-servers"></a>Back-endové servery

Teď, když jste vytvořili Application Gateway, vytvořte back-end virtuální počítače, které budou hostovat weby. Back-end se může skládat z síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače pro Azure, které budou používat jako servery back-end pro službu Application Gateway. Nainstalujete také službu IIS na virtuální počítače a ověříte tak, že Azure úspěšně vytvořil Aplikační bránu.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

1. Získejte nedávno vytvořenou konfiguraci Application Gatewayho fondu back-endu `Get-AzApplicationGatewayBackendAddressPool` .
2. Vytvořte síťové rozhraní pomocí `New-AzNetworkInterface` .
3. Vytvořte konfiguraci virtuálního počítače pomocí `New-AzVMConfig` .
4. Vytvořte virtuální počítač pomocí `New-AzVM` .

Když spustíte následující ukázku kódu pro vytvoření virtuálních počítačů, Azure vás vyzve k zadání přihlašovacích údajů. Jako uživatelské jméno a heslo zadejte *azureuser* :
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
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

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když služba IIS není nutná k vytvoření aplikační brány, nainstalovali jste se v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Použijte službu IIS k otestování služby Application Gateway:

1. Spuštěním `Get-AzPublicIPAddress` Získejte veřejnou IP adresu aplikační brány. 
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče. Když aktualizujete prohlížeč, měl by se zobrazit název virtuálního počítače. Platná odpověď ověří, že se služba Application Gateway úspěšně vytvořila, a může se úspěšně připojit k back-endu.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Otestování aplikační brány](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odstraňte skupinu prostředků. Když odstraníte skupinu prostředků, odstraníte také aplikační bránu a všechny související prostředky. 

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu vytvořením aplikační brány v Azure PowerShellu](./tutorial-manage-web-traffic-powershell.md)

