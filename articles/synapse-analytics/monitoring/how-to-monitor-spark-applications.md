---
title: Monitorování Apache Sparkch aplikací
description: K monitorování aplikací Apache Spark použijte Azure synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430744"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>K monitorování aplikací Apache Spark použijte Azure synapse Studio (Preview).

Pomocí služby Azure synapse Analytics můžete pomocí Sparku spouštět poznámkové bloky, úlohy a jiné druhy aplikací ve vašich fondech Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat aplikace Spark, což vám umožní sledovat nejnovější stav, problémy a průběh.

## <a name="accessing-the-list-of-spark-applications"></a>Přístup k seznamu aplikací Spark

Pokud chcete zobrazit seznam aplikací Spark ve vašem pracovním prostoru, nejdřív [otevřete Azure synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

  > [!div class="mx-imgBorder"]
  > ![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

  > [!div class="mx-imgBorder"]
  > ![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte **aplikace Spark** , abyste zobrazili seznam aplikací Spark.

  > [!div class="mx-imgBorder"]
  > ![Vybrat aplikace Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrování aplikací Spark

Seznam aplikací Spark můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze aplikace Spark, které obsahují název "prodej":

  > [!div class="mx-imgBorder"]
  > ![Tlačítko Filtr](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Vzorový filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Zobrazení podrobností o konkrétní aplikaci Spark

Pokud chcete zobrazit podrobnosti o jedné z vašich aplikací Spark, vyberte aplikaci Spark a podívejte se na podrobnosti. Pokud je aplikace Spark stále spuštěná, můžete monitorovat průběh.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování kanálu spouští Azure synapse Studio](how-to-monitor-pipeline-runs.md) .  
