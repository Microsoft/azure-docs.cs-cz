---
title: 'Ukázkový skript Azure PowerShellu: Konfigurace sítě VPN typu Point-to-Site s ověřováním pomocí nativního certifikátu Azure | Microsoft Docs'
description: Konfigurace sítě VPN typu Point-to-Site s ověřováním nativním certifikátem Azure pomocí certifikátů podepsaných držitelem Tento článek používá PowerShell.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: a20a71ad527ee4f6e0a52a6c59881af0aa7fded2
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415659"
---
# <a name="create-a-vpn-gateway-and-add-point-to-site-configuration-using-powershell"></a>Vytvoření brány sítě VPN a přidání konfigurace typu Point-to-Site pomocí PowerShellu

Tento skript vytvoří bránu sítě VPN založenou na směrování a přidá konfiguraci typu Point-to-Site pomocí ověřování prostřednictvím nativního certifikátu Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "10.0.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $BESubPrefix = "10.1.1.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $RG = "TestRG1"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"
  $GWIPconfName = "gwipconf"
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
 -Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Add the VPN client address pool
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
# Create a self-signed root certificate
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
 -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
 -HashAlgorithm sha256 -KeyLength 2048 `
 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
# Export the root certificate to "C:\cert\P2SRootCert.cer"
# Upload the root certificate public key information
$P2SRootCertName = "P2SRootCert.cer"
$filePathForCert = "C:\cert\P2SRootCert.cer"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
$p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName `
 -VirtualNetworkGatewayname "VNet1GW" `
 -ResourceGroupName "TestRG1" -PublicCertData $CertBase64

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili, použijte [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odstranění skupiny prostředků. Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Přidá konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [Add-AzVpnClientRootCertificate](/powershell/module/az.network/add-azvpnclientrootcertificate) | Nahraje informace o veřejném klíči kořenového certifikátu do brány sítě VPN.|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Načte podrobnosti virtuální sítě. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Načte podrobnosti brány virtuální sítě. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Načte podrobnosti o konfiguraci podsítě virtuální sítě. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. Tato konfigurace se použije v procesu vytváření virtuální sítě. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Vytvoří novou konfiguraci protokolu IP brány. |
| [New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway?view=azurermps-6.8.1) | Vytvoří bránu sítě VPN. |
| [New-SelfSignedCertificate]https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps) | Vytvoří nový kořenový certifikát podepsaný svým držitelem. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Nastaví konfiguraci podsítě pro virtuální síť. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).
