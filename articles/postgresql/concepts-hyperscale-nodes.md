---
title: Uzly – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Přečtěte si o dvou typech uzlů, koordinátora a pracovní procesy ve skupině serverů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382793"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Uzly v Azure Database for PostgreSQL – Citus (škálování)

Typ hostingu Citus () Azure Database for PostgreSQL umožňuje, aby se servery (označované jako uzly) mezi sebou vzájemně koordinovaly v architektuře "Shared Nothing". Uzly ve skupině serverů společně uchovávají více dat a využívají více jader procesoru, než by bylo možné na jednom serveru. Architektura také umožňuje škálování databáze přidáním dalších uzlů do skupiny serverů.

## <a name="coordinator-and-workers"></a>Koordinátor a pracovníci

Každá skupina serverů má uzel koordinátora a více pracovních procesů. Aplikace odesílají své dotazy do uzlu koordinátora, který je přenáší na příslušné pracovníky a shromažďuje jejich výsledky. Aplikace se nemůžou přímo připojit k pracovníkům.

Citus () umožňuje správci databáze *distribuovat* tabulky a ukládat různé řádky do různých pracovních uzlů. Distribuované tabulky jsou klíčem k výkonu škálovatelného škálování. Selhání při distribuci tabulek ponechá je zcela v uzlu koordinátora a nemůže využít paralelní paralelismuy mezi počítači.

Pro každý dotaz v distribuovaných tabulkách ho koordinátor směruje buď na jeden pracovní uzel, nebo ho parallelizes napříč několika v závislosti na tom, jestli jsou požadovaná data umístěná na jednom nebo několika uzlech. Koordinátor rozhodne o tom, co dělat v rámci konzultací s tabulkami metadat. Tyto tabulky sledují názvy DNS a stav pracovních uzlů a distribuci dat napříč uzly.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [distribuovaných tabulkách](concepts-hyperscale-distributed-data.md)
