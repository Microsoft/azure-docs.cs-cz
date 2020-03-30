---
title: Analýza na Azure HDInsight Hadoop pomocí Hive – proces vědecké analýzy týmových dat
description: Příklady procesu vědecké analýzy týmových dat, které procházejí používáním Hive v Azure HDInsight Hadoop k prediktivní analýze.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864158"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Návody pro datové vědy HDInsight Hadoop pomocí Hive v Azure 

Tyto návody používají Hive s clusterem HDInsight Hadoop k prediktivní analýze. Postupujte podle kroků uvedených v procesu vědecké vědy o týmových datech. Přehled procesu vědecké ho spoje týmových dat naleznete v tématu [Proces datové vědy](overview.md). Úvod do Azure HDInsightu najdete [v tématu Úvod do Azure HDInsight, zásobníku technologie Hadoop a clusterů Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Další datové vědy návody, které provádějí proces vědecké správy týmových dat jsou seskupeny podle **platformy,** které používají. Viz [návody provádění procesu vědecké ho týmových dat](walkthroughs.md) pro rozepsání těchto příkladů.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Předvídejte tipy na taxi pomocí Hive s HDInsight Hadoop

Návod [k použití clusterů HDInsight Hadoop](hive-walkthrough.md) používá data z newyorských taxíků k předvídání: 

- Zda je spropitné zaplaceno 
- Rozdělení částek spropitného

Scénář se implementuje pomocí Hive s [clusterem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Dozvíte se, jak ukládat, prozkoumávat a funkce inženýr data z veřejně dostupné nyc taxi výlet a tarif datové sady. Azure Machine Learning můžete také použít k sestavení a nasazení modelů.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Předvídejte kliknutí na reklamu pomocí Hive s HDInsight Hadoop

[Použití clusterů Azure HDInsight Hadoop v návodu](hive-criteo-walkthrough.md) k datové sadě 1 TB používá veřejně dostupnou datovou sadu [kliknutí Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) k předvídání, zda se platí tip a očekávané částky. Scénář se implementuje pomocí Hive s [clusterem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pro ukládání, zkoumání, inženýr funkcí a ukázková data dolů. Používá Azure Machine Learning k sestavení, trénování a skóre binární klasifikační model předpovídání, zda uživatel klikne na reklamu. Návod uzavírá ukazuje, jak publikovat jeden z těchto modelů jako webové služby.


## <a name="next-steps"></a>Další kroky

Pro diskusi o klíčových komponent, které tvoří proces vědecké vědy o týmových datech, naleznete [v přehledu vědeckého procesu týmových dat](overview.md).

Pro diskusi o životní cyklus procesu týmových dat, který můžete použít ke strukturování projektů datové vědy, najdete [v tématu Team Data Science Process lifecycle](lifecycle.md). Životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění. 

