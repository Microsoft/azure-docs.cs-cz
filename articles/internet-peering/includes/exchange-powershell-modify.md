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
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774416"
---
Následující operace úprav jsou podporovány pro partnerský vztah serveru Exchange
1. Přidání připojení partnerského vztahu exchange
1. Odebrání připojení partnerského vztahu serveru Exchange
1. Přidejte relaci IPv4/IPv6 u aktivních připojení.
1. Odebrání relace IPv4/IPv6 u aktivních připojení.


### <a name="add-exchange-peering-connections"></a>Přidání připojení partnerského vztahu exchange

Následující příklad popisuje, jak přidat připojení k existujícímu partnerského vztahu serveru Exchange.

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

Následující příklad popisuje, jak odebrat připojení k existujícímu partnerského vztahu serveru Exchange.

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

V níže uvedeném příkazu místo 0 zadejte číslo indexu pro připojení, které chcete odebrat.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidání relace IPv4/IPv6 u aktivních připojení

Níže uvedený příklad popisuje, jak přidat relaci IPv6 do existujícího připojení exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrání relace IPv4/IPv6 u aktivních připojení

Odebrání relace IPv4/IPv6 z existujícího připojení není v prostředí PowerShell aktuálně podporováno. Obraťte se na [partnerský vztah společnosti Microsoft](mailto:peeringexperience@microsoft.com).