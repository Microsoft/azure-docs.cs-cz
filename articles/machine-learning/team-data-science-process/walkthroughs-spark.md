---
title: Návody pro HDInsight Spark pomocí Pysparkem a Scalou v Azure | Dokumentace Microsoftu
description: Příklady vědecké zpracování týmových dat, které se provedou pomocí Pysparkem a Scalou na Azure HDInsight Spark pro prediktivní analýzy.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
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
ms.openlocfilehash: 2fd6d556e908c47baf3c58c46172c0684b66cdd9
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394246"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Pomocí Pysparkem a Scalou v Azure HDInsight Spark data science návody

Tyto postupy použijte Pysparkem a Scalou v clusteru Spark v Azure pro prediktivní analýzy. Postupujte podle kroků uvedených v vědecké zpracování týmových dat. Přehled vědeckého zpracování týmových dat, naleznete v tématu [vědecké zpracování dat](overview.md). Přehled Spark v HDInsight, naleznete v tématu [Představujeme Spark v HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Další data science návody, které jsou spouštěny vědecké zpracování týmových dat jsou seskupené podle **platformy** , které používají. Zobrazit [názorné postupy vědeckého zpracování týmových dat provádění](walkthroughs.md) pro rozpis tyto příklady.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Předpovídání spropitného pro taxikáře pomocí PySpark ve Sparku v Azure

[Používejte Spark v Azure HDInsight](spark-overview.md) návod používá data z New Yorku taxi předpovědět, jestli jde placenou tip a rozsah částky očekává, že se budou hradit. Používá vědecké zpracování týmových dat ve scénáři pomocí [clusteru Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumat a funkce zpětnou analýzu dat z veřejně dostupné cesty taxíkem NYC a jízdenky datové sady. Toto přehledové téma nastaví vám s clusterem HDInsight Spark a Jupyter PySpark používá ve zbývající části návodu poznámkových bloků. Tyto poznámkové bloky ukazují, jak zkoumat data a jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilými datovými ukazuje, jak zahrnout křížového ověřování, hyperparametrické cílit na konkrétní a vyhodnocení modelů.

### <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování se Sparkem 
Prozkoumejte datovou sadu a vytvořit, stanovení skóre a vyhodnotit modely strojového učení z práce prostřednictvím [vytvoření binární klasifikačních a regresních modelů pro data se sadou nástrojů knihovna Spark MLlib](spark-data-exploration-modeling.md) tématu.

### <a name="model-consumption"></a>Využití modelu
Informace o ke stanovení skóre klasifikačních a regresních modelů vytvořených v tomto tématu najdete v tématu [skóre a vyhodnocení modelů strojového učení předdefinovaných Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Křížové ověření a hyperparameter sweeping
Naleznete v tématu [rozšířené zkoumání a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) na to, jak modely můžete pomocí křížového ověření a hyperparametrické sweeping školení.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Předpovídání spropitného pro taxikáře pomocí Scala ve Sparku v Azure

[Použití Scaly se Sparkem v Azure](scala-walkthrough.md) návod používá data z New Yorku taxi předpovědět, jestli jde placenou tip a rozsah částky očekává, že se budou hradit. Ukazuje, jak pomocí Scala pro technik strojového učení úlohy Spark strojového učení knihovny (MLlib) a ve SparkML balíčky v clusteru Azure HDInsight Spark. Provede vás provedou úlohami, které tvoří [vědecké zpracování dat](http://aka.ms/datascienceprocess): příjem dat a zkoumání, vizualizaci, vytváření funkcí, modelování a modelu využití. Historické modely budou vytvářeny zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury.


## <a name="next-steps"></a>Další postup

Diskuzi o klíčové součásti, které tvoří vědecké zpracování týmových dat, naleznete v tématu [přehled vědeckého zpracování týmových dat](overview.md).

Diskuzi o životní cyklus vědeckého zpracování týmových dat, který vám pomůže strukturovat vaše projekty datových věd, naleznete v tématu [životní cyklus vědeckého zpracování týmových dat](lifecycle.md). Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. 

