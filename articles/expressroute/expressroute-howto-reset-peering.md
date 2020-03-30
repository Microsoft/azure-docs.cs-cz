---
title: 'Azure ExpressRoute: Obnovení partnerského vztahu okruhu'
description: Jak zakázat a povolit partnerský vztah okruhu ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941740"
---
# <a name="reset-expressroute-circuit-peerings"></a>Obnovení partnerských stran okruhů ExpressRoute

Tento článek popisuje, jak zakázat a povolit partnerský vztah okruhu ExpressRoute pomocí prostředí PowerShell. Pokud zakážete partnerský vztah, bude relace Protokolu BGP na primárním připojení i sekundárním připojení okruhu ExpressRoute ukončena. Ztratíte připojení prostřednictvím tohoto partnerského vztahu k Microsoftu. Pokud povolíte partnerský vztah, bude vyvolána relace Protokolu BGP v primárním i sekundárním připojení okruhu ExpressRoute. Znovu získáte připojení prostřednictvím tohoto partnerského vztahu k Microsoftu. Můžete povolit a zakázat Microsoft Peering a Azure Private Peering na okruhu ExpressRoute nezávisle. Při první konfiguraci partnerských stran v okruhu ExpressRoute jsou partnerské partnerské partnery ve výchozím nastavení povoleny.

Existuje několik scénářů, kde může být užitečné resetování vašeho partnerského vztahu ExpressRoute.
* Otestujte návrh a implementaci zotavení po havárii. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerské partnerské styky jednoho okruhu a vynutit, aby síťový provoz překračoval služby při selhání do druhého okruhu.
* Povolte obousměrnou detekci předávání (BFD) v privátním partnerském vztahu Azure nebo partnerském vztahu Microsoftu v okruhu ExpressRoute. BFD je ve výchozím nastavení povolena v privátním partnerském vztahu Azure, pokud se okruh ExpressRoute vytvoří po 1. Pokud byl váš okruh vytvořen před tím, BFD nebyla povolena. BfD můžete povolit zakázáním partnerského vztahu a jeho opětovným povolením. 

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Obnovení partnerského vztahu

1. Pokud používáte PowerShell místně, otevřete konzolu PowerShell se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

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
4. Spusťte následující příkazy pro načtení okruhu ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifikujte partnerský vztah, který chcete zakázat nebo povolit. *Partnerských stran* je pole. V následujícím příkladu je partnerský vztah[0] Azure Private Peering a Peerings[1] Microsoft Peering.

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
6. Chcete-li změnit stav partnerského vztahu, spusťte následující příkazy.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Partnerský vztah by měl být ve stavu, který nastavíte. 

## <a name="next-steps"></a>Další kroky
Pokud potřebujete pomoc s řešením problému expressroute, podívejte se na následující články:
* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Poradce při potížích s výkonem sítě](expressroute-troubleshooting-network-performance.md)
