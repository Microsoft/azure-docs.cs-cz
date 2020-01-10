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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774390"
---
Rutina PowerShellu **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným parametrem `Kind`, který použijete v pozdějších krocích:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Umístění partnerských vztahů Exchange obsahují následující pole:
* Výměna
* PeeringLocation
* Země
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Pomocí odkazu na [PeeringDB](https://w www.peeringdb.com)ověřte, že se nacházíte na požadovaném zařízení pro partnerský vztah.

Níže je příklad, který ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření partnerského vztahu:

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