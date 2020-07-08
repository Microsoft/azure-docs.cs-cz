---
title: Uzly – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Přečtěte si o dvou typech uzlů, koordinátora a pracovní procesy ve skupině serverů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74973998"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Uzly v Azure Database for PostgreSQL – Citus (škálování)

Typ hostingu Citus () Azure Database for PostgreSQL umožňuje, aby se servery (označované jako uzly) mezi sebou vzájemně koordinovaly v architektuře "Shared Nothing". Uzly ve skupině serverů společně uchovávají více dat a využívají více jader procesoru, než by bylo možné na jednom serveru. Architektura také umožňuje škálování databáze přidáním dalších uzlů do skupiny serverů.

## <a name="coordinator-and-workers"></a>Koordinátor a pracovníci

Každá skupina serverů má uzel koordinátora a více pracovních procesů. Aplikace odesílají své dotazy do uzlu koordinátora, který je přenáší na příslušné pracovníky a shromažďuje jejich výsledky. Aplikace se nemůžou přímo připojit k pracovníkům.

U každého dotazu ho koordinátor směruje buď na jeden pracovní uzel, nebo ho parallelizes napříč několika v závislosti na tom, jestli jsou požadovaná data umístěná v jednom uzlu nebo víc. Koordinátor rozhodne o tom, co dělat v rámci konzultací s tabulkami metadat. Tyto tabulky sledují názvy DNS a stav pracovních uzlů a distribuci dat napříč uzly.

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak uzly ukládají [distribuovaná data](concepts-hyperscale-distributed-data.md)
