---
title: Monitorování Azure Data Lake Analytics – Azure Portal
description: Tento článek popisuje, jak použít Azure Portal k řešení potíží s úlohami Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316592"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorování úloh v Azure Data Lake Analytics pomocí webu Azure Portal

**Zobrazení všech úloh**

1. V Azure Portal klikněte na **Microsoft Azure** v levém horním rohu.
2. Klikněte na dlaždici s názvem účtu Data Lake Analytics.  Souhrn úlohy se zobrazí na dlaždici **Správa úloh** .

    ![Správa úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Správa úloh vám poskytne přehled o stavu úlohy. Všimněte si, že úloha není úspěšná.
3. Kliknutím na dlaždici **Správa úloh** Zobrazte úlohy. Úlohy jsou zařazené dokategorií spuštěno, zařazeno do **fronty**a **skončilo**. Vaše úloha, která selhala, se zobrazí v části ukončeno. První z nich je v seznamu. Když máte spoustu úloh, můžete kliknout na **Filtr** a pomůžou vám najít úlohy.

    ![Azure Data Lake Analytics filtrovat úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Kliknutím na neúspěšnou úlohu ze seznamu otevřete Podrobnosti úlohy:

    ![Neúspěšná úloha Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Všimněte si tlačítka pro opětovné **odeslání** . Po vyřešení problému můžete úlohu znovu odeslat.
5. Podrobnosti o chybě otevřete kliknutím na zvýrazněnou část z předchozího snímku obrazovky.  Vidíte něco podobného:

    ![Podrobnosti o neúspěšné úloze Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Oznamuje vám, že se nenašla zdrojová složka.
6. Klikněte na **Duplikovat skript**.
7. Aktualizujte cestu **z** cesty na:

    /Samples/data/SearchLog.TSV
8. Klikněte na **Odeslat úlohu**.

## <a name="see-also"></a>Viz také:
* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu](data-lake-analytics-get-started-powershell.md)
* [Správa Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md)
