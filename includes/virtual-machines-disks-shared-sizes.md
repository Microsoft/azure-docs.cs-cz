---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81008323"
---
V současné době můžou sdílené disky povolit jenom disky s Ultra SSD a Premium. Různé velikosti disků mohou mít jiný `maxShares` limit, což není při nastavení hodnoty možné překročit `maxShares` . Pro Premium SSD jsou velikosti disků, které podporují sdílení svých disků, P15 a větší.

Pro každý disk můžete definovat `maxShares` hodnotu, která představuje maximální počet uzlů, které mohou současně sdílet disk. Pokud například plánujete nastavit cluster s podporou převzetí služeb při selhání se dvěma uzly, nastavíte `maxShares=2` . Maximální hodnota je horní mez. Uzly se můžou připojit k clusteru (připojit nebo odpojit disk), pokud je počet uzlů menší, než je zadaná `maxShares` hodnota.

> [!NOTE]
> `maxShares`Hodnotu lze nastavit nebo upravit pouze v případě, že je disk odpojen ze všech uzlů.

### <a name="premium-ssd-ranges"></a>Rozsahy SSD úrovně Premium

Následující tabulka ukazuje maximální povolené hodnoty pro `maxShares` velikosti disků úrovně Premium:

|Velikosti disků  |maxShares limit  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Omezení počtu vstupně-výstupních operací a šířky pásma disku nejsou ovlivněny `maxShares` hodnotou. Například maximální IOPS disku P15 je 1100, zda maxShares = 1 nebo maxShares > 1.

### <a name="ultra-disk-ranges"></a>Rozsahy disků Ultra

Minimální `maxShares` hodnota je 1, zatímco maximální `maxShares` hodnota je 5. Pro disky Ultra nejsou k dispozici žádná omezení velikosti, libovolná velikost Ultra disk může používat libovolnou hodnotu `maxShares` , až do a včetně maximální hodnoty.