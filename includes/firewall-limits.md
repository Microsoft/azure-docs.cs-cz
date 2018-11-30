---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/26/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8b236bc1f0089b89aca90e7c69e9b445b01a374b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440054"
---
| Prostředek | Výchozí omezení |
| --- | --- |
| Zpracovaná data |1000 TB/brána firewall/měsíc <sup>1</sup> |
|Pravidla|10 tisíc – všechna pravidla kombinovat typy|
|Globální partnerský vztah|Nepodporuje se. Měli byste mít minimálně jedno nasazení brány firewall v jedné oblasti.|
|Maximální počet portů v jedné síti pravidlo|15<br>Rozsah portů (například: 2 10) se počítá jako dvě.
|Minimální velikost AzureFirewallSubnet |/26


<sup>1</sup> požádejte podporu Azure v případě potřebujete tyto limity zvýšit.
