---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174954"
---
Po vytvoření naslouchacího procesu skupiny dostupnosti může být nutné upravit parametry clusteru RegisterAllProvidersIP a HostRecordTTL pro prostředek naslouchacího procesu. Tyto parametry mohou zkrátit dobu opětovného připojení po převzetí služeb při selhání, což by mohlo zabránit vypršení časových limitů připojení. Další informace o těchto parametrech a ukázkovém kódu najdete v tématu [Vytvoření nebo konfigurace naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

