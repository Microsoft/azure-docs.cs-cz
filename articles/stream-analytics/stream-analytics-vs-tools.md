---
title: Zobrazení úloh Azure Stream Analytics ve Visual Studiu
description: Naučte se zobrazit, spustit a zastavit, otestovat připojení, zkontrolovat výsledky a exportovat úlohy Azure Stream Analytics pomocí Visual Studia.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369622"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Zobrazení úloh Azure Stream Analytics pomocí Visual Studia

Nástroje Azure Stream Analytics pro Visual Studio usnadňují vývojářům spravovat úlohy Stream Analytics přímo z ide. Pomocí nástrojů Azure Stream Analytics můžete:
- [Vytváření nových pracovních míst](stream-analytics-quick-create-vs.md)
- Spuštění, zastavení a [sledování úloh](stream-analytics-monitor-jobs-use-vs.md)
- Zkontrolovat výsledky úlohy
- Export existujících úloh do projektu
- Testování vstupních a výstupních připojení
- [Spouštět dotazy místně](stream-analytics-vs-tools-local-run.md)

Přečtěte si, jak [nainstalovat nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Prozkoumejte zobrazení úloh

Zobrazení úloh můžete použít k interakci s úlohami Azure Stream Analytics z Visual Studia.

### <a name="open-the-job-view"></a>Otevření zobrazení úlohy

1. V **Průzkumníkovi serveru**vyberte **úlohy Stream Analytics** a pak vyberte **Aktualizovat**. Vaše úloha by se měla zobrazit v části **Úlohy Služby Stream Analytics**.

    ![Seznam průzkumníků serverů Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Rozbalte uzel úlohy a poklepáním na uzel **zobrazení úloh** otevřete zobrazení úlohy.
    
   ![Rozšířený uzel úlohy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Spuštění a zastavení úloh

Úlohy Azure Stream Analytics lze plně spravovat ze zobrazení úloh ve Visual Studiu. Pomocí ovládacích prvků můžete spustit, zastavit nebo odstranit úlohu.
    
   ![Ovládací prvky úloh Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Zkontrolovat výsledky úlohy

Nástroje Stream Analytics pro Visual Studio aktuálně podporují náhled výstupu pro Azure Data Lake Storage a úložiště objektů blob. Chcete-li zobrazit výsledek, jednoduše poklepejte na výstupní uzel diagramu úloh v **zobrazení úloh** y a zadejte příslušná pověření.

   ![Výstup objektu blob úlohy Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Export úloh do projektu

Existující úlohu lze exportovat do projektu dvěma způsoby.

1. V **Průzkumníkovi serveru**klikněte v uzlu Úlohy analýzy datových proudů pravým tlačítkem myši na uzel úlohy. Vyberte **Exportovat do projektu New Stream Analytics Project**.
    
   ![Export úlohy do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Vygenerovaný projekt se zobrazí v **Průzkumníku řešení**.
    
   ![Průzkumník řešení](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. V zobrazení úloh vyberte **Generovat projekt**.
    
   ![Generovat projekt ze zobrazení úlohy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testovací připojení

Vstupní a výstupní připojení lze testovat ze **zobrazení úloh** výběrem možnosti v rozevíracím okně **Testovací připojení.**

   ![Rozevírací informace o testovacím připojení](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Výsledky **testovacího připojení** jsou zobrazeny v okně **Výstup.**

   ![Výsledky testovacího připojení](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Další kroky

* [Sledování a správa úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-monitor-jobs-use-vs.md)
* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md)
* [Kurz: Nasazení úlohy Azure Stream Analytics s CI/CD pomocí Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
