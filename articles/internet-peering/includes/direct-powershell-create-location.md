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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774286"
---
Rutina prostředí PowerShell **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným parametrem `Kind`, který použijete v pozdějších krocích:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Umístění přímého partnerského vztahu obsahují následující pole:
* Umístění partnerského vztahu 
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Šířka pásmaNabídky

Ověřte, zda se k dispozici v požadovaném partnerském vztahu pomocí [PeeringDB](https://wwww.peeringdb.com).

Níže je uveden příklad, který ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření přímého partnerského vztahu:

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