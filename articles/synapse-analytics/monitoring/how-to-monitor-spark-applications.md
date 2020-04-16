---
title: Sledování aplikací Apache Spark
description: Azure Synapse Studio můžete monitorovat své aplikace Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430744"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Azure Synapse Studio (preview) slouží ke sledování aplikací Apache Spark

S Azure Synapse Analytics můžete pomocí Sparku spouštět poznámkové bloky, úlohy a další druhy aplikací ve svých fondech Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak sledovat aplikace Spark, což vám umožní sledovat nejnovější stav, problémy a pokrok.

## <a name="accessing-the-list-of-spark-applications"></a>Přístup k seznamu aplikací Spark

Pokud chcete zobrazit seznam aplikací Spark ve vašem pracovním prostoru, nejdřív [otevřete Azure Synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

  > [!div class="mx-imgBorder"]
  > ![Přihlášení do pracovního prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte část **Monitor** vlevo.

  > [!div class="mx-imgBorder"]
  > ![Vybrat centrum monitoru](./media/common/left-nav.png)

Vyberte **aplikace Spark,** chcete-li zobrazit seznam aplikací Spark.

  > [!div class="mx-imgBorder"]
  > ![Vybrat aplikace Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrování aplikací Spark

Seznam aplikací Spark můžete filtrovat na ty, které vás zajímají. Filtry v horní části obrazovky umožňují určit pole, na které chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze aplikace Spark, které obsahují název "prodej":

  > [!div class="mx-imgBorder"]
  > ![Tlačítko Filtrovat](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Ukázkový filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Zobrazení podrobností o konkrétní aplikaci Spark

Chcete-li zobrazit podrobnosti o jedné z aplikací Spark, vyberte aplikaci Spark a zobrazte podrobnosti. Pokud je aplikace Spark stále spuštěná, můžete sledovat průběh.

## <a name="next-steps"></a>Další kroky

Další informace o spuštění kanálu monitorování, najdete [v tématu monitorování kanálu běží Azure Synapse Studio](how-to-monitor-pipeline-runs.md) článku.  
