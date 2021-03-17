---
title: Zobrazení Azure Stream Analytics úloh v aplikaci Visual Studio
description: Naučte se zobrazovat, spouštět a zastavovat, testovat připojení, kontrolovat výsledky a exportovat Azure Stream Analytics úlohy pomocí sady Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019121"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh

Azure Stream Analytics Tools for Visual Studio umožňují vývojářům snadno spravovat úlohy Stream Analytics přímo z integrovaného vývojového prostředí. Pomocí nástrojů pro Azure Stream Analytics můžete:
- [Vytvořit nové úlohy](stream-analytics-quick-create-vs.md)
- Spustit, zastavit a [monitorovat úlohy](stream-analytics-monitor-jobs-use-vs.md)
- Kontrolovat výsledky úlohy
- Exportovat existující úlohy do projektu
- Testování vstupních a výstupních připojení
- [Spustit dotazy místně](stream-analytics-vs-tools-local-run.md)

Přečtěte si, jak [nainstalovat nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Prozkoumat zobrazení úlohy

Můžete použít zobrazení úlohy k interakci s Azure Stream Analytics úlohami ze sady Visual Studio.

### <a name="open-the-job-view"></a>Otevřete zobrazení úlohy.

1. V **Průzkumník serveru** vyberte **Stream Analytics úlohy** a pak vyberte **aktualizovat**. Vaše úloha by se měla zobrazit v části **Stream Analytics úlohy**.

    ![Seznam Stream Analytics Průzkumníku serveru](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Rozbalte uzel úlohy a dvojím kliknutím na uzel **zobrazení úlohy** otevřete zobrazení úlohy.
    
   ![Uzel rozbalené úlohy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Spouštění a zastavování úloh

Azure Stream Analytics úlohy je možné plně spravovat ze zobrazení úloh v aplikaci Visual Studio. Pomocí ovládacích prvků můžete spustit, zastavit nebo odstranit úlohu.
    
   ![Ovládací prvky úlohy Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Kontrolovat výsledky úlohy

Stream Analytics Tools for Visual Studio v současné době podporuje Náhled výstupu pro Azure Data Lake Storage a úložiště objektů BLOB. Pokud chcete zobrazit výsledek, jednoduše poklikejte na uzel výstup diagramu úloh v **zobrazení úlohy** a zadejte příslušné přihlašovací údaje.

   ![Výstup objektu BLOB úlohy Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportovat úlohy do projektu

Existují dva způsoby, jak můžete exportovat existující úlohu do projektu.

1. V **Průzkumník serveru** pod uzlem úlohy Stream Analytics klikněte pravým tlačítkem myši na uzel úlohy. Vyberte **exportovat do nového projektu Stream Analytics**.
    
   ![Exportovat úlohu do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Vygenerovaný projekt se zobrazí v **Průzkumník řešení**.
    
   ![Průzkumník řešení](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. V zobrazení úlohy vyberte **generovat projekt**.
    
   ![Generovat projekt ze zobrazení úlohy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Test připojení

Vstupní a výstupní připojení lze testovat ze **zobrazení úlohy** výběrem možnosti z rozevíracího seznamu **Test připojení** .

   ![Rozevírací seznam testovat připojení](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Výsledky **testu připojení** se zobrazí v okně **výstup** .

   ![Test výsledků připojení](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Další kroky

* [Monitorování a správa úloh Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
