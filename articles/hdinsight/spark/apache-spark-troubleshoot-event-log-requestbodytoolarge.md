---
title: RequestBodyTooLarge chyba z aplikace Apache Spark - Azure HDInsight
description: NativníAzureFileSystem ... RequestBodyTooLarge se zobrazí v protokolu pro streamovací aplikaci Apache Spark v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894405"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativníAzureFileSystem... RequestBodyTooLarge" se objeví v apache spark streaming app log v HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Chyba: `NativeAzureFileSystem ... RequestBodyTooLarge` Zobrazí se v protokolu ovladačů pro streamovací aplikaci Apache Spark.

## <a name="cause"></a>Příčina

Soubor protokolu událostí Spark pravděpodobně dosahuje limitu délky souboru pro WASB.

Ve Sparku 2.3 každá aplikace Spark generuje jeden soubor protokolu událostí Spark. Soubor protokolu událostí Spark pro streamovací aplikaci Spark se stále zvětšuje, když je aplikace spuštěná. Dnes soubor na WASB má limit bloku 50000 a výchozí velikost bloku je 4 MB. Takže ve výchozí konfiguraci je maximální velikost souboru 195 GB. Azure Storage však zvýšil maximální velikost bloku na 100 MB, což efektivně přineslo limit jednoho souboru na 4,75 TB. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Řešení

Pro tuto chybu jsou k dispozici tři řešení:

* Zvětšete velikost bloku až na 100 MB. V uzdu Ambari upravte `fs.azure.write.request.size` vlastnost konfigurace `Custom core-site` HDFS (nebo ji vytvořte v sekci). Nastavte vlastnost na větší hodnotu, například: 33554432. Uložte aktualizovanou konfiguraci a restartujte ohrožené součásti.

* Pravidelně zastavte a znovu odešlete úlohu streamování jiskry.

* Pomocí HDFS uložte protokoly událostí Spark. Použití HDFS pro úložiště může mít za následek ztrátu dat událostí Spark během škálování clusteru nebo upgradů Azure.

    1. Proveďte `spark.eventlog.dir` změny `spark.history.fs.logDirectory` v a prostřednictvím ui Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Vytváření adresářů na HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Restartujte všechny ovlivněné služby prostřednictvím ui.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
