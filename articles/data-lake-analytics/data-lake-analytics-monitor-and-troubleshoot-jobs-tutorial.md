---
title: Monitorování Azure Data Lake Analytics – Azure Portal
description: Tento článek popisuje, jak použít Azure Portal k řešení potíží s úlohami Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: 08e14765b2b70f1e2567bd6a9cd2d67ee9aed83b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121278"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorování úloh v Azure Data Lake Analytics pomocí webu Azure Portal

## <a name="to-see-all-the-jobs"></a>Zobrazení všech úloh

1. V Azure Portal klikněte na **Microsoft Azure** v levém horním rohu.

2. Klikněte na dlaždici s názvem účtu Data Lake Analytics.  Souhrn úlohy se zobrazí na dlaždici **Správa úloh** .

   ![Správa úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Správa úloh vám poskytne přehled o stavu úlohy. Všimněte si, že úloha není úspěšná.
3. Kliknutím na dlaždici **Správa úloh** Zobrazte úlohy. Úlohy jsou zařazené do kategorií **spuštěno**, **zařazeno do fronty**a **skončilo**. Vaše úloha, která selhala, se zobrazí v části **ukončeno** . První z nich je v seznamu. Když máte spoustu úloh, můžete kliknout na **Filtr** a pomůžou vám najít úlohy.

   ![Azure Data Lake Analytics filtrovat úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Kliknutím na neúspěšnou úlohu ze seznamu otevřete Podrobnosti úlohy:

   ![Neúspěšná úloha Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Všimněte si tlačítka pro opětovné **odeslání** . Po vyřešení problému můžete úlohu znovu odeslat.

5. Podrobnosti o chybě otevřete kliknutím na zvýrazněnou část z předchozího snímku obrazovky.  Vidíte něco podobného:

   ![Podrobnosti o neúspěšné úloze Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   Oznamuje vám, že se nenašla zdrojová složka.

6. Klikněte na **Duplikovat skript**.

7. Aktualizujte cestu **z** cesty na:`/Samples/Data/SearchLog.tsv`

8. Klikněte na **Odeslat úlohu**.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu](data-lake-analytics-get-started-powershell.md)
* [Správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md)
