---
title: Ladění opakujících se úloh v Azure Data Lake Analytics
description: Další informace o použití Azure Data Lake Tools pro Visual Studio pro ladění neobvykle se opakující úlohou.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260036"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Řešení potíží s neobvykle se opakující úlohou

Tento článek popisuje, jak používat [Azure Data Lake Tools pro Visual Studio](https://aka.ms/adltoolsvs) k řešení problémů s opakující se úlohy. Další informace o kanálu a opakované úlohy ze [blogu Azure Data Lake a Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Opakující se úlohy obvykle sdílet stejnou logiku dotazu a podobně jako vstupní data. Představte si například, že máte opakovaná úloha spuštěná v 8: 00 ráno každé pondělí ke zjištění poslední týden týdenní aktivního uživatele. Skripty pro tyto úlohy sdílet jednu šablonu skript, který obsahuje logiku dotazu. Vstupy pro tyto úlohy jsou data o využití za poslední týden. Sdílet stejnou logiku dotazu a podobně jako vstup obvykle znamená, že je výkon těchto úloh podobné a stabilní. Pokud jeden z vašich opakující se úlohy náhle neobvyklým způsobem, provede se nezdaří, nebo může zpomalit velké, můžete chtít:

- Zobrazit sestavy statistik pro předchozích spuštění opakované úlohy, které chcete zobrazit, co se stalo.
- Porovnání neobvyklé úlohy s normální tím zjistit, co se změnilo.

**Související zobrazení úloh** v Azure Data Lake Tools pro Visual Studio vám pomáhá zrychlit postup řešení potíží s obou případech.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Vyhledání opakující se úlohy a otevřete zobrazení související úlohy

Použití zobrazení související úlohy k vyřešení problému opakované úlohy, musíte napřed zjistěte opakované úlohy v sadě Visual Studio a otevřete zobrazení související úlohy.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Případ 1: Máte adresu URL pro opakovaná úloha

Prostřednictvím **nástroje** > **Data Lake** > **zobrazení úloh**, můžete vložit adresu URL úlohy a otevřete zobrazení úloh v sadě Visual Studio. Vyberte **zobrazit související úlohy** otevřete zobrazení související úlohy.

![Odkaz zobrazení související úlohy v nástrojích Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Případ 2: Máte kanál pro opakované úlohy, ale nikoliv adresu URL

V sadě Visual Studio, můžete otevřít Prohlížeč kanálů prostřednictvím Průzkumníka serveru > váš účet Azure Data Lake Analytics > **kanály**. (Pokud tento uzel nejde najít v Průzkumníku serveru [stáhněte si nejnovější modul plug-in](https://aka.ms/adltoolsvs).) 

![Při vybrání uzlu kanály](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

V prohlížeči kanálu jsou uvedeny všechny kanály pro účet Data Lake Analytics na levé straně. Můžete rozbalit kanály pro vyhledání všech opakovaných úloh a vyberte ten, který má problémy. Vpravo se otevře zobrazení související úlohy.

![Výběr kanálu a otevřete zobrazení související úlohy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analýza sestavy statistik

Souhrn a statistiky sestavy se zobrazují v horní části zobrazení související úlohy. Tam si můžete najít hlavní příčinu problému. 

1.  Osu x v sestavě, ukazuje čas odeslání úlohy. Můžete najít neobvyklé úlohu.
2.  Zkontrolujte statistiky a získávat přehledy o problému a možná řešení pomocí procesu v následujícím diagramu.

![Diagram procesu kontroly statistiky](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porovnání neobvyklé úlohy s normální úlohou

Zjistíte, že všechna odeslání opakující se úlohy prostřednictvím seznamu úloh v dolní části zobrazení související úlohy. Najít další přehledy a možná řešení, kliknete pravým tlačítkem na neobvyklé úlohy. Pomocí úlohy rozdílové zobrazení můžete porovnat neobvyklé úlohy s předchozím histogramem normální.

![Místní nabídku pro porovnání úloh](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Věnujte pozornost velké rozdíly mezi tyto dvě úlohy. Tyto rozdíly jsou pravděpodobně způsobí problémy s výkonem. Zkontrolujte dále, postupujte podle kroků v následujícím diagramu:

![Diagram procesu kontroly rozdíly mezi úlohami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Další postup

* [Vyřešit problémy – Nerovnoměrná distribuce dat](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Ladění uživatelského kódu C# pro selhání úloh U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
