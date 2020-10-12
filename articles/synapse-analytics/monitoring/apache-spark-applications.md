---
title: Monitorování Apache Spark aplikací pomocí synapse studia
description: Pomocí synapse studia můžete monitorovat aplikace Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387349"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Monitorování aplikací Apache Spark pomocí nástroje synapse Studio

Pomocí služby Azure synapse Analytics můžete pomocí Apache Spark spouštět poznámkové bloky, úlohy a jiné druhy aplikací v fondech Apache Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat aplikace Apache Spark, což vám umožní sledovat nejnovější stav, problémy a průběh.

Tento kurz se zabývá následujícími úkony:

* Monitorování běžící Apache Spark aplikace
* Zobrazení dokončené Apache Spark aplikace
* Zobrazit zrušenou Apache Spark aplikaci
* Ladění Apache Spark aplikace selhalo.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:

- Pracovní prostor synapse Studio. Pokyny najdete v tématu [Vytvoření pracovního prostoru synapse Studio](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace).

- Fond Apache Spark.

## <a name="monitor-running-apache-spark-application"></a>Monitorování běžící Apache Spark aplikace

Otevřete **monitorování**a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o spuštěných aplikacích Apache Spark, vyberte odeslání aplikace Apache Spark a zobrazte podrobnosti. Pokud je aplikace Apache Spark stále spuštěná, můžete monitorovat průběh.

  ![vybrat spuštěnou úlohu](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Zkontroluje **dokončené úkoly**, **stav**a **celkovou dobu trvání**.

2. Zrušte Apache Spark aplikaci.

3. Aktualizuje dotaz protokolu.

4. Zobrazit graf

5. Podívejte se na **souhrnné** informace.

6. Ověřte **protokoly**. Informace protokolu jsou při spuštění prázdné.

    ![Zobrazit spuštěnou úlohu](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>Zobrazení dokončené Apache Spark aplikace

Otevřete **monitorování**a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o dokončených Apache Spark aplikacích, vyberte aplikaci Apache Spark a zobrazte podrobnosti.

  ![Výběr dokončené úlohy](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Zkontroluje **dokončené úkoly**, **stav**a **celkovou dobu trvání**.

2. Aktualizuje dotaz protokolu.

3. Kliknutím na položku Server **Historie Spark**otevřete odkaz server pro Apache Spark historie.

4. Podívejte se na **souhrnné** informace kliknutím na ikonu v grafu.

5. Ověřte **protokoly**. Z rozevíracího seznamu můžete vybrat jiný typ protokolů a informace o protokolu si můžete stáhnout kliknutím na **Stáhnout protokoly**.

6. Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Ve výchozím nastavení se v grafu zobrazují všechny úlohy. Toto zobrazení můžete filtrovat podle **ID úlohy**.

7. Ve výchozím nastavení je vybraná možnost zobrazení **průběhu** . Tok dat můžete kontrolovat výběrem možnosti **číst** nebo **napsané** v rozevíracím seznamu **zobrazení** .

8. Pokud chcete úlohu znovu přehrát, vyberte **přehrávání**. Kdykoli můžete zastavit výběr **zastavit** .

9. Pomocí rolovacího posuvníku se můžete přiblížit nebo oddálit v grafu úlohy nebo můžete vybrat možnost **přiblížení** , aby se vešlo na obrazovku.

10. Uzel grafu úlohy zobrazí následující informace o každé fázi:

    * Účet.

    * Název nebo popis

    * Celkové číslo úlohy

    * Přečtená data: součet velikosti vstupní velikosti a náhodné velikosti čtení.

    * Zápis dat: součet velikosti výstupu a náhodné velikosti zápisů.

    * Doba spuštění: čas mezi časem zahájení prvního pokusu a časem dokončení posledního pokusu.

    * Počet řádků: součet vstupních záznamů, výstupních záznamů, náhodného čtení záznamů a náhodného zápisu záznamů.

    * Přejde.

     ![Zobrazit dokončenou úlohu](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Klikněte na graf a zobrazí se podrobnosti o fázi.

   ![Podrobnosti pro fázi](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>Zobrazit zrušenou Apache Spark aplikaci

Otevřete **monitorování**a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o stornovaných Apache Spark aplikacích, vyberte aplikaci Apache Spark a Prohlédněte si podrobnosti.

 ![vybrat zrušenou úlohu](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Zkontroluje **dokončené úkoly**, **stav**a **celkovou dobu trvání**.

2. Aktualizujte dotaz protokolu.

3. Kliknutím na **Historie serveru Spark**otevřete odkaz serveru pro historii Apache.

4. Zobrazit graf

5. Podívejte se na **souhrnné** informace.

6. Ověřte **protokoly**. Z rozevíracího seznamu můžete vybrat jiný typ protokolů a informace o protokolu si můžete stáhnout kliknutím na **Stáhnout protokoly**.

   ![Zobrazit zrušenou úlohu](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Ladění Apache Spark aplikace selhalo.

Otevřete **monitorování**a pak vyberte **Apache Spark aplikace**. Pokud chcete zobrazit podrobnosti o aplikacích, které selhaly Apache Spark, vyberte aplikaci Apache Spark a Prohlédněte si podrobnosti.

![vybrat neúspěšnou úlohu](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Zkontroluje **dokončené úkoly**, **stav**a **celkovou dobu trvání**.

2. Aktualizuje dotaz protokolu.

3. Kliknutím na položku Server **Historie Spark**otevřete odkaz server pro Apache Spark historie.

4. Zobrazit graf

5. Podívejte se na **souhrnné** informace.

6. Podívejte se na informace o chybě.

   ![informace o neúspěšné úloze](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování spuštění kanálu pomocí synapse studia](how-to-monitor-pipeline-runs.md) .  
