---
title: 'Resetování okruhu, partnerského vztahu – ExpressRoute: Azure | Dokumentace Microsoftu'
description: Postup zakázání a povolení partnerské vztahy okruhu ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: ad050e11c98139af00ad752f8960d55a58ca2f34
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132581"
---
# <a name="reset-expressroute-circuit-peerings"></a>Resetovat partnerské vztahy okruhu ExpressRoute

Tento článek popisuje, jak zakázat a povolit partnerské vztahy okruhu ExpressRoute pomocí prostředí PowerShell. Pokud zakážete partnerský vztah, se ukončí relace protokolu BGP na připojení k primární a sekundární připojení váš okruh ExpressRoute. Dojde ke ztrátě připojení prostřednictvím tohoto partnerského vztahu Microsoftu. Když povolíte partnerský vztah, se aktivují relace protokolu BGP na připojení k primární a sekundární připojení váš okruh ExpressRoute. Se obnoví připojení prostřednictvím tohoto partnerského vztahu Microsoftu. Můžete povolit nebo zakázat Microsoft Peering a privátní partnerské vztahy Azure na okruh ExpressRoute nezávisle na sobě. Při první konfiguraci partnerských vztahů na váš okruh ExpressRoute, jsou ve výchozím nastavení povolené partnerské vztahy.

Existuje několik scénářů, kde vám může být užitečné resetování vašeho partnerské vztahy ExpressRoute.
* Testování návrhu zotavení po havárii a implementaci. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerských vztahů jednoho okruhu a vynutit provozu vaší sítě do dalších okruhů převzít služby při selhání.
* Povolení obousměrného předávání detekce (BFD) na Azure soukromého partnerského vztahu pro váš okruh ExpressRoute. BFD je ve výchozím nastavení povolené, pokud váš okruh ExpressRoute je vytvořená po 1. srpna 2018. Pokud před, který byl vytvořen váš okruh, BFD nebyl povolen. Můžete povolit BFD zakázáním partnerský vztah a opětovné povolení ho. Je třeba poznamenat, že BFD je podporován pouze v privátní partnerské vztahy Azure.

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Obnovení nastavení partnerského vztahu

1. Pokud používáte PowerShell místně, otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```azurepowershell
  Connect-AzureRmAccount
  ```
2. Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
3. Určete předplatné, které chcete použít.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Spusťte následující příkazy pro načtení váš okruh ExpressRoute.

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```
Partnerský vztah musí být ve stavu, které jste nastavili. 

## <a name="next-steps"></a>Další postup
Pokud potřebujete pomoc při řešení potíží s ExpressRoute, přečtěte si následující články:
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
