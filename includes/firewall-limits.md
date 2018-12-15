---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 7d905550114bb76a0a091146b3972bab4a652022
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429944"
---
| Prostředek | Výchozí omezení |
| --- | --- |
| Zpracovaná data |1000 TB/brána firewall/měsíc <sup>1</sup> |
|Pravidla|10 tisíc – všechna pravidla kombinovat typy|
|Globální partnerský vztah|Nepodporuje se. Měli byste mít minimálně jedno nasazení brány firewall v jedné oblasti.|
|Maximální počet portů v jedné síti pravidlo|15<br>Rozsah portů (například: 2 - 10) se počítá jako dvě.
|Minimální velikost AzureFirewallSubnet |/26|
|Rozsah portů v pravidlech sítě a aplikace|0-64 000. Práce se toto omezení zmírnit.|
|


<sup>1</sup> požádejte podporu Azure v případě potřebujete tyto limity zvýšit.
