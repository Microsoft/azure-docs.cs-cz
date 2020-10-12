---
title: 'Azure ExpressRoute: resetování partnerského vztahu okruhů'
description: Naučte se, jak zakázat a povolit partnerské vztahy okruhu Azure ExpressRoute pomocí Azure PowerShell. Když nakonfigurujete partnerské vztahy, budou ve výchozím nastavení povolené.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 01/13/2018
ms.author: duau
ms.openlocfilehash: f3b34966aa46ca8d663f83ab2aceafa4b0dda2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395736"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetování partnerských vztahů okruhu ExpressRoute

Tento článek popisuje, jak zakázat a povolit partnerské vztahy okruhu ExpressRoute pomocí prostředí PowerShell. Když zakážete partnerský vztah, vypne se relace protokolu BGP v primárním připojení i sekundární připojení okruhu ExpressRoute. Přes tento partnerský vztah k Microsoftu ztratíte připojení. Když povolíte partnerský vztah, provede se relace protokolu BGP v primárním připojení i sekundární připojení okruhu ExpressRoute. Prostřednictvím tohoto partnerského vztahu k Microsoftu budete moct znovu připojit. V okruhu ExpressRoute můžete nezávisle povolit a zakázat partnerský partnerský vztah Microsoftu a privátní partnerské vztahy Azure. Při první konfiguraci partnerských vztahů v okruhu ExpressRoute jsou partnerské vztahy ve výchozím nastavení povolené.

Existuje několik scénářů, ve kterých může být užitečné resetovat partnerské vztahy ExpressRoute.
* Otestujte návrh a implementaci zotavení po havárii. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerské vztahy jednoho okruhu a vynutit převzetí služeb při selhání provozu v síti jiným okruhem.
* Povolte rozpoznávání obousměrného předávání (BFD) na privátním partnerském vztahu Azure nebo partnerském vztahu Microsoftu vašeho okruhu ExpressRoute. BFD je ve výchozím nastavení povolená u privátního partnerského vztahu Azure, pokud se váš okruh ExpressRoute vytvoří po 1 2018. srpna a v partnerském vztahu Microsoftu, pokud se váš okruh ExpressRoute vytvoří po lednu 10 2020. Pokud byl okruh vytvořen dříve, BFD nebyl povolen. BFD můžete povolit zakázáním partnerského vztahu a jeho povolením. 

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
Pokud potřebujete pomoct s řešením problému s ExpressRoute, přečtěte si následující články:
* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
