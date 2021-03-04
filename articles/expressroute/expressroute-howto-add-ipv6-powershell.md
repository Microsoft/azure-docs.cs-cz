---
title: 'Azure ExpressRoute: Přidání podpory protokolu IPv6 pomocí Azure PowerShell'
description: Naučte se, jak přidat podporu protokolu IPv6 pro připojení k nasazením Azure pomocí Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: 402714b55d7513e41458503b12c68768d0c6ad5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745000"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Přidání podpory protokolu IPv6 pro privátní partnerské vztahy pomocí Azure PowerShell (Preview)

Tento článek popisuje, jak přidat podporu protokolu IPv6 pro připojení prostřednictvím ExpressRoute k prostředkům v Azure pomocí Azure PowerShell.

> [!Note]
> Tato funkce je v tuto chvíli dostupná pro verzi Preview v [oblastech Azure s zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). Okruh ExpressRoute je proto možné vytvořit pomocí libovolného umístění partnerského vztahu, ale nasazení založená na protokolu IPv6, ke kterému se připojuje, musí být v oblasti s Zóny dostupnosti.

## <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Zaregistrujte se Public Preview
Před přidáním podpory protokolu IPv6 musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, udělejte to prosím pomocí Azure PowerShell:
1.  Přihlaste se k Azure a vyberte předplatné. Musíte to udělat u předplatného obsahujícího okruh ExpressRoute a předplatné, které obsahuje vaše nasazení Azure (Pokud se liší).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Žádost o registraci předplatného pro Public Preview pomocí následujícího příkazu:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Váš požadavek bude potom tým ExpressRoute schválit do 2-3 pracovních dnů.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Přidání privátního partnerského vztahu IPv6 do okruhu ExpressRoute

1. [Vytvořte okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) nebo použijte existující okruh. Načtěte okruh spuštěním příkazu **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Načtěte konfiguraci privátního partnerského vztahu pro okruh spuštěním **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Přidejte privátní partnerský vztah IPv6 do existující konfigurace privátního partnerského vztahu IPv4. Zadejte dvojici podsítí IPv6 (/126), které vlastníte pro primární propojení a sekundární odkazy. Z každé z těchto podsítí přiřadíte směrovač první použitelná IP adresa, protože společnost Microsoft použije pro svůj směrovač druhou použitelnou IP adresu.

> [!Note]
> Partnerské číslo ASN a VlanId by se mělo shodovat s číslem v konfiguraci privátního partnerského vztahu IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Po úspěšném uložení konfigurace přečtěte tento okruh spuštěním příkazu **Get-AzExpressRouteCircuit** . Odpověď by měla vypadat podobně jako v následujícím příkladu:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Aktualizace připojení k existující virtuální síti

Použijte následující postup, pokud máte existující prostředí prostředků Azure v oblasti s Zóny dostupnosti, kterou chcete použít pro privátní partnerské vztahy IPv6 s nástrojem.

1. Načtěte virtuální síť, ke které je připojený okruh ExpressRoute.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Přidejte adresní prostor IPv6 do virtuální sítě.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Přidejte adresní prostor IPv6 do podsítě brány. Podsíť brány IPv6 pro bránu by měla být/64 nebo větší.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Pokud máte existující zónu s redundantní bránou, spusťte následující příkaz, kterým povolíte připojení IPv6. V opačném případě [vytvořte bránu virtuální sítě](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) pomocí redundantní SKU zóny (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Vytvoření připojení k nové virtuální síti

Postupujte podle následujících kroků, pokud se chcete připojit k nové sadě prostředků Azure v oblasti s Zóny dostupnosti pomocí privátního partnerského vztahu IPv6.

1. Vytvořte virtuální síť se dvěma zásobníky s adresním prostorem IPv4 i IPv6. Další informace najdete v tématu [vytvoření virtuální sítě](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Vytvořte podsíť brány Dual-Stack](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Vytvořte bránu virtuální sítě](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) pomocí zóny – redundantní SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Pokud plánujete používat FastPath, použijte ErGw3AZ.

4. [Připojte svoji virtuální síť k okruhu ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Omezení
I když je podpora protokolu IPv6 dostupná pro připojení k nasazením v oblastech s Zóny dostupnosti, nepodporuje následující případy použití:

* Připojení k nasazením v Azure prostřednictvím neaz ExpressRoute Gateway SKU
* Připojení k nasazením v oblastech mimo AZ
* Připojení Global Reach mezi okruhy ExpressRoute

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s ExpressRoute, přečtěte si následující články:

* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
