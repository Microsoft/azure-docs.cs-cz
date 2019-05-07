---
title: Uzly ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)
description: Dva typy uzlů ve skupině serveru.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077272"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Uzly ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

Velkokapacitní (Citus) (preview), typ hostingu umožňuje ke koordinaci mezi sebou v architektuře "paměť s nesdíleným" – Azure Database servery PostgreSQL (nazývané uzly). Uzly ve skupině serverů souhrnně uložení více dat a používat více jader procesoru, než by bylo možné na jednom serveru. Tato architektura také umožňuje databáze, kterou chcete škálovat přidáním dalších uzlů do skupiny serverů.

## <a name="coordinator-and-workers"></a>Koordinátor a pracovních procesů

Každá skupina serveru má koordinační uzel a více zaměstnanců. Aplikace odesílat dotazy na koordinační uzel, který předává příslušným pracovníkům a shromažďuje jejich výsledky. Aplikace, nebudou se moct připojit přímo k pracovních procesů.

Pro každý dotaz koordinátor směruje do jednoho pracovního uzlu nebo parallelizes několika v závislosti na tom, zda požadovaná data se nachází na jeden uzel nebo více. Koordinátor rozhodne, co potřebujete udělat tak, že consulting tabulky metadat. Tyto tabulky sledování názvy DNS a stavu pracovních uzlů a distribuci dat napříč uzly.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak ukládat uzly [distribuovaných dat](concepts-hyperscale-distributed-data.md)
