---
title: Chyba IllegalArgumentException pro Apache Spark – Azure HDInsight
description: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight pro Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894383"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scénář: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o spuštění aktivity Spark v kanálu Azure Data Factory se zobrazí následující výjimka:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Příčina

Úloha Spark se nezdaří, pokud soubor jar aplikace není umístěn ve výchozím/primárním úložišti clusteru Spark.

Jedná se o známý problém s open-source framework Spark sledovány v této chybě: [Spark úloha selže, pokud fs.defaultFS a aplikace jar jsou různé url](https://issues.apache.org/jira/browse/SPARK-22587).

Tento problém byl vyřešen v Sparku 2.3.0.

## <a name="resolution"></a>Řešení

Ujistěte se, že nádoba aplikace je uložena ve výchozím/primárním úložišti pro cluster HDInsight. V případě Azure Data Factory, ujistěte se, že propojená služba ADF je odkazována na výchozí kontejner HDInsight spíše než sekundární kontejner.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
