---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174954"
---
Po vytvoření naslouchací proces skupiny dostupnosti může být nutné upravit parametry clusteru RegisterAllProvidersIP a HostRecordTTL pro prostředek naslouchacího procesu. Tyto parametry mohou zkrátit dobu opětovného připojení po převzetí služeb při selhání, což může zabránit časovým limitům připojení. Další informace o těchto parametrech a ukázkový kód naleznete v [tématu Vytvoření nebo konfigurace naslouchací proces skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

