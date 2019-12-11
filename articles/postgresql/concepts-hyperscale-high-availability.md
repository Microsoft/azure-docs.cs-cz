---
title: Vysoká dostupnost – Citus (High-Scale) – Azure Database for PostgreSQL
description: Koncepce vysoké dostupnosti a zotavení po havárii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975529"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Vysoká dostupnost v Azure Database for PostgreSQL – škálovatelné (Citus)

Vysoká dostupnost (HA) zabraňuje výpadkům databází díky správě samostatných replik všech uzlů ve skupině serverů. Když se vypne uzel, Hyperscale přepne příchozí připojení z chybového uzlu na příslušný rezervní uzel. Během několika minut dojde k převzetí služeb při selhání a uzly se zvýšenou úrovní mají vždycky čerstvá data díky synchronní replikaci streamování PostgreSQL.

Aby bylo možné využít výhod HA v uzlu koordinátora, musí databázové aplikace detekovat a opakovat zrušená připojení a neúspěšné transakce. Nově povýšený koordinátor bude přístupný se stejným připojovacím řetězcem.

Obnovení může být rozdělené do tří fází: detekce, převzetí služeb při selhání a úplné obnovení.  Škálovatelné běhy spouští pravidelné kontroly stavu na všech uzlech a po čtyřech neúspěšných kontrolách zjistí, že uzel je mimo provoz. Škálování pak propaguje v pohotovostním stavu na primární uzel (převzetí služeb při selhání) a zřídí nový pohotovostní režim.
Zahájí se replikace streamování, čímž se nový uzel bude v aktuálním stavu.  Po replikaci všech dat uzel dosáhl úplného obnovení.

### <a name="next-steps"></a>Další kroky

- Naučte se, jak [Povolit vysokou dostupnost](howto-hyperscale-high-availability.md) ve skupině serverů s velkou škálou.
