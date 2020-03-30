---
title: Konfigurace partnerských zařízení VPN pro připojení k bráně Azure VPN
description: Tento článek obsahuje přehled konfigurací partnerských zařízení VPN pro připojení k bránám Azure VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279400"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Přehled konfigurací partnerských zařízení VPN
Tento článek obsahuje přehled konfigurace místních zařízení VPN pro připojení k bránám Azure VPN. Ukázkové nastavení virtuální sítě Azure a brány VPN se používá k zobrazení, jak se připojit k různým místním konfiguracím zařízení VPN pomocí stejných parametrů.



## <a name="device-requirements"></a>Požadavky na zařízení
Brány Azure VPN používají standardní sady protokolů IPsec/IKE pro tunely VPN typu site-to-site (S2S). Seznam parametrů IPsec/IKE a kryptografické algoritmy pro brány Azure VPN najdete [v tématu O zařízeních VPN](vpn-gateway-about-vpn-devices.md). Můžete také určit přesné algoritmy a silné stránky klíče pro konkrétní připojení, jak je popsáno v [části O kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Jeden tunel VPN
První konfigurace v ukázce se skládá z jednoho tunelu S2S VPN mezi bránou Azure VPN a místním zařízením VPN. Volitelně můžete nakonfigurovat [protokol BGP (Border Gateway Protocol) v celém tunelu VPN](#bgp).

![Diagram jednoho tunelu VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Podrobné pokyny k nastavení jednoho tunelového propojení VPN naleznete v [tématu Konfigurace připojení k webu k webu](vpn-gateway-howto-site-to-site-resource-manager-portal.md). V následujících částech jsou určeny parametry připojení pro ukázkovou konfiguraci a poskytují skript prostředí PowerShell, který vám pomůže začít.

### <a name="connection-parameters"></a>Parametry připojení
V této části jsou uvedeny parametry pro příklady, které jsou popsány v předchozích částech.

| **Parametr**                | **Hodnotu**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP adresa brány Azure VPN         | IP brána Azure VPN         |
| Předpony místní adresy | 10.51.0.0/16<br>10.52.0.0/16 |
| Adresa IP místního zařízení VPN    | Adresa IP místního zařízení VPN    |
| * Virtuální síť BGP ASN                | 65010                        |
| * Ip partnerské strany Azure BGP           | 10.12.255.30                 |
| * Místní protokol BGP ASN         | 65050                        |
| * Místní IP adresa partnera Protokolu BGP     | 10.52.255.254                |

\*Volitelný parametr pouze pro protokol BGP.

### <a name="sample-powershell-script"></a>Ukázkový skript prostředí PowerShell
Tato část obsahuje ukázkový skript, který vám pomůže začít. Podrobné pokyny najdete [v tématu Vytvoření připojení VPN S2S pomocí prostředí PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

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

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Nepovinné) Použití vlastních zásad IPsec/IKE s usePolicyBasedTrafficSelectors
Pokud vaše zařízení VPN nepodporují žádné voliče provozu, jako jsou konfigurace založené na trasách nebo VTI, vytvořte vlastní zásady IPsec/IKE pomocí možnosti [UsePolicyBasedTrafficSelectors.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

> [!IMPORTANT]
> Musíte vytvořit zásadu IPsec/IKE, která povolí možnost **UsePolicyBasedTrafficSelectors** v připojení.


Ukázkový skript vytvoří zásadu Protokolu IPsec/IKE s následujícími algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Životnost 7,200 sekund a 20,480,000 KB (20 GB)

Skript použije zásadu IPsec/IKE a povolí možnost **UsePolicyBasedTrafficSelectors** pro připojení.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Nepovinné) Použití protokolu BGP při připojení S2S VPN
Při vytváření připojení S2S VPN můžete volitelně použít [protokol BGP pro bránu VPN](vpn-gateway-bgp-resource-manager-ps.md). Tento přístup má dva rozdíly:

* Předpony místní adresy mohou být jedna adresa hostitele. Místní IP adresa partnerského protokolu BGP je určena takto:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Při vytváření připojení je nutné nastavit možnost **-EnableBGP** tak, aby $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k nastavení aktivních bran VPN s aktivní mincovní, najdete [v tématu Konfigurace aktivních a aktivních bran VPN pro připojení mezi místními sítěmi a připojení virtuální sítě k virtuální síti](vpn-gateway-activeactive-rm-powershell.md).

