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
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775844"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Monitorování aplikací Apache Spark pomocí nástroje synapse Studio

Pomocí služby Azure synapse Analytics můžete pomocí Apache Spark spouštět poznámkové bloky, úlohy a jiné druhy aplikací v fondech Apache Spark ve vašem pracovním prostoru.

Tento článek vysvětluje, jak monitorovat aplikace Apache Spark, což vám umožní sledovat nejnovější stav, problémy a průběh.

Tento kurz se zabývá následujícími úkony:

* Monitorování běžící Apache Spark aplikace
* Zobrazení dokončené Apache Spark aplikace
* Zobrazit zrušenou Apache Spark aplikaci
* Ladění Apache Spark aplikace selhalo.

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:

- Pracovní prostor synapse Studio. Pokyny najdete v tématu [Vytvoření pracovního prostoru synapse Studio](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Fond Apache Spark.

## <a name="view-apache-spark-applications"></a>Zobrazit Apache Spark aplikace 
Můžete zobrazit všechny Apache Spark aplikace z **monitorování**  ->  **Apache Spark aplikace**.
   ![aplikace Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Zobrazení dokončené Apache Spark aplikace

Otevřete **monitorování** a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o dokončených Apache Spark aplikacích, vyberte aplikaci Apache Spark a zobrazte podrobnosti.

  ![Výběr dokončené úlohy](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Zkontroluje **dokončené úkoly**, **stav** a **celkovou dobu trvání**.

2. Aktualizuje dotaz protokolu.

3. Kliknutím na položku Server **Historie Spark** otevřete odkaz server pro Apache Spark historie.

4. Podívejte se na **souhrnné** informace.

5. Ověřte **protokoly**. Z rozevíracího seznamu můžete vybrat jiný typ protokolů a informace o protokolu si můžete stáhnout tak, že kliknete na **Stáhnout protokoly** a potom zaškrtnutím políčka **filtrovat chyby a upozornění** vyfiltrujete chyby a upozornění, která potřebujete.

6. Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Ve výchozím nastavení se v grafu zobrazují všechny úlohy. Toto zobrazení můžete filtrovat podle **ID úlohy**.

7. Ve výchozím nastavení je vybraná možnost zobrazení **průběhu** . Tok dat můžete kontrolovat výběrem možnosti  /  /  / **Doba** čtení v rozevíracím seznamu **zobrazení** průběhu.

8. Pokud chcete úlohu přehrát, klikněte na tlačítko **přehrávání** . Kdykoli můžete kliknout na tlačítko **zastavit** .

9. Pro přiblížení a zmenšení grafu úlohy můžete použít posouvání ukazatele myši nebo posuvník, ale také můžete vybrat možnost **přiblížení** , aby se vešlo na obrazovku.

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
    
11. Klikněte na **Zobrazit podrobnosti** v grafu a pak se zobrazí podrobnosti o fázi.

    ![Podrobnosti pro fázi](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Monitorování běžící Apache Spark aplikace

Otevřete **monitorování** a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o spuštěných aplikacích Apache Spark, vyberte odeslání aplikace Apache Spark a zobrazte podrobnosti. Pokud je aplikace Apache Spark stále spuštěná, můžete monitorovat průběh.

   ![vybrat spuštěnou úlohu](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Zkontroluje **dokončené úkoly**, **stav** a **celkovou dobu trvání**.

2. **Zrušte** Apache Spark aplikaci.

3. **Aktualizovat** Dotaz protokolu

4. Kliknutím na tlačítko **uživatelského rozhraní Spark** přejděte na stránku úlohy Spark.

5. Zobrazit graf Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Podívejte se na krok 6, 7, 8, 9, 10 [zobrazení dokončeno Apache Spark aplikaci](#view-completed-apache-spark-application).

6. Podívejte se na **souhrnné** informace.

7. Podívejte se na kartu Diagnostika na **diagnostiku** .

8. Podívejte se na **protokoly** na této kartě. Z rozevíracího seznamu můžete vybrat jiný typ protokolů a informace o protokolu si můžete stáhnout tak, že kliknete na **Stáhnout protokoly** a potom zaškrtnutím políčka **filtrovat chyby a upozornění** vyfiltrujete chyby a upozornění, která potřebujete.

    ![Zobrazit spuštěnou úlohu](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Zobrazit zrušenou Apache Spark aplikaci

Otevřete **monitorování** a pak vyberte **Apache Spark aplikace**. Chcete-li zobrazit podrobnosti o stornovaných Apache Spark aplikacích, vyberte aplikaci Apache Spark a Prohlédněte si podrobnosti.

 ![vybrat zrušenou úlohu](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Zkontroluje **dokončené úkoly**, **stav** a **celkovou dobu trvání**.

2. Aktualizujte dotaz protokolu.

3. Kliknutím na **Historie serveru Spark** otevřete odkaz serveru pro historii Apache.

4. Zobrazit graf Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Podívejte se na krok 6, 7, 8, 9, 10 [zobrazení dokončeno Apache Spark aplikaci](#view-completed-apache-spark-application).

5. Podívejte se na **souhrnné** informace.

6. Ověřte **protokoly**. Z rozevíracího seznamu můžete vybrat jiný typ protokolů a informace o protokolu si můžete stáhnout tak, že kliknete na **Stáhnout** protokoly a potom zaškrtnutím políčka **filtrovat chyby a upozornění** vyfiltrujete chyby a upozornění, která potřebujete.

7. Klikněte na **Zobrazit podrobnosti** v grafu a pak se zobrazí podrobnosti o fázi.

   ![Zobrazit zrušenou úlohu](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Ladění Apache Spark aplikace selhalo.

Otevřete **monitorování** a pak vyberte **Apache Spark aplikace**. Pokud chcete zobrazit podrobnosti o aplikacích, které selhaly Apache Spark, vyberte aplikaci Apache Spark a Prohlédněte si podrobnosti.

![vybrat neúspěšnou úlohu](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Zkontroluje **dokončené úkoly**, **stav** a **celkovou dobu trvání**.

2. Aktualizuje dotaz protokolu.

3. Kliknutím na položku Server **Historie Spark** otevřete odkaz server pro Apache Spark historie.

4. Zobrazit graf Přehled úlohy můžete zobrazit v grafu vygenerované úlohy. Podívejte se na krok 6, 7, 8, 9, 10 [dokončených Apache Sparkch aplikací.](#view-completed-apache-spark-application)

5. Podívejte se na **souhrnné** informace.

6. Podívejte se na informace o chybě.

   ![informace o neúspěšné úloze](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Porovnání aplikací Apache Spark

Existují dva způsoby, jak porovnat aplikace. Můžete porovnat volbou **aplikace porovnat** nebo kliknutím na tlačítko **porovnat v poznámkovém bloku** je zobrazit v poznámkovém bloku.

### <a name="compare-by-choose-an-application"></a>Porovnat volbou aplikace

Klikněte na tlačítko **Porovnat aplikace** a vyberte aplikaci pro porovnání výkonu, můžete intuitivním rozdílem mezi těmito dvěma aplikacemi zobrazit rozdíl.

![porovnat aplikace](./media/how-to-monitor-spark-applications/compare-applications.png)

![Podrobnosti porovnat aplikace](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Pomocí myši najeďte na aplikaci a zobrazí se ikona **Porovnat aplikace** .

2. Klikněte na ikonu **Porovnat aplikace** a automaticky se zobrazí stránka porovnat aplikace.

3. Kliknutím na tlačítko **Zvolit aplikaci** otevřete stránku **Zvolit aplikaci porovnání** .

4. Při výběru aplikace pro porovnání musíte zadat adresu URL aplikace, nebo si vybrat ze seznamu opakování. Pak klikněte na tlačítko **OK** . 

   ![zvolit aplikaci porovnání](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Výsledek porovnání se zobrazí na stránce porovnat aplikace.

   ![výsledek porovnání](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Porovnat porovnáním poznámkového bloku

Kliknutím na tlačítko **porovnat v poznámkovém bloku** na stránce **Porovnat aplikace** otevřete Poznámkový blok. Výchozím názvem souboru. ipynb je **přesoučasná analýza aplikace**.

![porovnat v poznámkovém bloku](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

V poznámkovém bloku: znovu aktuální soubor analýzy aplikace můžete spustit přímo po nastavení fondu a jazyka Spark.

![přesoučasná analýza aplikace](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování spuštění kanálu najdete v článku [monitorování spuštění kanálu pomocí synapse studia](how-to-monitor-pipeline-runs.md) .
