---
title: Vysoká dostupnost – Citus (High-Scale) – Azure Database for PostgreSQL
description: Koncepce vysoké dostupnosti a zotavení po havárii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 406b4f2ada665d65ee0452579e24744d1f7cfc63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314849"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Vysoká dostupnost v Azure Database for PostgreSQL – škálovatelné (Citus)

Vysoká dostupnost (HA) brání výpadkům databáze tím, že udržuje pohotovostní repliky všech uzlů ve skupině serverů. Pokud dojde k výpadku uzlu, Citus (škálování) přepne příchozí připojení z neúspěšného uzlu do jeho úsporného režimu. Převzetí služeb při selhání proběhne během několika minut a propagované uzly mají vždycky nová data prostřednictvím replikace synchronního streamování PostgreSQL.

Aby bylo možné využít výhod HA v uzlu koordinátora, musí databázové aplikace detekovat a opakovat zrušená připojení a neúspěšné transakce. Nově povýšený koordinátor bude přístupný se stejným připojovacím řetězcem.

Obnovení může být rozdělené do tří fází: detekce, převzetí služeb při selhání a úplné obnovení.  Citus) spouští pravidelné kontroly stavu na všech uzlech a po čtyřech neúspěšných kontrolách zjistí, že uzel je mimo provoz. Citus (škálování na více koncových uzlů) potom propaguje pohotovostní stav na primární uzel (převzetí služeb při selhání) a zřídí nový pohotovostní režim.
Zahájí se replikace streamování, čímž se nový uzel bude v aktuálním stavu.  Po replikaci všech dat uzel dosáhl úplného obnovení.

### <a name="next-steps"></a>Další kroky

- Naučte se, jak [Povolit vysokou dostupnost](howto-hyperscale-high-availability.md) ve skupině serverů Citus (High Scale).
