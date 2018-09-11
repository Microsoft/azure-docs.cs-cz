---
title: Zobrazit úlohy Azure Stream Analytics v sadě Visual Studio
description: Tento článek popisuje, jak zobrazit úlohy Stream Analytics v sadě Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: dba0474e83c886edcad1101083e45ff39ee222dd
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300219"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio

Nástroje Azure Stream Analytics pro Visual Studio usnadňuje vývojářům spravovat přímo z integrovaného vývojového prostředí svoje úlohy Stream Analytics. Nástroje Azure Stream Analytics vám umožní:
- [Vytvoření nové úlohy](stream-analytics-quick-create-vs.md)
- Spuštění, zastavení, a [monitorování úloh](stream-analytics-monitor-jobs-use-vs.md)
- Zkontrolujte výsledky úlohy
- Export stávající úlohy do projektu
- Testovat vstupní a výstupní připojení
- [Spouštění dotazů místně](stream-analytics-vs-tools-local-run.md)

Zjistěte, jak [instalace nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Prozkoumejte zobrazení úloh

Zobrazení úloh můžete použít k interakci s úlohy Azure Stream Analytics ze sady Visual Studio.

### <a name="open-the-job-view"></a>Otevřete zobrazení úloh

1. V **Průzkumníka serveru**vyberte **úlohy Stream Analytics** a pak vyberte **aktualizovat**. Vaše úloha by se měla zobrazit pod **úlohy Stream Analytics**.

    ![Stream Analytics seznam Průzkumníka serveru](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Rozbalte uzel projektu a dvakrát klikněte na **zobrazení úloh** uzlu otevřete zobrazení úloh.
    
   ![Rozšířené úlohy uzlu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Spuštění a zastavení úlohy

Úlohy Azure Stream Analytics je plně spravovat v zobrazení úlohy v sadě Visual Studio. Pomocí ovládacích prvků pro spuštění, zastavení nebo odstranění úlohy.
    
   ![Ovládací prvky úlohy Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Zkontrolujte výsledky úlohy

Stream Analytics tools for Visual Studio teď podporuje výstup ve verzi preview pro úložiště Azure Data Lake Storage a blob storage. Chcete-li zobrazit výsledek, jednoduše dvakrát klikněte na uzel výstup z diagram úloh v **zobrazení úloh** a zadejte příslušné přihlašovací údaje.

   ![Výstup blob úlohy Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Export úloh do projektu

Existují dva způsoby, můžete to taky existující úlohy do projektu.

1. V **Průzkumníka serveru**, pod uzlem úlohy Stream Analytics, klikněte pravým tlačítkem na uzel projektu. Vyberte **exportovat do nového projektu Stream Analytics**.
    
   ![Úloha exportu do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Generovaný projekt se objeví v **Průzkumníka řešení**.
    
   ![Průzkumník řešení](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. V zobrazení úloh, vyberte **generování projektu**.
    
   ![Vytvořit projekt z zobrazení úloh](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testovat připojení

Vstupní a výstupní připojení můžete otestovat z **zobrazení úloh** výběrem možnosti z **Test připojení** rozevíracího seznamu.

   ![Test připojení rozevíracího seznamu](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**Test připojení** výsledky jsou zobrazeny v **výstup** okna.

   ![Výsledky testu připojení](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Další postup

* [Monitorování a Správa úloh Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Kurz: Nasazení úlohy Azure Stream Analytics s CI/CD s využitím Azure kanály](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
