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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680784"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah:

* Přidejte připojení s přímým partnerským vztahem.
* Odeberte připojení s přímým partnerským vztahem.
* Upgradovat nebo downgradovat šířku pásma u aktivních připojení
* Přidat relace protokolu IPv4 nebo IPv6 na aktivní připojení.
* Odebere relace protokolu IPv4 nebo IPv6 na aktivních připojeních.

### <a name="add-direct-peering-connections"></a>Přidat připojení s přímým partnerským vztahem

Tento příklad popisuje, jak přidat připojení ke stávajícímu přímému partnerskému vztahu.

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

### <a name="remove-direct-peering-connections"></a>Odebrat připojení s přímým partnerským vztahem

Odebrání připojení není v prostředí PowerShell aktuálně podporováno. Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgradovat nebo downgradovat šířku pásma u aktivních připojení

Tento příklad popisuje, jak přidat 10 GB/s do stávajícího přímého připojení.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Přidat relace protokolu IPv4 nebo IPv6 na aktivní připojení

Tento příklad popisuje, jak přidat relaci IPv6 v existujícím přímém připojení jenom s relací IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Odebrat relace protokolu IPv4 nebo IPv6 u aktivních připojení

Odebrání relace protokolu IPv4 nebo IPv6 ze stávajícího připojení není aktuálně podporováno v prostředí PowerShell. Další informace získáte u [partnerského vztahu Microsoftu](mailto:peeringexperience@microsoft.com).