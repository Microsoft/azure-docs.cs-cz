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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774260"
---
Tato část popisuje, jak provést následující operace úprav pro přímý partnerský vztah:

* Přidat připojení s přímým partnerským vztahem
* Odebrat připojení s přímým partnerským vztahem
* Upgradovat nebo downgradovat šířku pásma u aktivních připojení
* Přidat relaci protokolu IPv4/IPv6 k aktivním připojením.
* Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení.

### <a name="add-direct-peering-connections"></a>Přidat připojení s přímým partnerským vztahem

Níže uvedený příklad popisuje, jak přidat připojení ke stávajícímu přímému partnerskému vztahu.

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

Odebrání připojení není v prostředí PowerShell aktuálně podporováno. Kontaktujte [partnerský vztah Microsoftu](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgradovat nebo downgradovat šířku pásma u aktivních připojení

Níže uvedený příklad popisuje, jak přidat peering – do existujícího přímého připojení.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Přidat relaci protokolu IPv4/IPv6 k aktivním připojením.

Níže uvedený příklad popisuje, jak přidat relaci IPv6 v existujícím přímém připojení jenom s relací IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Odebrat relaci protokolu IPv4/IPv6 u aktivních připojení.

Odebrání relace IPv4/IPv6 ze stávajícího připojení se v prostředí PowerShell aktuálně nepodporuje. Kontaktujte [partnerský vztah Microsoftu](mailto:peeringexperience@microsoft.com).