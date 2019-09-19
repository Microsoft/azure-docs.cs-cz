---
title: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight
description: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight pro Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 57ba285b7de34dd548128b1f58644a32e153d056
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087164"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scénář: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o spuštění aktivity Sparku v kanálu Azure Data Factory se zobrazí následující výjimka:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Příčina

Pokud soubor JAR aplikace není umístěný ve výchozím/primárním úložišti clusteru Spark, úloha Sparku se nezdaří.

Jedná se o známý problém s rozhraním open source pro Spark sledovaným v této chybě: [Pokud je v FS. defaultFS a v JAR aplikace jiná adresa URL, úloha Sparku se nezdařila](https://issues.apache.org/jira/browse/SPARK-22587).

Tento problém byl vyřešen ve Spark 2.3.0.

## <a name="resolution"></a>Řešení

Zajistěte, aby byl jar aplikace uložený ve výchozím a primárním úložišti pro cluster HDInsight. V případě Azure Data Factory se ujistěte, že je propojená služba ADF na výchozím kontejneru HDInsight, nikoli v sekundárním kontejneru.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
