---
title: 'Azure ExpressRoute: resetování partnerského vztahu okruhů'
description: Postup zakázání a povolení partnerské vztahy okruhu ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941740"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetovat partnerské vztahy okruhu ExpressRoute

Tento článek popisuje, jak zakázat a povolit partnerské vztahy okruhu ExpressRoute pomocí prostředí PowerShell. Pokud zakážete partnerský vztah, se ukončí relace protokolu BGP na připojení k primární a sekundární připojení váš okruh ExpressRoute. Dojde ke ztrátě připojení prostřednictvím tohoto partnerského vztahu Microsoftu. Když povolíte partnerský vztah, se aktivují relace protokolu BGP na připojení k primární a sekundární připojení váš okruh ExpressRoute. Se obnoví připojení prostřednictvím tohoto partnerského vztahu Microsoftu. Můžete povolit nebo zakázat Microsoft Peering a privátní partnerské vztahy Azure na okruh ExpressRoute nezávisle na sobě. Při první konfiguraci partnerských vztahů na váš okruh ExpressRoute, jsou ve výchozím nastavení povolené partnerské vztahy.

Existuje několik scénářů, kde vám může být užitečné resetování vašeho partnerské vztahy ExpressRoute.
* Testování návrhu zotavení po havárii a implementaci. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerských vztahů jednoho okruhu a vynutit provozu vaší sítě do dalších okruhů převzít služby při selhání.
* Povolte rozpoznávání obousměrného předávání (BFD) na privátním partnerském vztahu Azure nebo partnerském vztahu Microsoftu vašeho okruhu ExpressRoute. BFD je ve výchozím nastavení povolená u privátního partnerského vztahu Azure, pokud se váš okruh ExpressRoute vytvoří po 1 2018. srpna a v partnerském vztahu Microsoftu, pokud se váš okruh ExpressRoute vytvoří po lednu 10 2020. Pokud před, který byl vytvořen váš okruh, BFD nebyl povolen. Můžete povolit BFD zakázáním partnerský vztah a opětovné povolení ho. 

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Obnovení nastavení partnerského vztahu

1. Pokud používáte PowerShell místně, otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

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
4. Spusťte následující příkazy pro načtení váš okruh ExpressRoute.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifikace, partnerský vztah, který chcete povolit nebo zakázat. *Partnerské vztahy* je pole. V následujícím příkladu je partnerské vztahy [0] privátní partnerské vztahy Azure a Microsoft Peering partnerské vztahy [1].

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
6. Spusťte následující příkazy, které mění stav partnerského vztahu.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Partnerský vztah musí být ve stavu, které jste nastavili. 

## <a name="next-steps"></a>Další kroky
Pokud potřebujete pomoc při řešení potíží s ExpressRoute, přečtěte si následující články:
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
