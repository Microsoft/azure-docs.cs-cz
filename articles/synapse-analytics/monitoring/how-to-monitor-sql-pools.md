---
title: Jak monitorovat fondy SQL v synapse studiu
description: Naučte se monitorovat své fondy SQL pomocí synapse studia.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466660"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Monitorování fondů SQL pomocí synapse studia

Pomocí synapse studia můžete spouštět skripty SQL na fondech SQL ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat fondy SQL, což vám umožní sledovat stav a činnost fondů.

## <a name="access-sql-pools-list"></a>Přístup k seznamu fondů SQL

Pokud chcete zobrazit seznam fondů SQL ve vašem pracovním prostoru, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte možnost **fondy SQL** pro zobrazení seznamu fondů SQL.

 ![Výběr fondů SQL](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrování fondů SQL

Seznam fondů SQL můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze fondy SQL obsahující název "salesrecords":

![Vzorový filtr](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Zobrazit podrobnosti o konkrétním fondu SQL

Pokud chcete zobrazit podrobnosti o jednom z vašich fondů SQL, vyberte fond SQL pro zobrazení podrobností.

![Podrobnosti o fondu SQL](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování běhu kanálu v synapse studiu](how-to-monitor-pipeline-runs.md) . 

Další informace o monitorování požadavků SQL najdete v článku [monitorování požadavků SQL v synapse studiu](how-to-monitor-sql-requests.md) .