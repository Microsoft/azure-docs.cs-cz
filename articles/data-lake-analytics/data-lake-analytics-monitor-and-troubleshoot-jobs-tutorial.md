---
title: Monitorování Azure Data Lake Analytics – portál Azure
description: Tento článek popisuje, jak používat portál Azure k řešení potíží s úlohami Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316592"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorování úloh v Azure Data Lake Analytics pomocí webu Azure Portal

**Chcete-li zobrazit všechny úlohy**

1. Na portálu Azure klikněte v levém horním rohu na **Microsoft Azure.**
2. Klikněte na dlaždici s názvem účtu Data Lake Analytics.  Souhrn úlohy se zobrazí na dlaždici **Správa úloh.**

    ![Správa úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Správa úlohy vám poskytne přehled o stavu úlohy. Všimněte si, že došlo k neúspěšné úloze.
3. Kliknutím na dlaždici **Správa úloh** zobrazíte úlohy. Úlohy jsou zařazeny do kategorií **Spuštěno**, **Zařazeno do fronty**a **Ukončeno**. Neúspěšnou úlohu uvidíte v části **Ukončeno.** Jedná se o první v seznamu. Pokud máte mnoho úloh, můžete kliknout na **filtr,** který vám pomůže najít úlohy.

    ![Úlohy filtru Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kliknutím na neúspěšnou úlohu ze seznamu otevřete podrobnosti o úloze:

    ![Úloha Azure Data Lake Analytics selhala](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Všimněte si tlačítka **Znovu odeslat.** Po vyřešení problému můžete úlohu znovu odeslat.
5. Kliknutím na zvýrazněnou část z předchozího snímku obrazovky otevřete podrobnosti o chybě.  Uvidíte něco jako:

    ![Azure Data Lake Analytics nepodařilo podrobnosti o úloze](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    To vám řekne, že zdrojová složka nebyla nalezena.
6. Klepněte na **položku Duplicitní skript**.
7. Aktualizujte cestu **FROM** na:

    "/Ukázky/Data/SearchLog.tsv"
8. Klikněte na **Odeslat úlohu**.

## <a name="see-also"></a>Viz také
* [Azure Data Lake Analytics – přehled](data-lake-analytics-overview.md)
* [Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu](data-lake-analytics-get-started-powershell.md)
* [Správa Azure Data Lake Analytics pomocí portálu Azure](data-lake-analytics-manage-use-portal.md)
