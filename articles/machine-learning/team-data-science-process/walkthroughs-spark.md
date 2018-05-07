---
title: Návody pro HDInsight Spark v Azure pomocí PySpark a Scala | Microsoft Docs
description: Příklady procesu Team dat vědecké účely, které provede prostřednictvím PySpark a Scala v Azure HDInsight Spark pro prediktivní analýzy.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 06498f55b38be98441ddceb8f1667c89d257f8fd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Návody vědecké účely dat HDInsight Spark v Azure pomocí PySpark a Scala

Tyto postupy použijte PySpark a Scala v clusteru Azure Spark pro prediktivní analýzy. Jejich postupujte podle kroků uvedených v procesu Team dat vědecké účely. Přehled procesu Team dat vědecké účely, najdete v tématu [proces vědecké účely dat](overview.md). Přehled Spark v HDInsight, naleznete v části [Úvod do Spark v HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Návody vědecké účely další data, které se spouští proces vědecké účely Team dat jsou seskupené podle **platformy** , které používají. V tématu [návody spouštění procesu tým datové vědy](walkthroughs.md) pro rozpis těchto příkladech.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Předpověď taxíkem tipy pomocí PySpark na Azure Spark

[Používejte Spark v Azure HDInsight](spark-overview.md) návod používá data z New Yorku taxislužby předpovědět, zda je placené tip a rozsah objemy očekává zaplacení. Používá proces Team dat. vědecké účely scénář pomocí [clusteru Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) uložit, prozkoumat a funkce pracovníka data z veřejně dostupné cestě taxíkem NYC a jízdenky datovou sadu. Toto přehledové téma sad, které si s clusteru HDInsight Spark a Jupyter PySpark poznámkových bloků použít ve zbývající části průvodce. Tyto poznámkových bloků ukazují, jak prozkoumat vaše data a jak vytvářet a využívat modelů. Poznámkový blok pro zkoumání a modelování pokročilé data ukazuje, jak křížového ověřování, technologie hyper parametr komínů, zahrnout a vyhodnocení modelu.

### <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání dat a modelování pomocí Spark 
Prozkoumejte datovou sadu a vytvářet, stanovení skóre a vyhodnotit strojového učení modely projdete [vytvořit binární klasifikace a regrese modely dat pomocí Spark MLlib sady toolkit](spark-data-exploration-modeling.md) tématu.

### <a name="model-consumption"></a>Spotřeba modelu
Informace o skóre pro klasifikaci a regrese modely, které jsou vytvořené v tomto tématu najdete v tématu [skóre a vyhodnocení modelů learning vytvořené Spark počítač](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Křížové ověření a hyperparameter (vymetání) komínů
V tématu [Advanced zkoumání dat a modelování pomocí Spark](spark-advanced-data-exploration-modeling.md) na tom, jak může být modely Trénink pomocí sweeping křížové ověření a technologie hyper parametr.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Předpověď taxíkem tipy pomocí na Azure Spark Scala

[Pomocí Scala pomocí Spark v Azure](scala-walkthrough.md) návod používá data z New Yorku taxislužby předpovědět, zda je placené tip a rozsah objemy očekává zaplacení. Ukazuje, jak používat Scala pro pod dohledem strojového učení úlohy s Spark strojového učení knihovny (MLlib) a balíčky SparkML v clusteru Azure HDInsight Spark. Provede vás provedou úlohami, které tvoří [proces vědecké účely dat](http://aka.ms/datascienceprocess): přijímání dat a zkoumání, vizualizace, funkce analýzy, modelování a spotřeba modelu. Modely vytvářeny zahrnují logistic a lineární regrese, náhodné doménové struktury a přechodu boosted stromy.


## <a name="next-steps"></a>Další postup

Informace součástí klíče, které tvoří proces Team dat. vědecké účely, naleznete v [přehled tým datové vědy procesu](overview.md).

Informace životního cyklu Team datové vědy procesu, můžete použít pro struktury projekty vědecké účely dat, naleznete v [procesu vědecké účely Team datového cyklu](lifecycle.md). Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. 

