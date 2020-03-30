---
title: Vysoká dostupnost – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Vysoká dostupnost a koncepce zotavení po havárii
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975529"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Vysoká dostupnost v databázi Azure pro PostgreSQL – Hyperscale (Citus)

Vysoká dostupnost (HA) zabraňuje prostojům databáze udržováním pohotovostních replik každého uzlu ve skupině serverů. Pokud uzel přejde dolů, hyperškálování přepne příchozí připojení z uzlu selhání do jeho pohotovostního režimu. Převzetí služeb při selhání se stane během několika minut a propagované uzly mají vždy čerstvá data prostřednictvím replikace synchronního streamování PostgreSQL.

Chcete-li využít výhod ha na uzlu koordinátora, databázové aplikace je třeba zjistit a opakovat vyřazených připojení a neúspěšných transakcí. Nově povýšený koordinátor bude přístupný se stejným připojovacím řetězcem.

Obnovení lze rozdělit do tří fází: detekce, převzetí služeb při selhání a úplné obnovení.  Hyperscale spouští pravidelné kontroly stavu na každém uzlu a po čtyřech neúspěšných kontrolách určuje, že uzel je dole. Hyperscale pak podporuje pohotovostní režim na primární stav uzlu (převzetí služeb při selhání) a zřídí nový pohotovostní režim.
Začne streamování replikace, čímž nový uzel aktuální.  Po replikaci všech dat uzel dosáhl úplného obnovení.

### <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [povolit vysokou dostupnost](howto-hyperscale-high-availability.md) ve skupině serverů Hyperscale.
