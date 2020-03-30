---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471677"
---
Prozatím mohou sdílené disky povolit pouze prémiové disky SSD. Velikosti disků, které podporují tuto funkci jsou P15 a větší. Různé velikosti disků `maxShares` mohou mít jiný limit, `maxShares` který nelze překročit při nastavování hodnoty.

Pro každý disk můžete `maxShares` definovat hodnotu, která představuje maximální počet uzlů, které mohou současně sdílet disk. Pokud například plánujete nastavit cluster s podporou převzetí služeb při `maxShares=2`selhání se 2 uzny, nastavíte . Maximální hodnota je horní mez. Uzly se mohou připojit nebo opustit cluster (připojit nebo odpojit disk), `maxShares` pokud je počet uzlů nižší než zadaná hodnota.

> [!NOTE]
> Hodnotu `maxShares` lze nastavit nebo upravit pouze v případě, že je disk odpojen od všech uzlů.

Následující tabulka znázorňuje povolené `maxShares` maximální hodnoty podle velikosti disku:

|Velikosti disků  |limit maxShares  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Viposlužby a omezení šířky pásma `maxShares` pro disk nejsou ovlivněny hodnotou. Například max IOPS disku P15 jsou 1100, zda maxShares = 1 nebo maxShares > 1.