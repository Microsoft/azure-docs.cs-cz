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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680780"
---
Rutina prostředí PowerShell **Get-AzPeeringLocation** vrátí seznam umístění partnerského `Kind`vztahu s povinným parametrem , který použijete v pozdějších krocích.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Umístění přímého partnerského vztahu obsahují následující pole:
* Umístění partnerského vztahu 
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Šířka pásmaNabídky

Ověřte, zda jste přítomni v požadovaném partnerském vztahu, odkazem na [PeeringDB](https://wwww.peeringdb.com).

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