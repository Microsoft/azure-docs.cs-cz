---
title: Ladění opakovaných úloh v Azure Data Lake Analytics
description: Zjistěte, jak pomocí nástrojů Datového jezera Azure pro Visual Studio ladit abnormální opakované úlohy.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629710"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Řešení potíží s neobvykle se opakující úlohou

Tento článek ukazuje, jak používat [Nástroje datového jezera Azure pro Visual Studio](https://aka.ms/adltoolsvs) k řešení problémů s opakovanými úlohami. Další informace o kanálu a opakovaných úlohách najdete na [blogu Azure Data Lake a Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Opakované úlohy obvykle sdílejí stejnou logiku dotazu a podobná vstupní data. Představte si například, že máte opakovanou úlohu spuštěnou každé pondělí ráno v 8 hodin ráno. pro počítání týdenního aktivního uživatele z minulého týdne. Skripty pro tyto úlohy sdílejí jednu šablonu skriptu, která obsahuje logiku dotazu. Vstupy pro tyto úlohy jsou údaje o využití za minulý týden. Sdílení stejné logiky dotazu a podobný vstup obvykle znamená, že výkon těchto úloh je podobné a stabilní. Pokud jedna z vašich opakovaných úloh náhle provádí abnormálně, selže nebo zpomaluje hodně, možná budete chtít:

- Podívejte se na statistické sestavy pro předchozí spuštění opakované úlohy a zjistěte, co se stalo.
- Porovnejte abnormální práci s normální, abyste zjistili, co se změnilo.

**Související zobrazení úloh** v nástrojích Azure Data Lake tools for Visual Studio vám pomůže urychlit průběh řešení potíží s oběma případy.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Vyhledání opakovaných úloh a otevření souvisejícího zobrazení úlohy

Chcete-li použít související zobrazení úloh k řešení potíží s opakovanými úlohami, musíte nejprve najít opakovanou úlohu v sadě Visual Studio a potom otevřít související zobrazení úloh.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Případ 1: Máte adresu URL opakované úlohy

Pomocí **nástroje** > **Data Lake** > **Zobrazení úlohy**můžete vložit adresu URL úlohy pro otevření zobrazení úlohy v sadě Visual Studio. Vyberte **Zobrazit související úlohy,** chcete-li otevřít související zobrazení úloh.

![Zobrazit odkaz Související úlohy v nástrojích Analýzy datového jezera](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Případ 2: Máte potrubí pro opakované úlohy, ale ne URL

V sadě Visual Studio můžete otevřít prohlížeč kanálu prostřednictvím Průzkumníka serveru > účtu Azure Data Lake Analytics > **kanály**. (Pokud tento uzel v Průzkumníkovi serveru nemůžete najít, [stáhněte si nejnovější modul plug-in](https://aka.ms/adltoolsvs).) 

![Výběr uzlu Potrubí](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

V prohlížeči kanálů jsou všechny kanály pro účet Data Lake Analytics uvedeny vlevo. Můžete rozbalit kanály a najít všechny opakované úlohy a pak vybrat ten, který má problémy. Související zobrazení úloh se otevře vpravo.

![Výběr kanálu a otevření souvisejícího zobrazení úloh](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analýza statistické sestavy

Souhrn a statistika sestavy jsou zobrazeny v horní části související zobrazení úloh. Zde můžete najít potenciální hlavní příčinu problému. 

1.  V sestavě ukazuje osa X čas odeslání úlohy. Použij to k nalezení té abnormální práce.
2.  Pomocí procesu v následujícím diagramu zkontrolujte statistiky a získejte přehled o problému a možných řešeních.

![Diagram procesu pro kontrolu statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porovnání abnormální úlohy s normální úlohou

Všechny odeslané opakované úlohy najdete v seznamu úloh v dolní části zobrazení související úlohy. Chcete-li najít další poznatky a potenciální řešení, klikněte pravým tlačítkem myši na neobvyklou úlohu. Pomocí zobrazení Rozdíl úlohy můžete porovnat abnormální úlohu s předchozí normální úlohou.

![Místní nabídka pro porovnání úloh](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Dávejte pozor na velké rozdíly mezi těmito dvěma pracovními místy. Tyto rozdíly jsou pravděpodobně příčinou problémů s výkonem. Chcete-li zkontrolovat další, postupujte podle kroků v následujícím diagramu:

![Diagram procesu pro kontrolu rozdílů mezi úlohami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Další kroky

* [Řešení problémů se zkosením dat](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Ladění uživatelem definovaného kódu Jazyka C pro neúspěšné úlohy U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
