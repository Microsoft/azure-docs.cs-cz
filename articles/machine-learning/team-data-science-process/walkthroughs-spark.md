---
title: Analýzy v HDInsight Spark s PySpark, Scala – týmových datových vědeckých procesů
description: Příklady vědeckého procesu týmového zpracování dat, který prochází používáním PySpark a Scala na Azure HDInsight Spark.
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
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320137"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Návody pro datové vědy pro HDInsight Spark využívající PySpark a Scala v Azure

Tyto návody používají PySpark a Scala v clusteru Azure Spark k provádění prediktivních analýz. Postupuje podle kroků popsaných v rámci vědeckého procesu týmového zpracování dat. Přehled vědeckého zpracování týmových dat najdete v tématu věnovaném [zpracování datových věd](overview.md). Přehled Sparku ve službě HDInsight najdete v tématu [Úvod do Sparku v HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Další návody pro datové vědy, které spouštějí vědecké zpracování týmových dat, jsou seskupeny podle **platformy** , kterou používají. Projděte si [návody, které spouštějí vědecký procesní tým](walkthroughs.md) pro vydaný rozpis těchto příkladů.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Předpověď tipů taxislužby pomocí PySpark ve službě Azure Spark

Pomocí nástroje New York taxislužby data předpovídá návod [Použití Sparku v Azure HDInsight](spark-overview.md) , jestli je Tip vyplacený, a rozsah očekávaných částek. V tomto příkladu se používá vědecký proces týmových dat ve scénáři, který používá [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumávání a zpracovávání dat funkcí z veřejně dostupné datové sady služby NYC taxislužby TRIPS a jízdné. Toto přehledové téma používá cluster HDInsight Spark a poznámkové bloky Jupyter PySpark. Tyto poznámkové bloky ukazují, jak prozkoumat data a jak vytvářet a spotřebovávat modely. Poznámkový blok pokročilého zkoumání a modelování dat ukazuje, jak zahrnout křížové ověřování, navzájemování a vyhodnocování modelů.

### <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování dat pomocí Sparku 
Prozkoumejte datovou sadu a vytvářejte, myslete a vyhodnoťte modely strojového učení pomocí tématu [Vytvoření binární klasifikace a regresní modely pro data pomocí sady nástrojů Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

### <a name="model-consumption"></a>Spotřeba modelu
Informace o tom, jak určit skóre modelů klasifikace a regrese vytvořených v tomto tématu, najdete v tématu [skóre a vyhodnocení modelů strojového učení](spark-model-consumption.md)s využitím Sparku.

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Křížové ověřování a nakroužení parametrů
Podívejte [se na téma pokročilé zkoumání a modelování dat pomocí Sparku](spark-advanced-data-exploration-modeling.md) , jak je možné modely vyškolené pomocí křížového ověřování a s využitím Hyper-parametring.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Předpověď tipů taxislužby pomocí Scala ve službě Azure Spark

Návod [použít Scala s Sparkem v Azure](scala-walkthrough.md) předpovídá, jestli je Tip placená, a rozsah částek, které mají být placené. Ukazuje, jak používat Scala pro dohled nad úkoly strojového učení s balíčky Spark Machine Learning Library (MLlib) a SparkML v clusteru Azure HDInsight Spark. Provede vás úkoly, které tvoří [proces vědeckého zpracování dat](./index.yml): přijímání a zkoumání dat, vizualizace, strojírenství funkcí, modelování a využití modelu. Sestavené modely zahrnují logistické a lineární regrese, náhodné doménové struktury a barevné zesílené stromy.


## <a name="next-steps"></a>Další kroky

Přehled vědeckého zpracování týmových dat najdete v tématu [Přehled procesu vědeckého zpracování](overview.md)týmových dat.

Diskuzi o životním cyklu vědeckého zpracování týmových dat najdete v tématu [životní cyklus procesu vědeckého zpracování dat týmu](lifecycle.md). Tento životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění.