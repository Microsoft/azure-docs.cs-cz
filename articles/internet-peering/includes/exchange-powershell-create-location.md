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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678639"
---
Rutina PowerShellu **Get-AzPeeringLocation** vrátí seznam umístění partnerského vztahu s povinným `Kind`parametrem, který použijete v pozdějších krocích.

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

Pomocí odkazu na [PeeringDB](https://wwww.peeringdb.com)ověřte, že se nacházíte na požadovaném zařízení pro partnerský vztah.

Tento příklad ukazuje, jak použít Seattle jako umístění partnerského vztahu k vytvoření partnerského vztahu.

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