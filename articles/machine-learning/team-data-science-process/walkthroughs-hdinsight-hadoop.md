---
title: Analýzy Azure HDInsight Hadoop s využitím procesu vědeckého zpracování dat v podregistru
description: Příklady vědeckého procesu týmového zpracování dat, který prochází používáním podregistru v Azure HDInsight Hadoop k provádění prediktivních analýz.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864158"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Návody pro datové vědy HDInsight Hadoop s využitím podregistru v Azure 

Tyto návody používají pro prediktivní analýzu podregistr s clusterem HDInsight Hadoop. Postupuje podle kroků popsaných v rámci vědeckého procesu týmového zpracování dat. Přehled vědeckého zpracování týmových dat najdete v tématu věnovaném [zpracování datových věd](overview.md). Úvod do Azure HDInsight najdete v tématu [Úvod do Azure HDInsight, technologie Hadoop a clustery Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Další návody pro datové vědy, které spouštějí vědecké zpracování týmových dat, jsou seskupeny podle **platformy** , kterou používají. Projděte si [návody, které spouštějí vědecký procesní tým](walkthroughs.md) pro vydaný rozpis těchto příkladů.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Předpověď tipů taxislužby pomocí podregistru v HDInsight Hadoop

Návod [použít clustery HDInsight Hadoop](hive-walkthrough.md) používá pro předpověď data z New York Taxis: 

- Zda je vyplacen Tip 
- Rozdělení částek Tip

Scénář je implementován pomocí podregistru s [Azure HDInsight Hadoopm clusterem](https://azure.microsoft.com/services/hdinsight/). Naučíte se, jak ukládat, zkoumat a přezkoumávat data z veřejně dostupné datové sady taxislužby pro NYC a tarify. K sestavování a nasazování modelů také slouží Azure Machine Learning.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Předpověď inzerce kliknutím na použití podregistru v HDInsight Hadoop

Criteo [clustery s Azure HDInsight Hadoop využitím návodu pro datovou sadu s 1 TB](hive-criteo-walkthrough.md) používá veřejně dostupnou datovou sadu [](https://labs.criteo.com/downloads/download-terabyte-click-logs/) a předpovídá, jestli je Tip placená, a očekávané částky. Scénář je implementován pomocí podregistru s [Azure HDInsight Hadoopm clusterem](https://azure.microsoft.com/services/hdinsight/) pro ukládání, zkoumání, inženýry funkcí a vypínání ukázkových dat. Používá Azure Machine Learning k sestavení, školení a stanovení skóre binárního klasifikačního modelu, který předpovídá, jestli uživatel klikne na reklamu. V tomto návodu se dokončí zobrazení způsobu publikování jednoho z těchto modelů jako webové služby.


## <a name="next-steps"></a>Další kroky

Diskuzi o klíčových součástech, které se skládají z vědeckého zpracování týmových dat, najdete v tématu [Přehled procesu vědeckého zpracování týmových dat](overview.md).

Diskuzi o životním cyklu vědeckého zpracování týmových dat, které můžete použít ke strukturování projektů pro datové vědy, najdete v tématu [životní cyklus procesů týmového zpracování dat](lifecycle.md). Životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění. 

