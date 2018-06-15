---
title: Partner konfigurace zařízení VPN pro připojení k Azure VPN Gateway | Microsoft Docs
description: Tento článek obsahuje přehled partnera konfigurace zařízení VPN pro připojení k Azure VPN Gateway.
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
ms.openlocfilehash: dd9ca3937d688170798c42fce45dbcd7711773d1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599607"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Přehled konfigurace zařízení VPN partnera
Tento článek obsahuje přehled o konfiguraci místní zařízení VPN pro připojení k Azure VPN Gateway. A ukázkové virtuální síť Azure a nastavení brány VPN se používá k ukazují, jak se připojit k jiné místní konfigurace zařízení VPN pomocí stejné parametry.

## <a name="device-requirements"></a>Požadavky na zařízení
Azure VPN Gateway používat standardní sady protokolu IPsec/IKE pro site-to-site (S2S) VPN tunelových propojení. Seznam parametrů protokolu IPsec/IKE a kryptografické algoritmy pro Azure VPN Gateway najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Můžete také zadáte přesný algoritmy a klíče síly pro konkrétní připojení jak je popsáno v [o požadavcích na kryptografických](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Jedno tunelové propojení sítě VPN
První konfigurace v ukázce se skládá z jedné tunel S2S VPN mezi Azure VPN gateway a místní zařízení VPN. Volitelně můžete nakonfigurovat [protokol BGP (Border Gateway) přes tunelové propojení VPN](#bgp).

![Diagram jedno tunelové propojení S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Podrobné pokyny k nastavení jedno tunelové propojení VPN najdete v tématu [konfigurace připojení typu site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Následující části zadejte parametry připojení pro ukázkové konfiguraci a zadejte skript prostředí PowerShell, který vám pomůže začít.

### <a name="connection-parameters"></a>Parametry připojení
V této části jsou uvedeny parametry příklady, které jsou popsané v předchozích částech.

| **Parametr**                | **Hodnota**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure IP adresa brány sítě VPN         | Brána Azure VPN IP         |
| Předpony adres místní | 10.51.0.0/16<br>10.52.0.0/16 |
| IP adresa zařízení místní sítě VPN    | IP adresa zařízení místní sítě VPN    |
| * Virtuální sítě ASN protokolu BGP                | 65010                        |
| * Azure IP adresa partnera BGP           | 10.12.255.30                 |
| * Místní ASN protokolu BGP         | 65050                        |
| * IP adresa partnera BGP na místě     | 10.52.255.254                |

\* Volitelný parametr pro protokol BGP jenom.

### <a name="sample-powershell-script"></a>Ukázkový skript prostředí PowerShell
Tato část obsahuje ukázkový skript, které vám pomůžou začít. Podrobné pokyny najdete v tématu [vytvořit připojení S2S VPN pomocí prostředí PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Volitelné) Pomocí vlastních zásad protokolu IPsec/IKE UsePolicyBasedTrafficSelectors
Pokud vaše zařízení VPN nepodporují selektorů any-to-any provoz, jako je například konfigurace založené na směrování nebo na základě VTI vytvořit vlastní zásady protokolu IPsec/IKE s [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) možnost.

> [!IMPORTANT]
> Musíte vytvořit zásady protokolu IPsec/IKE pro povolení **UsePolicyBasedTrafficSelectors** možnost na připojení.


Ukázkový skript vytvoří zásadu protokolu IPsec/IKE se tyto algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* Protokol IPsec: AES256, SHA1, PFS24, 7,200 životnost přidružení zabezpečení sekund a 20,480,000 KB (20 GB)

Skript se vztahují zásady protokolu IPsec/IKE a umožňuje **UsePolicyBasedTrafficSelectors** možnost na připojení.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Volitelné) Použít protokol BGP na připojení S2S VPN
Když vytvoříte připojení S2S VPN, můžete volitelně použít [protokolu BGP pro bránu VPN](vpn-gateway-bgp-resource-manager-ps.md). Tento přístup má dvě rozdíly:

* Předpony adres místní může být adresa jednoho hostitele. IP adresy partnerského uzlu protokolu BGP místní je zadán následujícím způsobem:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Při vytváření připojení, musíte nastavit **- EnableBGP** možnost $True:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Další postup
Podrobné pokyny k nastavení aktivní aktivní brány VPN najdete v tématu [konfiguraci aktivní aktivní brány sítě VPN pro mezi různými místy a připojení VNet-to-VNet](vpn-gateway-activeactive-rm-powershell.md).

