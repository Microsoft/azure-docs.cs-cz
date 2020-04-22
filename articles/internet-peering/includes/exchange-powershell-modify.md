---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678690"
---
Pro partnerský vztah serveru Exchange jsou podporovány následující operace úprav:
* Přidejte připojení partnerského vztahu serveru Exchange.
* Odeberte připojení partnerského vztahu serveru Exchange.
* Přidejte relaci IPv4 nebo IPv6 u aktivních připojení.
* Odeberte relaci IPv4 nebo IPv6 u aktivních připojení.


### <a name="add-exchange-peering-connections"></a>Přidání připojení partnerského vztahu exchange

Tento příklad popisuje, jak přidat připojení k existujícímu partnerského vztahu serveru Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Odebrání připojení partnerského vztahu serveru Exchange

Tento příklad popisuje, jak odebrat připojení k existujícímu partnerského vztahu serveru Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Zobrazte všechna připojení a vyberte připojení, které chcete odebrat. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

V následujícím příkazu zadejte místo 0 číslo indexu pro připojení, které chcete odebrat.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Přidání relace IPv4 nebo IPv6 u aktivních připojení

Tento příklad popisuje, jak přidat relaci IPv6 k existujícímu připojení serveru Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Odebrání relace IPv4 nebo IPv6 u aktivních připojení

Odebrání relace IPv4 nebo IPv6 z existujícího připojení není aktuálně podporováno v prostředí PowerShell. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).