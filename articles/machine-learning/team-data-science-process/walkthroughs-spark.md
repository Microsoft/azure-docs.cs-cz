---
title: Analytics v prostředí HDInsight Spark s PySpark, Scala - týmu vědecké zpracování dat
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
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864141"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Pomocí Pysparkem a Scalou v Azure HDInsight Spark data science návody

Tyto postupy použijte Pysparkem a Scalou v clusteru Spark v Azure pro prediktivní analýzy. Postupujte podle kroků uvedených v vědecké zpracování týmových dat. Přehled vědeckého zpracování týmových dat, naleznete v tématu [vědecké zpracování dat](overview.md). Přehled Spark v HDInsight, naleznete v tématu [Představujeme Spark v HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Další data science návody, které jsou spouštěny vědecké zpracování týmových dat jsou seskupené podle **platformy** , které používají. Zobrazit [názorné postupy vědeckého zpracování týmových dat provádění](walkthroughs.md) pro rozpis tyto příklady.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Předpovídání spropitného pro taxikáře pomocí PySpark ve Sparku v Azure

Pomocí nástroje New York taxislužby data předpovídá návod [Použití Sparku v Azure HDInsight](spark-overview.md) , jestli je Tip vyplacený, a rozsah očekávaných částek. V tomto příkladu se používá vědecký proces týmových dat ve scénáři, který používá [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumávání a zpracovávání dat funkcí z veřejně dostupné datové sady služby NYC taxislužby TRIPS a jízdné. Toto přehledové téma používá cluster HDInsight Spark a poznámkové bloky Jupyter PySpark. Tyto poznámkové bloky ukazují, jak zkoumat data a jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilými datovými ukazuje, jak zahrnout křížového ověřování, hyperparametrické cílit na konkrétní a vyhodnocení modelů.

### <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování se Sparkem 
Prozkoumejte datovou sadu a vytvořit, stanovení skóre a vyhodnotit modely strojového učení z práce prostřednictvím [vytvoření binární klasifikačních a regresních modelů pro data se sadou nástrojů knihovna Spark MLlib](spark-data-exploration-modeling.md) tématu.

### <a name="model-consumption"></a>Využití modelu
Informace o ke stanovení skóre klasifikačních a regresních modelů vytvořených v tomto tématu najdete v tématu [skóre a vyhodnocení modelů strojového učení předdefinovaných Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Křížové ověření a hyperparameter sweeping
Naleznete v tématu [rozšířené zkoumání a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) na to, jak modely můžete pomocí křížového ověření a hyperparametrické sweeping školení.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Předpovídání spropitného pro taxikáře pomocí Scala ve Sparku v Azure

Návod [použít Scala s Sparkem v Azure](scala-walkthrough.md) předpovídá, jestli je Tip placená, a rozsah částek, které mají být placené. Ukazuje, jak pomocí Scala pro technik strojového učení úlohy Spark strojového učení knihovny (MLlib) a ve SparkML balíčky v clusteru Azure HDInsight Spark. Provede vás provedou úlohami, které tvoří [vědecké zpracování dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): příjem dat a zkoumání, vizualizaci, vytváření funkcí, modelování a modelu využití. Historické modely budou vytvářeny zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury.


## <a name="next-steps"></a>Další kroky

Přehled vědeckého zpracování týmových dat najdete v tématu [Přehled procesu vědeckého zpracování](overview.md)týmových dat.

Diskuzi o životním cyklu vědeckého zpracování týmových dat najdete v tématu [životní cyklus procesu vědeckého zpracování dat týmu](lifecycle.md). Tento životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění. 

