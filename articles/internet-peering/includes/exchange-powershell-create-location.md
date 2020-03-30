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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774390"
---
Rutina prostředí PowerShell **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným parametrem `Kind`, který použijete v pozdějších krocích:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Umístění partnerského vztahu serveru Exchange obsahují následující pole:
* ExchangeName
* Umístění partnerského vztahu
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Adresa MicrosoftIPv4Address
* Adresa MicrosoftIPv6Address

Ověřte, zda se k dispozici v požadovaném partnerském vztahu pomocí [PeeringDB](https://wwww.peeringdb.com).

Níže je uveden příklad, který ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření partnerského vztahu:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```