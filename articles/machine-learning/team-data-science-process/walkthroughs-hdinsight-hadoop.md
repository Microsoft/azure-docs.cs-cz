---
title: Používání Hive v Azure HDInsight Hadoop data vědecké účely návody | Microsoft Docs
description: Příklady procesu Team dat vědecké účely, které provede pomocí Hive v Azure HDInsight Hadoop pro prediktivní analýzy.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 8b7eead12b6546ad86f6ff0aa48f4754c00a4734
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838751"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Používání Hive v Azure HDInsight Hadoop data vědecké účely návody 

Tyto postupy použijte Hive s clusteru HDInsight Hadoop pro prediktivní analýzy. Jejich postupujte podle kroků uvedených v procesu Team dat vědecké účely. Přehled procesu Team dat vědecké účely, najdete v tématu [proces vědecké účely dat](overview.md). Úvod do Azure HDInsight, naleznete v části [Úvod do Azure HDInsight, technologie zásobníku Hadoop a clustery systému Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Návody vědecké účely další data, které se spouští proces vědecké účely Team dat jsou seskupené podle **platformy** , které používají. V tématu [návody spouštění procesu tým datové vědy](walkthroughs.md) pro rozpis těchto příkladech.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Předpověď taxíkem tipy pro používání Hive s HDInsight Hadoop

[Clusterů systému HDInsight Hadoop pomocí](hive-walkthrough.md) návod používá data z New Yorku taxislužby k předvídání: 

- Jestli je placené tip 
- Rozdělení objemu tipu

Tento scénář je implementovaná pomocí Hive se [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Zjistíte, jak k uložení prozkoumat, a funkce pracovníka data z veřejně dostupné NYC taxi služební cestě a tarif datovou sadu. Je také použít k vytváření a nasazování modely Azure Machine Learning.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Předpověď klikne na oznámení o inzerovaném programu používání Hive s HDInsight Hadoop

[Použití Azure HDInsight Hadoop clusterů v sadě dat 1 TB](hive-criteo-walkthrough.md) návod používá veřejně dostupné [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) klikněte na datovou sadu, která předvídat, jestli je tip placené a rozsah objemy očekává. Tento scénář je implementovaná pomocí Hive se [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k uložení, prozkoumejte, funkci pracovníka a dolů ukázková data. Azure Machine Learning používá pro vytváření, trénování a určení skóre modelu binární klasifikace predikci, zda uživatel klikne na oznámení o inzerovaném programu. Průvodce se ukončí znázorňující k publikování některého z těchto modelů jako webovou službu.


## <a name="next-steps"></a>Další postup

Informace součástí klíče, které tvoří proces Team dat. vědecké účely, naleznete v [přehled tým datové vědy procesu](overview.md).

Informace životního cyklu Team datové vědy procesu, můžete použít pro struktury projekty vědecké účely dat, naleznete v [procesu vědecké účely Team datového cyklu](lifecycle.md). Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. 

