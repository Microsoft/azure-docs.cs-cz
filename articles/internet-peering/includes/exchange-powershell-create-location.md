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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678639"
---
Rutina prostředí PowerShell **Get-AzPeeringLocation** vrátí seznam umístění partnerského `Kind`vztahu s povinným parametrem , který použijete v pozdějších krocích.

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