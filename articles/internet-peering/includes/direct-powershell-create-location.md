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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774286"
---
Rutina PowerShellu **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným parametrem `Kind`, který použijete v pozdějších krocích:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Umístění s přímým partnerským vztahem obsahují následující pole:
* PeeringLocation 
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Pomocí odkazu na [PeeringDB](https://w www.peeringdb.com)ověřte, že se nacházíte na požadovaném zařízení pro partnerský vztah.

Níže je příklad, který ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření přímého partnerského vztahu:

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```