---
title: 'Azure ExpressRoute: resetování partnerského vztahu okruhů'
description: Naučte se, jak povolit a zakázat partnerské vztahy pro okruh Azure ExpressRoute pomocí Azure PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97559569"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetování partnerských vztahů okruhu ExpressRoute

Tento článek popisuje, jak povolit a zakázat partnerské vztahy okruhu ExpressRoute pomocí prostředí PowerShell. Partnerské vztahy jsou ve výchozím nastavení povoleny při jejich vytváření. Když zakážete partnerský vztah, vypne se relace protokolu BGP v primárním i sekundárním připojení okruhu ExpressRoute. U tohoto partnerského vztahu k Microsoftu přijdete o připojení. Když povolíte partnerský vztah, bude se navázat relace protokolu BGP na primárním i sekundárním připojení okruhu ExpressRoute. Připojení k Microsoftu se obnoví pro tento partnerský vztah. Partnerský vztah pro partnerský vztah Microsoftu a soukromý partnerský vztah Azure můžete povolit a zakázat nezávisle na okruhu ExpressRoute.

Existují dva scénáře, kdy může být užitečné resetovat partnerské vztahy ExpressRoute.
* Pokud chcete otestovat návrh a implementaci zotavení po havárii. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerské vztahy na jednom okruhu a vynutit převzetí služeb při selhání v síti jiným okruhem.
* Povolte rozpoznávání obousměrného předávání (BFD) buď na privátním partnerském vztahu Azure, nebo v partnerském vztahu Microsoftu vašeho okruhu ExpressRoute. BFD je ve výchozím nastavení povolený u privátního partnerského vztahu Azure, pokud jste okruh ExpressRoute vytvořili po 1. srpnu 2018 a pro partnerský vztah Microsoftu po 10. lednu 2020. Pokud byl okruh vytvořen před uvedeným datem, budete muset resetovat partnerský vztah, aby povolil BFD. 

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Resetování partnerského vztahu

1. Pokud používáte PowerShell místně, otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Určete předplatné, které chcete použít.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Spusťte následující příkazy, abyste načetli okruh ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifikujte partnerský vztah, který chcete zakázat nebo povolit. *Partnerské vztahy* je pole. V následujícím příkladu partnerské vztahy [0] jsou privátní partnerské vztahy Azure a partnerské vztahy [1] partnerské vztahy Microsoftu.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
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
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Spusťte následující příkazy, abyste změnili stav partnerského vztahu.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Partnerský vztah by měl být ve stavu, který jste nastavili. 

## <a name="next-steps"></a>Další kroky
Pokud potřebujete pomoc s řešením problémů s ExpressRoute, přečtěte si následující články:
* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
