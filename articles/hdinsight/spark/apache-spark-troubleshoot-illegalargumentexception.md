---
title: IllegalArgumentExceptioná chyba pro Apache Spark – Azure HDInsight
description: IllegalArgumentException pro aktivitu Apache Spark v Azure HDInsight pro Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 429659d605cdaf8aad978841e486a17da321cce4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929400"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scénář: IllegalArgumentException pro aktivitu Apache Spark ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o spuštění aktivity Sparku v kanálu Azure Data Factory se zobrazí následující výjimka:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Příčina

Pokud soubor JAR aplikace není umístěný ve výchozím/primárním úložišti clusteru Spark, úloha Sparku se nezdaří.

Jedná se o známý problém s rozhraním open source v Sparku, které je sledováno v této chybě: [Pokud FS. defaultFS a JAR aplikace jsou jinou adresou URL, úloha Spark se nezdařila](https://issues.apache.org/jira/browse/SPARK-22587).

Tento problém byl vyřešen ve Spark 2.3.0.

## <a name="resolution"></a>Řešení

Zajistěte, aby byl jar aplikace uložený ve výchozím a primárním úložišti pro cluster HDInsight. V případě Azure Data Factory se ujistěte, že je propojená služba ADF na výchozím kontejneru HDInsight, nikoli v sekundárním kontejneru.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]