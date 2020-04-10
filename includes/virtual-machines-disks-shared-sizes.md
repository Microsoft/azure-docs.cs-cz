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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008323"
---
Prozatím mohou sdílené disky povolit pouze ultra disky a prémiové Disky SSD. Různé velikosti disků `maxShares` mohou mít jiný limit, `maxShares` který nelze překročit při nastavování hodnoty. U disků SSD pro premium jsou velikosti disků, které podporují sdílení disků, P15 a vyšší.

Pro každý disk můžete `maxShares` definovat hodnotu, která představuje maximální počet uzlů, které mohou současně sdílet disk. Pokud například plánujete nastavit cluster s podporou převzetí služeb při `maxShares=2`selhání se 2 uzny, nastavíte . Maximální hodnota je horní mez. Uzly se mohou připojit nebo opustit cluster (připojit nebo odpojit disk), `maxShares` pokud je počet uzlů nižší než zadaná hodnota.

> [!NOTE]
> Hodnotu `maxShares` lze nastavit nebo upravit pouze v případě, že je disk odpojen od všech uzlů.

### <a name="premium-ssd-ranges"></a>Prémiové řady SSD

Následující tabulka znázorňuje povolené `maxShares` maximální hodnoty podle velikosti disků premium:

|Velikosti disků  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Viposlužby a omezení šířky pásma `maxShares` pro disk nejsou ovlivněny hodnotou. Například max IOPS disku P15 je 1100, zda maxShares = 1 nebo maxShares > 1.

### <a name="ultra-disk-ranges"></a>Rozsahy ultra disků

Minimální `maxShares` hodnota je 1, `maxShares` zatímco maximální hodnota je 5. Neexistují žádná omezení velikosti na ultra disky, libovolná `maxShares`velikost ultra disk může použít libovolnou hodnotu pro , až do maximální hodnoty včetně.