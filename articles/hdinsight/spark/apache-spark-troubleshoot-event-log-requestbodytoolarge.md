---
title: Chyba RequestBodyTooLarge z aplikace Apache Spark App – Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge se zobrazí v protokolu pro aplikaci streamování Apache Spark ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 2d2e929335f6af2ee24a81e719d9d0d899f7b8ef
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241836"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "se zobrazuje v protokolu aplikací streamování aplikace Apache Spark v HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Chyba: `NativeAzureFileSystem ... RequestBodyTooLarge` se zobrazí v protokolu ovladače aplikace streamování Apache Spark.

## <a name="cause"></a>Příčina

Váš soubor protokolu událostí Spark pravděpodobně zasáhne omezení délky souboru pro WASB.

V Spark 2,3 každá aplikace Spark generuje jeden soubor protokolu událostí Spark. Soubor protokolu událostí Spark pro aplikaci pro streamování Sparku se v době, kdy je aplikace spuštěná, dál zvětšuje. V současné době má soubor v WASB limit bloku 50000 a výchozí velikost bloku je 4 MB. Proto je ve výchozí konfiguraci maximální velikost souboru 195 GB. Služba Azure Storage ale zvýšila maximální velikost bloku na 100 MB, což efektivně zavedlo omezení jednoho souboru na 4,75 TB. Další informace najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Rozlišení

K dispozici jsou tři řešení pro tuto chybu:

* Zvětšete velikost bloku na až 100 MB. V uživatelském rozhraní Ambari upravte konfigurační vlastnost HDFS `fs.azure.write.request.size` (nebo ji vytvořte v `Custom core-site` části). Nastavte vlastnost na větší hodnotu, například: 33554432. Uložte aktualizovanou konfiguraci a restartujte příslušné součásti.

* Pravidelně zastavte a znovu odešlete úlohu Spark-streamování.

* Pomocí HDFS můžete ukládat protokoly událostí Spark. Použití HDFS pro úložiště může způsobit ztrátu dat událostí Spark během škálování clusteru nebo upgrady Azure.

    1. Provádění změn `spark.eventlog.dir` a `spark.history.fs.logDirectory` prostřednictvím uživatelského rozhraní Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Vytvořit adresáře na HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Všechny ovlivněné služby restartujte přes uživatelské rozhraní Ambari.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
