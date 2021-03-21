---
title: Jak monitorovat aplikace Apache Spark v synapse studiu
description: Naučte se monitorovat Apache Spark aplikace pomocí nástroje synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586036"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Jak používat synapse Studio k monitorování aplikací Apache Spark

Pomocí služby Azure synapse Analytics můžete pomocí Sparku spouštět poznámkové bloky, úlohy a jiné druhy aplikací ve vašich fondech Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat aplikace Apache Spark, což vám umožní sledovat nejnovější stav, problémy a průběh.

## <a name="access-apache-spark-applications-list"></a>Přístup k seznamu Apache Spark aplikací

Pokud chcete zobrazit seznam aplikací Apache Spark ve vašem pracovním prostoru, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Pokud chcete zobrazit seznam aplikací Apache Spark, vyberte **Apache Spark aplikace** .

 ![Vybrat aplikace Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrování aplikací Apache Spark

Seznam aplikací Apache Spark můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze aplikace Apache Spark, které obsahují název "prodej":

![Vzorový filtr](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Zobrazit podrobnosti o konkrétní Apache Spark aplikaci

Chcete-li zobrazit podrobnosti o jedné z aplikací Apache Spark, vyberte aplikaci Apache Spark a Prohlédněte si podrobnosti. Pokud je aplikace Apache Spark stále spuštěná, můžete monitorovat průběh. [Další informace](apache-spark-applications.md).

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování kanálu spuštění synapse studia](how-to-monitor-pipeline-runs.md) . 

Další informace o ladění Apache Spark aplikace najdete v článku [monitorování Apache Spark aplikace v synapse studiu](apache-spark-applications.md) .