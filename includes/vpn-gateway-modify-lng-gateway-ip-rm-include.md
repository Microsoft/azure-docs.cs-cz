---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ee182202cf1ecbbb0845541269f7241de26c170
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326502"
---
### <a name="gwipnoconnection"></a>Úprava IP adresy brány místní sítě (GatewayIpAddress) – žádné připojení brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Pomocí tohoto příkladu upravte bránu místní sítě, která nemá připojení brány.

Při upravování této hodnoty můžete také zároveň upravit předpony adres. Ujistěte se, že k přepsání aktuálního nastavení používáte existující název brány místní sítě. Pokud použijete jiný název, vytvoříte novou bránu místní sítě, místo abyste přepsali tu stávající.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Úprava IP adresy brány místní sítě (GatewayIpAddress) – existující připojení brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Pokud už nějaké připojení brány existuje, musíte ho nejdřív odebrat. Po odebrání připojení můžete upravit IP adresu brány a vytvořit nové připojení. Můžete také zároveň upravit předpony adresy. Způsobí to určitý výpadek připojení VPN. Při upravování IP adresy brány není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.
 

1. Odeberte připojení. Název vašeho připojení můžete najít pomocí rutiny Get-AzureRmVirtualNetworkGatewayConnection.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1
  ```
2. Upravte hodnotu GatewayIpAddress. Můžete také zároveň upravit předpony adresy. Ujistěte se, že k přepsání aktuálního nastavení používáte existující název brány místní sítě. Pokud to neuděláte, vytvoříte novou bránu místní sítě, místo abyste přepsali tu stávající.

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 `
  -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
  ```
3. Vytvořte připojení. V tomto příkladu konfigurujeme typ připojení IPsec. Až budete připojení znovu vytvářet, použijte typ připojení stanovený pro vaši konfiguraci. Informace o dalších typech připojení najdete na stránce [Rutina prostředí PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).  Pokud chcete získat název brány VirtualNetworkGateway, můžete spustit rutinu Get-AzureRmVirtualNetworkGateway.
   
    Nastavte proměnné.

  ```azurepowershell-interactive
  $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  ```
   
    Vytvořte připojení.

  ```azurepowershell-interactive 
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
  -Location "East US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```