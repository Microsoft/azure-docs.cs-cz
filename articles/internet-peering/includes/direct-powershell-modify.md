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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774260"
---
Tato část popisuje, jak provádět následující operace změny pro přímý partnerský vztah:

* Přidání připojení přímého partnerského vztahu
* Odebrání připojení přímého partnerského vztahu
* Upgrade nebo downgrade šířku pásma na aktivní připojení.
* Přidejte relaci IPv4/IPv6 u aktivních připojení.
* Odebrání relace IPv4/IPv6 u aktivních připojení.

### <a name="add-direct-peering-connections"></a>Přidání připojení přímého partnerského vztahu

Níže uvedený příklad popisuje, jak přidat připojení k existujícímu přímému partnerského vztahu

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

Odebrání připojení není v prostředí PowerShell aktuálně podporováno. Obraťte se na [partnerský vztah společnosti Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgrade nebo snížení šířky pásma u aktivních připojení

Níže uvedený příklad popisuje, jak přidat 10 Gb/s k existujícímu přímému připojení.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidejte relaci IPv4/IPv6 u aktivních připojení.

Níže uvedený příklad popisuje, jak přidat relaci IPv6 k existujícímu přímému připojení pouze s relací IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrání relace IPv4/IPv6 u aktivních připojení.

Odebrání relace IPv4/IPv6 z existujícího připojení není v prostředí PowerShell aktuálně podporováno. Obraťte se na [partnerský vztah společnosti Microsoft](mailto:peeringexperience@microsoft.com).