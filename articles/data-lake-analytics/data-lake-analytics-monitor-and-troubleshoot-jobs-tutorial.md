---
title: Monitorování úloh v Azure Data Lake Analytics pomocí portálu Azure
description: Tento článek popisuje postup řešení potíží s úlohy Azure Data Lake Analytics pomocí portálu Azure.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8c61b8736dfb13f0c2c2520f22979ac51646e05f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623651"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorování úloh v Azure Data Lake Analytics pomocí portálu Azure

**Chcete-li zobrazit všechny úlohy**

1. Z portálu Azure klikněte na tlačítko **Microsoft Azure** v levém horním rohu.
2. Klikněte na dlaždici s názvem účtu Data Lake Analytics.  Úlohu souhrnu se zobrazí na **úlohy správy** dlaždici.

    ![Správa úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Úlohy správy poskytuje přehled o stavu úlohy. Všimněte si, že je neúspěšnou úlohu.
3. Klikněte **úlohy správy** dlaždice zobrazíte úlohy. Úlohy jsou rozdělené do **systémem**, **zařazeno ve frontě**, a **ukončeno**. Zobrazí se vaše neúspěšnou úlohu v **ukončeno** části. Použije se první z nich v seznamu. Pokud máte mnoho úloh, můžete kliknout na **filtru** můžete vyhledat úlohy.

    ![Filtrujte úlohy Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klikněte na možnost neúspěšné úlohy ze seznamu a otevřete tak podrobnosti úlohy:

    ![Azure Data Lake Analytics se nezdařilo úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Upozornění **odešlete znovu** tlačítko. Po vyřešení problému znovu odešlete úlohu.
5. Klikněte na tlačítko zvýrazněná část na předchozím snímku obrazovky Otevřít podrobnosti o chybě.  Zobrazí se něco podobného jako:

    ![Azure Data Lake Analytics se nezdařilo podrobnosti úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Zde zjistíte, že zdrojová složka nebyla nalezena.
6. Klikněte na tlačítko **duplicitní skriptu**.
7. Aktualizace **FROM** cestu k:

    "/ Samples/Data/SearchLog.tsv"
8. Klikněte na **Odeslat úlohu**.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Správa Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md)
