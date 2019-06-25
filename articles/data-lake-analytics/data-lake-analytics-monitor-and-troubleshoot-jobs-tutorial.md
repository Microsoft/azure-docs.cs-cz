---
title: Monitorování úloh v Azure Data Lake Analytics pomocí webu Azure portal
description: Tento článek popisuje postup řešení potíží s úlohami Azure Data Lake Analytics pomocí webu Azure portal.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 40864bab068659be016161f7dc40243ebbd45174
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812571"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Monitorování úloh v Azure Data Lake Analytics pomocí webu Azure Portal

**Pokud chcete zobrazit všechny úlohy**

1. Na webu Azure Portal, klikněte na tlačítko **Microsoft Azure** v levém horním rohu.
2. Klikněte na dlaždici s názvem účtu Data Lake Analytics.  V souhrnu úlohy se zobrazí na **Správa úloh** dlaždici.

    ![Správa úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Úlohy správy poskytuje přehled o stavu úlohy. Všimněte si, že je neúspěšnou úlohu.
3. Klikněte na tlačítko **Správa úloh** dlaždici zobrazíte úlohy. Úlohy, které jsou rozdělené do **systémem**, **ve frontě**, a **ukončeno**. Měly by se zobrazit neúspěšné úlohy v **ukončeno** oddílu. Musí být první z nich v seznamu. Pokud máte velké množství úloh, můžete kliknout na **filtr** můžete vyhledat úlohy.

    ![Filtrování úloh Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klikněte na úlohu ze seznamu zobrazíte podrobnosti úlohy:

    ![Azure Data Lake Analytics se nepovedlo úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Všimněte si, že **znovu odeslat** tlačítko. Po vyřešení problému, můžete neúspěšné úlohy.
5. Klikněte na část zvýrazněný na předchozím snímku obrazovky, chcete-li otevřít podrobnosti o chybě.  Měly by se zobrazit něco jako:

    ![Azure Data Lake Analytics se nezdařilo podrobnosti úlohy](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Zjistíte, že zdrojová složka nebyla nalezena.
6. Klikněte na tlačítko **duplicitní skript**.
7. Aktualizace **FROM** cestu k:

    "/Samples/Data/SearchLog.tsv"
8. Klikněte na **Odeslat úlohu**.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Azure Data Lake Analytics pomocí Azure Powershellu](data-lake-analytics-get-started-powershell.md)
* [Správa Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md)
