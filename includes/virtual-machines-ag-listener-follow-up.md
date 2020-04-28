---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174954"
---
Po vytvoření naslouchacího procesu skupiny dostupnosti může být nutné upravit parametry clusteru RegisterAllProvidersIP a HostRecordTTL pro prostředek naslouchacího procesu. Tyto parametry mohou zkrátit dobu opětovného připojení po převzetí služeb při selhání, což by mohlo zabránit vypršení časových limitů připojení. Další informace o těchto parametrech a ukázkovém kódu najdete v tématu [Vytvoření nebo konfigurace naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

