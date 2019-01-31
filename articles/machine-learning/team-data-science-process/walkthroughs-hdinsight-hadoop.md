---
title: Analýzy v Azure HDInsight Hadoop pomocí Hivu - vědecké zpracování týmových dat
description: Příklady vědecké zpracování týmových dat, které se provedou pomocí Hivu v Azure HDInsight Hadoop pro prediktivní analýzy.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8db11759ce501929e5cfd7b721c5e9dc80f9e01a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473183"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Pomocí Hivu v Azure HDInsight Hadoop data science návody 

Tyto kurzy použití Hivu s clusterem HDInsight Hadoop pro prediktivní analýzy. Postupujte podle kroků uvedených v vědecké zpracování týmových dat. Přehled vědeckého zpracování týmových dat, naleznete v tématu [vědecké zpracování dat](overview.md). Úvod do Azure HDInsight, naleznete v tématu [Úvod do služby Azure HDInsight, technologie hadoop a clustery systému Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Další data science návody, které jsou spouštěny vědecké zpracování týmových dat jsou seskupené podle **platformy** , které používají. Zobrazit [názorné postupy vědeckého zpracování týmových dat provádění](walkthroughs.md) pro rozpis tyto příklady.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Předpovídání spropitného pro taxikáře používání Hive s HDInsight Hadoopem

[Clusterů systému HDInsight Hadoop použijte](hive-walkthrough.md) návod používá data z New Yorku taxi předpovědět: 

- Určuje, zda je placené tip 
- Distribuce částky tip

Tento scénář je implementována pomocí Hivu s [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Zjistíte, jak ukládat, prozkoumejte a funkce zpětnou analýzu dat z veřejně dostupné NYC taxi cesty a tarif datové sady. Můžete také používat Azure Machine Learning pro sestavování a nasazování modelů.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Předpověď kliknutí oznámení o inzerovaném programu pomocí Hivu se službou HDInsight Hadoop

[Použití Azure HDInsight Hadoop clusterů v datové sadě 1 TB](hive-criteo-walkthrough.md) názorný postup využívá veřejně dostupné [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) klikněte na datovou sadu, která předpovědět, jestli jde placenou tip a rozsah částky očekávání. Tento scénář je implementována pomocí Hivu s [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumejte, funkce technik a dolů ukázková data. Využívá Azure Machine Learning k vytváření, trénování a stanovíte jeho skóre binární klasifikační model predikci, jestli uživatel klikne na reklamu. Dojde k závěru návodu ukazuje, jak publikování některého z těchto modelů jako webové služby.


## <a name="next-steps"></a>Další postup

Diskuzi o klíčové součásti, které tvoří vědecké zpracování týmových dat, naleznete v tématu [přehled vědeckého zpracování týmových dat](overview.md).

Diskuzi o životní cyklus vědeckého zpracování týmových dat, který vám pomůže strukturovat vaše projekty datových věd, naleznete v tématu [životní cyklus vědeckého zpracování týmových dat](lifecycle.md). Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. 

