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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471677"
---
V současné době můžou sdílené disky povolit jenom SSD úrovně Premium. Velikosti disků, které podporují tuto funkci, jsou P15 a vyšší. Různé velikosti disků můžou mít jiný limit `maxShares`, což nemůžete překročit při nastavení `maxShares` hodnoty.

Pro každý disk můžete definovat `maxShares` hodnotu, která představuje maximální počet uzlů, které mohou současně sdílet disk. Pokud například plánujete nastavit cluster s podporou převzetí služeb při selhání se dvěma uzly, nastavili jste `maxShares=2`. Maximální hodnota je horní mez. Uzly mohou připojit nebo opustit cluster (připojit nebo odpojit disk), pokud je počet uzlů nižší, než je zadaná hodnota `maxShares`.

> [!NOTE]
> Hodnotu `maxShares` lze nastavit nebo upravit pouze v případě, že je disk odpojen ze všech uzlů.

Následující tabulka ukazuje povolené maximální hodnoty pro `maxShares` podle velikosti disku:

|Velikosti disků  |maxShares limit  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Omezení počtu vstupně-výstupních operací a šířky pásma disku nejsou ovlivněny `maxShares` hodnotou. Například maximální IOPS disku P15 je 1100, zda maxShares = 1 nebo maxShares > 1.