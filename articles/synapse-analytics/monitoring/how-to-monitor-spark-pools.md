---
title: Jak monitorovat fondy Apache Spark v synapse studiu
description: Naučte se monitorovat fondy Apache Spark pomocí nástroje synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466575"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Monitorování fondů Apache Spark pomocí nástroje synapse Studio

Pomocí služby Azure synapse Analytics můžete pomocí Sparku spouštět poznámkové bloky, úlohy a jiné druhy aplikací ve fondech Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat fondy Apache Spark, abyste měli přehled o stavu fondů, včetně toho, kolik virtuální jádra jsou používány různými uživateli pracovního prostoru.

## <a name="access-spark-pools-list"></a>Přístup k seznamu fondů Spark

Pokud chcete zobrazit seznam fondů Apache Spark ve vašem pracovním prostoru, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte **fondy Apache Spark** , abyste zobrazili seznam fondů Apache Spark.

 ![Vybrat fondy Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrování fondů Spark

Seznam fondů Spark můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze fondy Sparku obsahující název "dataprep":

![Vzorový filtr](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Zobrazit podrobnosti o konkrétním fondu Spark

Pokud chcete zobrazit podrobnosti o jednom z vašich fondů Sparku, vyberte fond Spark pro zobrazení podrobností.

![Podrobnosti o Apache Spark fondu](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování běhu kanálu v synapse studiu](how-to-monitor-pipeline-runs.md) . 

Další informace o monitorování Apache Sparkch aplikací najdete v článku [monitorování Apache Spark aplikace v synapse studiu](how-to-monitor-spark-applications.md) .
