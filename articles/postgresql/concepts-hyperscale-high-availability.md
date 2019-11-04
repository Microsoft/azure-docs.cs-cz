---
title: Vysoká dostupnost v Azure Database for PostgreSQL – škálovatelné (Citus)
description: Koncepce vysoké dostupnosti a zotavení po havárii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514742"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Vysoká dostupnost v Azure Database for PostgreSQL – škálovatelné (Citus)

Vysoká dostupnost (HA) brání výpadkům databáze tím, že udržuje pohotovostní repliky všech uzlů ve skupině serverů. Pokud dojde k výpadku uzlu, změní škálování příchozí připojení z neúspěšného uzlu do jeho úsporného režimu. Převzetí služeb při selhání proběhne během několika minut a propagované uzly mají vždycky nová data prostřednictvím replikace synchronního streamování PostgreSQL.

Aby bylo možné využít výhod HA v uzlu koordinátora, musí databázové aplikace detekovat a opakovat zrušená připojení a neúspěšné transakce. Nově povýšený koordinátor bude přístupný se stejným připojovacím řetězcem.

Obnovení může být rozdělené do tří fází: detekce, převzetí služeb při selhání a úplné obnovení.  Škálovatelné běhy spouští pravidelné kontroly stavu na všech uzlech a po čtyřech neúspěšných kontrolách zjistí, že uzel je mimo provoz. Škálování pak propaguje v pohotovostním stavu na primární uzel (převzetí služeb při selhání) a zřídí nový pohotovostní režim.
Zahájí se replikace streamování, čímž se nový uzel bude v aktuálním stavu.  Po replikaci všech dat uzel dosáhl úplného obnovení.

### <a name="next-steps"></a>Další kroky

- Naučte se, jak [Povolit vysokou dostupnost](howto-hyperscale-high-availability.md) ve skupině serverů s velkou škálou.
