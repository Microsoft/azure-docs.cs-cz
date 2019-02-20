---
title: Partner konfigurací zařízení VPN pro připojení k Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek obsahuje přehled konfigurací zařízení VPN partner pro připojení k Azure VPN Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 07f2e46198118530de5a2163480eb44575891c4b
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415259"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Přehled konfigurací zařízení VPN partnera
Tento článek obsahuje základní informace o konfiguraci místní zařízení VPN pro připojení k Azure VPN Gateway. A ukázkový virtuální síť Azure a nastavení brány sítě VPN se používá k ukazují, jak se připojit s použitím stejné parametry do různých místních konfigurací zařízení VPN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="device-requirements"></a>Požadavky na zařízení
Azure VPN Gateway pomocí standardní sady protokolu IPsec/IKE pro site-to-site (S2S) VPN tunely. Seznam parametrů protokolu IPsec/IKE a kryptografické algoritmy pro služby Azure VPN Gateway najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Můžete také zadáte přesný algoritmy a síly klíče pro konkrétní připojení jak je popsáno v [informace o kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Jedno tunelové propojení sítě VPN
První konfigurace v ukázce se skládá z jednoho tunelu S2S VPN mezi službou Azure VPN gateway a místní zařízení VPN. Volitelně můžete nakonfigurovat [protokol BGP (Border Gateway) přes tunelové propojení VPN](#bgp).

![Diagram jeden tunel S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Podrobné pokyny k nastavení jeden tunel VPN najdete v tématu [konfigurace připojení typu site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Následující části zadejte parametry připojení pro ukázkové konfiguraci a zadejte skript prostředí PowerShell, který vám pomůžou začít.

### <a name="connection-parameters"></a>Parametry připojení
Tato část obsahuje seznam parametrů pro příklady, které jsou popsané v předchozích částech.

| **Parametr**                | **Hodnota**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway IP         | Azure VPN Gateway IP         |
| Předpony místních adres | 10.51.0.0/16<br>10.52.0.0/16 |
| Místní IP adresa zařízení sítě VPN    | Místní IP adresa zařízení sítě VPN    |
| * Virtuální sítě BGP číslo ASN                | 65010                        |
| * Azure místní adresu partnera BGP           | 10.12.255.30                 |
| * Místní ASN protokolu BGP         | 65050                        |
| * Místní adresu partnera BGP on-premises     | 10.52.255.254                |

\* Volitelný parametr BGP pouze.

### <a name="sample-powershell-script"></a>Ukázkový skript Powershellu
Tato část obsahuje ukázkový skript, které vám pomůžou začít. Podrobné pokyny najdete v tématu [vytvořte připojení S2S VPN s použitím prostředí PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Volitelné) Použití vlastních zásad IPsec/IKE s UsePolicyBasedTrafficSelectors
Pokud vaše zařízení VPN nepodporují selektory provozu na any-to-any, jako jsou založené na směrování nebo na základě VTI konfigurace, vytvoření vlastní zásady IPsec/IKE s [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) možnost.

> [!IMPORTANT]
> Musíte vytvořit zásady IPsec/IKE Povolit **UsePolicyBasedTrafficSelectors** možnost připojení.


Ukázkový skript vytvoří zásady IPsec/IKE s následující algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* Protokol IPsec: AES256, SHA1, PFS24, až 7 200 doba života přidružení zabezpečení sekund a 20,480,000 KB (20 GB)

Skript platí zásady IPsec/IKE a umožňuje **UsePolicyBasedTrafficSelectors** možnost připojení.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Volitelné) Použít protokol BGP pro připojení S2S VPN
Když vytvoříte připojení S2S VPN, můžete volitelně použít [protokol BGP pro bránu VPN](vpn-gateway-bgp-resource-manager-ps.md). Tento přístup má dva rozdíly:

* Předpony místních adres může být adresa jednoho hostitele. IP adresa partnerského uzlu protokolu BGP s místními určena následujícím způsobem:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Když vytvoříte připojení, je nutné nastavit **- EnableBGP** možnost $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Další postup
Podrobné pokyny k nastavení bran VPN typu aktivní aktivní najdete v tématu [Konfigurace bran VPN typu aktivní aktivní pro připojení VNet-to-VNet mezi různými místy a](vpn-gateway-activeactive-rm-powershell.md).

