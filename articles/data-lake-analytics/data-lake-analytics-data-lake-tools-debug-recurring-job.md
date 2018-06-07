---
title: Ladění opakované úlohy v Azure Data Lake Analytics
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio k ladění neobvyklé opakování úlohy.
services: data-lake-analytics
author: yanancai
manager: kfile
editor: jasonwhowell
ms.author: yanacai
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 6a181e0cb4014f80673c1bd33e89af69c5677b37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623566"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Řešení potíží s neobvyklé opakování úlohy

Tento článek ukazuje, jak používat [nástrojů Azure Data Lake pro Visual Studio](http://aka.ms/adltoolsvs) k řešení potíží s opakované úlohy. Další informace o kanálu a opakované úlohy z [blog Azure Data Lake a Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Opakované úlohy obvykle sdílet stejný dotaz logiku a podobně jako vstupní data. Představte si například, že máte opakování úlohy spuštěné každé pondělí ráno v 8: 00 Chcete-li počet minulého týdne týdenní aktivního uživatele. Skripty pro tyto úlohy sdílet jeden skript šablonu, která obsahuje logiku dotazu. Vstupy pro tyto úlohy se dat o využití za poslední týden. Sdílení stejné logiky, která dotazu a podobně jako vstup obvykle znamená, že výkon tyto úlohy je podobný a stabilní. Pokud jeden z opakované úlohy najednou provede neobvyklým způsobem, selže nebo zpomaluje mnoho, můžete chtít:

- Zobrazení statistiky hlášení pro předchozí spustí opakované úlohy, které chcete zobrazit, co se stalo.
- Porovnejte neobvyklé úlohy s normální jeden zjistěte, co se změnilo.

**Zobrazení úloh souvisejících s** v nástroje Azure Data Lake pro Visual Studio pomůže urychlit proces řešení potíží s oba případy.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Najít opakované úlohy a otevřete zobrazení souvisejících úloh

Použití zobrazení související úlohy opakované úlohy problém, musíte nejprve najít opakované úlohy v sadě Visual Studio a pak otevřete zobrazení související úlohy.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Případ 1: Máte adresu URL pro opakované úlohy

Prostřednictvím **nástroje** > **Data Lake** > **zobrazení úloh**, můžete vložit adresu URL úlohy otevřete zobrazení úloh v sadě Visual Studio. Vyberte **zobrazit související úlohy** otevření zobrazení související úlohy.

![Propojení zobrazení souvisejících úloh v nástrojů Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Případ 2: Máte kanálu pro opakované úlohy, avšak nikoliv adresu URL

V sadě Visual Studio, můžete otevřít prohlížeč kanálu pomocí Průzkumníka serveru > váš účet Azure Data Lake Analytics > **kanály**. (Pokud tento uzel nemůže najít v Průzkumníku serveru [stáhnout nejnovější modul plug-in](http://aka.ms/adltoolsvs).) 

![Výběrem uzlu kanálů](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

V prohlížeči kanálu jsou uvedeny všechny kanály pro účet Data Lake Analytics na levé straně. Můžete rozbalit kanály najít všechny opakované úlohy a pak vyberte ten, který má potíže s. Otevře se zobrazení souvisejících úloh vpravo.

![Vyberte kanál a otevírání související zobrazení úloh](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: Analýza sestavy statistik

Souhrn a sestavy statistik se zobrazí v horní části zobrazení související úlohy. Zde můžete najít potenciální původní příčině problému. 

1.  V sestavě ukazuje osy x čas odeslání úlohy. Umožňuje vám najít úlohu neobvyklé.
2.  Ke kontrole statistiky a získat informace o problému a možná řešení pomocí procesu v následujícím diagramu.

![Diagram procesu pro kontrole statistiky](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: Porovnání na normální úlohy neobvyklé úlohy

Můžete najít všechny předat opakované úlohy prostřednictvím seznam úloh v dolní části zobrazení související úlohy. Najít další statistiky a možná řešení, klikněte pravým tlačítkem na úlohu neobvyklé. Pomocí zobrazení úlohy rozdílové můžete porovnat neobvyklé úlohy s normální předchozímu.

![Místní nabídky pro porovnání úlohy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Věnujte pozornost velké rozdíly mezi tyto dvě úlohy. Tyto rozdíly pravděpodobně způsobují problémy s výkonem. Pokud chcete zkontrolovat další, použijte kroky v následujícím diagramu:

![Diagram procesu kontroly rozdíly mezi úlohami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Další postup

* [Řešení problémů zkosení dat](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Ladění uživatelem definované C# – kód pro selhání úloh U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
