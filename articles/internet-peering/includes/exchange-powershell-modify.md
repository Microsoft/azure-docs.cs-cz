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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774416"
---
Pro partnerský vztah Exchange se podporují následující operace úprav.
1. Přidat připojení partnerských vztahů Exchange
1. Odebrat připojení partnerských vztahů Exchange
1. Přidat relaci protokolu IPv4/IPv6 k aktivním připojením.
1. Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení.


### <a name="add-exchange-peering-connections"></a>Přidat připojení partnerských vztahů Exchange

Níže uvedený příklad popisuje, jak přidat připojení k existujícímu partnerskému serveru Exchange.

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

Níže uvedený příklad popisuje, jak odebrat připojení k existujícímu partnerskému serveru Exchange.

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

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidat relaci IPv4/IPv6 pro aktivní připojení

Níže uvedený příklad popisuje, jak přidat relaci IPv6 do stávajícího připojení k Exchangi.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení

Odebrání relace IPv4/IPv6 ze stávajícího připojení se v prostředí PowerShell aktuálně nepodporuje. Kontaktujte [partnerský vztah Microsoftu](mailto:peeringexperience@microsoft.com).