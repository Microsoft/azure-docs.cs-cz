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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680780"
---
Rutina PowerShellu **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným parametrem `Kind` , který použijete v pozdějších krocích.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Umístění s přímým partnerským vztahem obsahují následující pole:
* PeeringLocation 
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Odkazem na [PeeringDB](https://wwww.peeringdb.com)ověřte, že jste přítomni na požadovaném zařízení pro partnerský vztah.

Tento příklad ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření přímého partnerského vztahu.

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