---
title: Ladění opakujících se úloh v Azure Data Lake Analytics
description: Naučte se, jak pomocí Nástroje Azure Data Lake pro Visual Studio ladit neobvyklou opakovanou úlohu.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: 86d5134e257d2dae642eceb933a78047773b25a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87129946"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Řešení potíží s neobvykle se opakující úlohou

Tento článek popisuje, jak pomocí [nástroje Azure Data Lake pro Visual Studio](https://aka.ms/adltoolsvs) řešit problémy s opakovanými úlohami. Přečtěte si další informace o kanálu a opakujících se úlohách na [blogu Azure Data Lake a Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Opakující se úlohy obvykle sdílejí stejnou logiku dotazu a podobná vstupní data. Představte si například, že máte opakovanou úlohu, která se spouští každé pondělí ráno v 8 dop. pro výpočet týdenního aktivního uživatele v minulém týdnu. Skripty pro tyto úlohy sdílejí jednu šablonu skriptu obsahující logiku dotazu. Vstupy pro tyto úlohy jsou data o využití za poslední týden. Sdílení stejné logiky dotazů a podobného vstupu obvykle znamená, že výkon těchto úloh je podobný a stabilní. Pokud se jedna z vašich opakovaných úloh náhle vykoná neobvykle, dojde k chybě nebo zpomalí velkou spoustu, možná budete chtít:

- Další informace o tom, co se stalo, najdete v sestavách statistiky pro předchozí spuštění opakované úlohy.
- Pokud chcete zjistit, co se změnilo, porovnejte neobvyklou úlohu s normálním výskytem.

**Zobrazení související úlohy** v nástroje Azure Data Lake pro Visual Studio pomáhá urychlit průběh odstraňování potíží s oběma případy.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Krok 1: Vyhledání opakujících se úloh a otevření souvisejícího zobrazení úlohy

Chcete-li použít související zobrazení úlohy k řešení problému opakované úlohy, je třeba nejprve najít opakující se úlohu v aplikaci Visual Studio a následně otevřít související zobrazení úlohy.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Případ 1: máte adresu URL pro opakovanou úlohu.

Pomocí **nástrojů**  >  **Data Lake**  >  **zobrazení úloh**můžete vložit adresu URL úlohy pro otevření zobrazení úlohy v aplikaci Visual Studio. Vyberte **Zobrazit související úlohy** a otevřete zobrazení související úlohy.

![Zobrazit odkaz související úlohy v Data Lake Analyticsch nástrojích](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Případ 2: máte kanál pro opakovanou úlohu, ale ne adresu URL.

V aplikaci Visual Studio můžete otevřít Prohlížeč kanálů prostřednictvím Průzkumník serveru >te Azure Data Lake Analytics účtu > **kanály**. (Pokud tento uzel nemůžete najít v Průzkumník serveru, [Stáhněte si nejnovější modul plug-in](https://aka.ms/adltoolsvs).) 

![Výběr uzlu kanály](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

V prohlížeči kanálu jsou všechny kanály pro Data Lake Analytics účet uvedeny vlevo. Můžete rozbalit kanály a vyhledat všechny opakované úlohy a pak vybrat tu, která obsahuje problémy. Otevře se související zobrazení úlohy vpravo.

![Výběr kanálu a otevření souvisejícího zobrazení úlohy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Krok 2: analýza statistické sestavy

Souhrn a sestava statistiky se zobrazí v horní části zobrazení související úlohy. Zde můžete najít potenciální hlavní příčinu problému. 

1.  V sestavě se na ose X zobrazuje čas odeslání úlohy. Použijte ho k nalezení neobvyklé úlohy.
2.  Pomocí postupu v následujícím diagramu můžete kontrolovat statistiku a získat přehled o problému a možných řešeních.

![Diagram procesu pro kontrolu statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Krok 3: porovnání neobvyklé úlohy s normální úlohou

Všechny odeslané opakující se úlohy můžete najít v seznamu úloh v dolní části souvisejícího zobrazení úlohy. Pokud chcete najít další přehledy a potenciální řešení, klikněte pravým tlačítkem myši na neobvyklou úlohu. Pomocí zobrazení rozdílů úloh můžete porovnat neobvyklou úlohu s předchozí normální.

![Místní nabídka pro porovnání úloh](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Věnujte pozornost velkým rozdílům mezi těmito dvěma úlohami. Tyto rozdíly pravděpodobně způsobují problémy s výkonem. K další kontrole použijte postup v následujícím diagramu:

![Diagram procesu pro kontrolu rozdílů mezi úlohami](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Další kroky

* [Řešení problémů s pozkosením dat](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Ladit uživatelsky definovaný kód C# pro nezdařené úlohy U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
