---
title: Uzly v Azure Database for PostgreSQL – Citus (Preview)
description: Přečtěte si o dvou typech uzlů, koordinátora a pracovní procesy ve skupině serverů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 097fcdb3a7e53bb63db9dc2d352d754062df7be6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947560"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Uzly v Azure Database for PostgreSQL – Citus (Preview)

Typ hostingu Citus (ve verzi Preview) umožňuje Azure Database for PostgreSQL servery (nazývané uzly), aby se vzájemně koordinovaly v architektuře "Shared Nothing". Uzly ve skupině serverů společně uchovávají více dat a využívají více jader procesoru, než by bylo možné na jednom serveru. Architektura také umožňuje škálování databáze přidáním dalších uzlů do skupiny serverů.

## <a name="coordinator-and-workers"></a>Koordinátor a pracovníci

Každá skupina serverů má uzel koordinátora a více pracovních procesů. Aplikace odesílají své dotazy do uzlu koordinátora, který je přenáší na příslušné pracovníky a shromažďuje jejich výsledky. Aplikace se nemůžou přímo připojit k pracovníkům.

U každého dotazu ho koordinátor směruje buď na jeden pracovní uzel, nebo ho parallelizes napříč několika v závislosti na tom, jestli jsou požadovaná data umístěná v jednom uzlu nebo víc. Koordinátor rozhodne o tom, co dělat v rámci konzultací s tabulkami metadat. Tyto tabulky sledují názvy DNS a stav pracovních uzlů a distribuci dat napříč uzly.

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak uzly ukládají [distribuovaná data](concepts-hyperscale-distributed-data.md)
