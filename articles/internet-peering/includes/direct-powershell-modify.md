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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680784"
---
Tato část popisuje, jak provádět následující operace změny pro přímý partnerský vztah:

* Přidejte přímá připojení partnerského vztahu.
* Odeberte přímá připojení partnerského vztahu.
* Upgrade nebo downgrade šířku pásma na aktivní připojení.
* Přidejte relace IPv4 nebo IPv6 v aktivních připojeních.
* Odeberte relace IPv4 nebo IPv6 u aktivních připojení.

### <a name="add-direct-peering-connections"></a>Přidání připojení přímého partnerského vztahu

Tento příklad popisuje, jak přidat připojení k existujícímu přímému partnerského vztahu.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Odebrání připojení přímého partnerského vztahu

Odebrání připojení není v prostředí PowerShell u powershellu aktuálně podporováno. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgrade nebo snížení šířky pásma u aktivních připojení

Tento příklad popisuje, jak přidat 10 Gb/s k existujícímu přímému připojení.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Přidání relací IPv4 nebo IPv6 v aktivních připojeních

Tento příklad popisuje, jak přidat relaci IPv6 na existující přímé připojení pouze s relací IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Odebrání relací IPv4 nebo IPv6 u aktivních připojení

Odebrání relace IPv4 nebo IPv6 z existujícího připojení není aktuálně podporováno v prostředí PowerShell. Další informace získáte od [společnosti Microsoft peeringu](mailto:peeringexperience@microsoft.com).