---
title: Analýza na HDInsight Spark s PySpark, Scala - Proces vědecké ho služky
description: Příklady procesu vědecké analýzy týmových dat, které procházejí používáním PySparku a Scaly na Azure HDInsight Spark.
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
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864141"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Návody pro datové vědy HDInsight Spark využívající PyStoři a Scalu v Azure

Tyto návody používají PySpark a Scala v clusteru Azure Spark k prediktivní analýze. Postupujte podle kroků uvedených v procesu vědecké vědy o týmových datech. Přehled procesu vědecké ho spoje týmových dat naleznete v tématu [Proces datové vědy](overview.md). Přehled Sparka na HDInsightu najdete [v tématu Úvod do Sparku na HDInsightu](../../hdinsight/spark/apache-spark-overview.md).

Další datové vědy návody, které provádějí proces vědecké správy týmových dat jsou seskupeny podle **platformy,** které používají. Viz [návody provádění procesu vědecké ho týmových dat](walkthroughs.md) pro rozepsání těchto příkladů.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Předvídejte tipy pro taxi pomocí PyStoři na Azure Sparku

Pomocí dat taxi v New Yorku, [použití Spark na Azure HDInsight](spark-overview.md) návod předpovídá, zda tip se platí a rozsah očekávaných částek. Tento příklad používá proces vědecké analýzy týmových dat v případě použití [clusteru Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) k ukládání, zkoumání a inženýrfunkce data z veřejně dostupné nyc taxi výlet a tarif datové sady. Toto téma přehledu používá cluster HDInsight Spark a poznámkové bloky Jupyter PySpark. Tyto poznámkové bloky vám ukážou, jak prozkoumat data a potom, jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilých dat ukazuje, jak zahrnout křížové ověřování, hyperparametrické zametání a vyhodnocení modelu.

### <a name="data-exploration-and-modeling-with-spark"></a>Průzkum dat a modelování se Sparkem 
Prozkoumejte datovou sadu a vytvořte, vyhodnoťte a vyhodnoťte modely strojového učení pomocí [vytváření binárních klasifikačních a regresních modelů pro data pomocí tématu sady nástrojů Spark MLlib.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Spotřeba modelu
Informace o tom, jak skóre klasifikace a regresní modely vytvořené v tomto tématu, najdete v [tématu skóre a vyhodnotit Spark-postavený modely strojového učení](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Křížové ověřování a hyperparametrické zametání
Podívejte se [na pokročilé zkoumání dat a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) o tom, jak lze modely trénovat pomocí křížového ověřování a hyperparametrické zametání.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Předvídejte tipy pro taxi pomocí Scaly na Azure Spark

[Návod Použití Scaly se Sparkem v Azure](scala-walkthrough.md) předpovídá, jestli se platí tip a očekává se, že se zaplatí. Ukazuje, jak používat Scala pro úkoly strojového učení pod dohledem s knihovnou strojového učení Spark (MLlib) a Balíčky SparkML v clusteru Azure HDInsight Spark. Provede vás úkoly, které tvoří [proces datové vědy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestování a zkoumání dat, vizualizace, inženýrské funkce, modelování a spotřeba modelu. Vytvořené modely zahrnují logistickou a lineární regresi, náhodné doménové struktury a stromy posílené přechodem.


## <a name="next-steps"></a>Další kroky

Přehled procesu vědecké ho svědecké hospo- procesu týmových dat najdete v [tématu Přehled vědeckého procesu týmových dat](overview.md).

Pro diskusi o životní cyklus procesu vědecké ho spoje v oblasti týmových dat, najdete [v tématu Team Data Science Process lifecycle](lifecycle.md). Tento životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění. 

