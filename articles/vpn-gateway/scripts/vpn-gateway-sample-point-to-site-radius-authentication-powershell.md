---
title: Ukázka skriptu Azure PowerShell – konfigurace sítě VPN P2S s ověřováním pomocí protokolu RADIUS
titleSuffix: Azure VPN Gateway
description: Ukázka prostředí PowerShell pro konfiguraci sítě VPN typu Point-to-site s ověřováním uživatelského jména a hesla protokolu RADIUS
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: sample
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 0a89ce2b73ca9da64f86c81ea00b1f46ea18f996
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385808"
---
# <a name="create-a-vpn-gateway-with-p2s-radius-authentication---powershell-script-sample"></a>Vytvoření brány VPN pomocí ověřování RADIUS P2S – ukázka skriptu PowerShellu

Tento ukázkový skript vytvoří bránu sítě VPN založenou na směrování a přidá konfiguraci Point-to-site s použitím ověřování pomocí uživatelského jména a hesla protokolu RADIUS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $VNetPrefix1 = "10.1.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $RSAddress = "10.51.0.15"

# Create a resource group
New-AzResourceGroup -Name $RG -Location $Location
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName $RG `
  -Location $Location `
  -Name $VNetName `
  -AddressPrefix $VNetPrefix1
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name $FESubName `
  -AddressPrefix $FESubPrefix `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName $RG -Name $VNetName
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Create a secure string for the RADIUS secret
$Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"

# Add the VPN client address pool and the RADIUS server information
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
 -VpnClientAddressPool $VPNClientAddressPool -VpnClientProtocol @( "SSTP", "IkeV2" ) `
 -RadiusServerAddress $RSAddress -RadiusServerSecret $Secure_Secret
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Získá podrobnosti virtuální sítě. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Načte podrobnosti brány virtuální sítě. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podrobnosti o konfiguraci podsítě virtuální sítě. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří novou konfiguraci protokolu IP brány. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Vytvoří bránu sítě VPN. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Nastaví konfiguraci podsítě pro virtuální síť. |
| [Set-AzVirtualNetworkGateway](/powershell/module/az.network/set-azvirtualnetworkgateway) | Nastaví konfiguraci brány sítě VPN. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).
