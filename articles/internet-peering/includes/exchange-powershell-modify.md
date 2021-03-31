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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678690"
---
Pro partnerský vztah Exchange jsou podporovány následující operace úprav:
* Přidejte připojení partnerských vztahů Exchange.
* Odeberte připojení partnerských vztahů Exchange.
* Přidejte relaci protokolu IPv4 nebo IPv6 na aktivní připojení.
* Odebere relaci protokolu IPv4 nebo IPv6 na aktivních připojeních.


### <a name="add-exchange-peering-connections"></a>Přidat připojení partnerských vztahů Exchange

Tento příklad popisuje, jak přidat připojení k existujícímu partnerskému serveru Exchange.

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

### <a name="remove-exchange-peering-connections"></a>Odebrat připojení partnerských vztahů Exchange

Tento příklad popisuje, jak odebrat připojení k existujícímu partnerskému serveru Exchange.

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

V následujícím příkazu místo 0 zadejte číslo indexu pro připojení, které chcete odebrat.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Přidání relace protokolu IPv4 nebo IPv6 na aktivní připojení

Tento příklad popisuje, jak přidat relaci IPv6 k existujícímu připojení systému Exchange.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Odebrání relace protokolu IPv4 nebo IPv6 v aktivních připojeních

Odebrání relace protokolu IPv4 nebo IPv6 ze stávajícího připojení není aktuálně podporováno v prostředí PowerShell. Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).