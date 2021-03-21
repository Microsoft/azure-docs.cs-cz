---
title: Jak monitorovat požadavky SQL v synapse studiu
description: Naučte se monitorovat požadavky SQL pomocí synapse studia.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466653"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Použití synapse studia k monitorování požadavků SQL

Pomocí synapse studia můžete spouštět skripty SQL na fondech SQL ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat požadavky SQL, což vám umožní udržet si přehled o stavu spuštěných požadavků a zjistit podrobnosti o historických požadavcích.

## <a name="access-sql-requests-list"></a>Přístup k seznamu požadavků SQL

Pokud chcete zobrazit seznam požadavků SQL ve vašem pracovním prostoru, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte **požadavky SQL** pro zobrazení seznamu požadavků SQL.

 ![Výběr požadavků SQL](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Vybrat fond SQL

Ve výchozím nastavení se v tomto zobrazení zobrazí historie požadavků SQL pro integrovaný fond SQL bez serveru. Pokud si chcete zobrazit historii požadavků SQL tohoto fondu, můžete vybrat jeden z vašich vyhrazených fondů SQL.

![Vybrat fond SQL](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrování požadavků SQL

Seznam požadavků SQL můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze požadavky SQL odeslané `maria@contoso.com` :

![Vzorový filtr](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Zobrazit podrobnosti o konkrétní žádosti SQL

Chcete-li zobrazit podrobnosti o jednom z vašich požadavků SQL, otevřete požadavek SQL a přejděte do zobrazení podrobností. Pro komplexní požadavky spuštěné ve vyhrazených fondech SQL můžete sledovat průběh.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování běhu kanálu v synapse studiu](how-to-monitor-pipeline-runs.md) . 

Další informace o monitorování Apache Sparkch aplikací najdete v článku [monitorování Apache Spark aplikace v synapse studiu](how-to-monitor-spark-applications.md) .