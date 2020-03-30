---
title: 'Úvodní příručka: Přímý webový provoz pomocí PowerShellu'
titleSuffix: Azure Application Gateway
description: Zjistěte, jak pomocí Azure PowerShellu vytvořit aplikační bránu Azure, která směruje webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: abb38dfc342c8ff692ed1a3a05376b5dcefe8a3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399568"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Úvodní příručka: Přímý webový provoz s Aplikační bránou Azure pomocí Azure PowerShellu

V tomto rychlém startu použijete Azure PowerShell k vytvoření aplikační brány. Pak jej otestujete, abyste se ujistili, že funguje správně. 

Aplikační brána směruje webový provoz aplikací na konkrétní prostředky v back-endovém fondu. Naslouchací procesy přiřazujete k portům, vytváříte pravidla a přidáváte prostředky do back-endového fondu. Z důvodu jednoduchosti tento článek používá jednoduché nastavení s veřejnou front-endovou IP adresou, základní naslouchací proces pro hostování jedné sítě na aplikační bráně, základní pravidlo směrování požadavků a dva virtuální počítače v back-endovém fondu.

Tento rychlý start můžete také dokončit pomocí [azure cli](quick-create-cli.md) nebo [portálu Azure](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell verze 1.0.0 nebo novější](/powershell/azure/install-az-ps) (pokud spustíte Azure PowerShell místně).

## <a name="connect-to-azure"></a>Připojení k Azure

Chcete-li se `Connect-AzAccount`připojit k Azure, spusťte .

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělujete související prostředky skupině prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou.

Chcete-li vytvořit novou skupinu prostředků, použijte rutinu: `New-AzResourceGroup` 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť.  Podsíť aplikační brány může obsahovat pouze aplikační brány. Nejsou povoleny žádné další prostředky.  Můžete buď vytvořit novou podsíť pro aplikační bránu, nebo použít existující. V tomto příkladu vytvoříte dvě podsítě v tomto příkladu: jednu pro aplikační bránu a druhou pro servery back-endu. Front-endovou IP adresu aplikační brány můžete nakonfigurovat jako veřejnou nebo soukromou podle případu použití. V tomto příkladu zvolíte veřejnou ip adresu front-endu.

1. Vytvořte konfigurace podsítě `New-AzVirtualNetworkSubnetConfig`pomocí aplikace .
2. Vytvořte virtuální síť s konfiguracemi `New-AzVirtualNetwork`podsítě pomocí aplikace . 
3. Vytvořte veřejnou `New-AzPublicIpAddress`IP adresu pomocí aplikace . 

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
## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurací IP adres a front-endového portu

1. Slouží `New-AzApplicationGatewayIPConfiguration` k vytvoření konfigurace, která přidruží podsíť, kterou jste vytvořili, k aplikační bráně. 
2. Slouží `New-AzApplicationGatewayFrontendIPConfig` k vytvoření konfigurace, která přiřadí veřejnou IP adresu, kterou jste dříve vytvořili, k aplikační bráně. 
3. Slouží `New-AzApplicationGatewayFrontendPort` k přiřazení portu 80 pro přístup k aplikační bráně.

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

1. Slouží `New-AzApplicationGatewayBackendAddressPool` k vytvoření back-endového fondu pro aplikační bránu. Fond back-endu bude prozatím prázdný a při vytváření nicotných aplikací back-endového serveru v další části je přidáte do back-endového fondu.
2. Nakonfigurujte nastavení back-endového fondu pomocí aplikace `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
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

Azure vyžaduje naslouchací proces povolit aplikační brány pro směrování provozu vhodně do back-endového fondu. Azure také vyžaduje pravidlo pro naslouchací proces vědět, který back-end ový fond použít pro příchozí provoz. 

1. Vytvořte naslouchací proces pomocí `New-AzApplicationGatewayHttpListener` konfigurace front-endu a portu front-endu, který jste dříve vytvořili. 
2. Slouží `New-AzApplicationGatewayRequestRoutingRule` k vytvoření pravidla s názvem *rule1*. 

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

Teď, když jste vytvořili potřebné podpůrné prostředky, vytvořte aplikační bránu:

1. Slouží `New-AzApplicationGatewaySku` k určení parametrů pro aplikační bránu.
2. Slouží `New-AzApplicationGateway` k vytvoření aplikační brány.

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

Teď, když jste vytvořili aplikační bránu, vytvořte back-endové virtuální počítače, které budou hostovat weby. Back-end se může skládat z připojení k síťové karty, škálovacísady virtuálních počítačů, veřejných IP služeb, interních IP služeb, plně kvalifikovaných názvů domén (FQDN) a víceklientských back-endů, jako je Azure App Service. V tomto příkladu vytvoříte dva virtuální počítače pro Azure, které se mají používat jako back-endové servery pro aplikační bránu. Službu IIS také nainstalujete do virtuálních počítačů a ověříte, že Azure úspěšně vytvořil aplikační bránu.

#### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

1. Získejte nedávno vytvořenou konfiguraci `Get-AzApplicationGatewayBackendAddressPool`back-endového fondu aplikační brány s aplikací .
2. Vytvořte síťové `New-AzNetworkInterface`rozhraní s programem .
3. Vytvořte konfiguraci `New-AzVMConfig`virtuálního počítače pomocí aplikace .
4. Vytvořte virtuální `New-AzVM`počítač s .

Když spustíte následující ukázku kódu k vytvoření virtuálních počítačů, Azure vás vyzve k zadání přihlašovacích údajů. Zadejte *azureuser* pro uživatelské jméno a heslo:
    
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

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

I když služba IIS není nutné k vytvoření aplikační brány, nainstalovali jste ji v tomto rychlém startu k ověření, zda Azure úspěšně vytvořil aplikační bránu. Pomocí služby IIS otestujte bránu aplikace:

1. Spuštěním `Get-AzPublicIPAddress` získáte veřejnou IP adresu aplikační brány. 
2. Zkopírujte a vložte veřejnou IP adresu do adresního řádku prohlížeče. Při aktualizaci prohlížeče, měli byste vidět název virtuálního počítače. Platná odpověď ověří, zda byla aplikační brána úspěšně vytvořena a může se úspěšně připojit k back-endu.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Otestování aplikační brány](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odstraňte skupinu prostředků. Když odstraníte skupinu prostředků, odstraníte také bránu aplikace a všechny související prostředky. 

Chcete-li odstranit skupinu `Remove-AzResourceGroup` prostředků, zavolejte rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu vytvořením aplikační brány v Azure PowerShellu](./tutorial-manage-web-traffic-powershell.md)

