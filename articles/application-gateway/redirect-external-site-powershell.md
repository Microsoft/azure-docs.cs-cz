---
title: Vytvoření služby application gateway s externí přesměrování - prostředí Azure PowerShell | Microsoft Docs
description: Postup vytvoření služby application gateway, který přesměruje webových přenosů na externí web pomocí Azure Powershell.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 6ae672ba20ed6314f7baf49fee3310cecf7837ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Vytvoření služby application gateway s externí přesměrování pomocí Azure PowerShell

Prostředí Azure Powershell můžete použít ke konfiguraci [webové přenosy přesměrování](multiple-site-overview.md) při vytváření [Aplikační brána](overview.md). V tomto kurzu nakonfigurujete naslouchací proces a pravidel, který přesměruje webových přenosů, které dorazí na službu application gateway na externí web.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření pravidla, naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 3.6 nebo novější. Chcete-li najít verzi, spusťte ` Get-Module -ListAvailable AzureRM` . Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořit skupinu prostředků Azure pomocí [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Vytvoření konfigurace podsítě *myAGSubnet* pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Vytvořit virtuální síť s názvem *myVNet* pomocí [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) s konfigurací podsítě. A nakonec vytvořte na veřejnou IP adresu pomocí [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Tyto prostředky se používají k poskytování síťové připojení k službě application gateway a její přidružené prostředky.

```azurepowershell-interactive
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Vytvoření konfigurace protokolu IP a port front-endu

Přidružení *myAGSubnet* aplikace brány pomocí dříve vytvořeného [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Přiřadit veřejnou IP adresu brány aplikace pomocí [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). A pak můžete vytvořit pomocí portu HTTP [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
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

### <a name="create-the-backend-pool-and-settings"></a>Vytvoření fondu back-end a nastavení

Vytvořit fond back-end s názvem *defaultPool* pro aplikace pomocí brány [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Konfiguruje nastavení pro použití fondu [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>Vytvořte naslouchací proces a pravidla

K povolení aplikační brány, aby správně směrování provozu je potřeba naslouchací proces. Vytvořte naslouchací proces pomocí [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) s front-endovou konfiguraci a front-endový port, který jste dříve vytvořili. Pravidlo je vyžadována pro naslouchací proces vědět, kam má posílat příchozí přenosy. Vytvořte základní pravidlo s názvem *redirectRule* pomocí [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultListener = New-AzureRmApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzureRmApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "http://bing.com"
$redirectRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Teď, když jste vytvořili nezbytné doprovodné materiály, zadejte parametry pro službu application gateway s názvem *myAppGateway* pomocí [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku)a pak vytvořit pomocí [ Nový AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Můžete použít [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) získat veřejnou IP adresu aplikační brány. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

Měli byste vidět *vyhledávače bing.com* zobrazí v prohlížeči.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření pravidla, naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway