---
title: Uzly – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Seznamte se s dvěma typy uzlů, koordinátorem a pracovníky, ve skupině serverů v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973998"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Uzly v databázi Azure pro PostgreSQL – hyperškálování (Citus)

Typ hostování Hyperscale (Citus) umožňuje Azure Database pro servery PostgreSQL (nazývané uzly) vzájemně koordinovat v architektuře "sdílené nic". Uzly ve skupině serverů společně uchovávat více dat a používat více jader procesoru, než by bylo možné na jednom serveru. Architektura také umožňuje databázi škálovat přidáním dalších uzlů do skupiny serverů.

## <a name="coordinator-and-workers"></a>Koordinátor a pracovníci

Každá skupina serverů má uzel koordinátora a více pracovníků. Aplikace odesílají své dotazy do uzlu koordinátora, který je předává příslušným pracovníkům a shromažďuje jejich výsledky. Aplikace se nemohou připojit přímo k pracovníkům.

Pro každý dotaz koordinátor buď směruje do jednoho pracovního uzlu nebo paralelizuje přes několik v závislosti na tom, zda požadovaná data žije na jednom uzlu nebo více. Koordinátor rozhoduje o tom, co má dělat, nahlédnutím do tabulek metadat. Tyto tabulky sledují názvy DNS a stav uzlů pracovníka a distribuci dat mezi uzly.

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak uzly ukládají [distribuovaná data](concepts-hyperscale-distributed-data.md)
