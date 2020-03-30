---
title: Průvodce odstraňováním potíží s Azure HDInsight
description: Poradce při potížích s úlohami Apache Hadoop pomocí Azure HDInsight. Podrobné dokumenty ukazují, jak pomocí HDInsight řešit běžné problémy s Apache Hive, Apache Spark, Apache YARN, Apache HBase, HDFS a Apache Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 4460c24bb742bff72af47954363b1051f5d9b43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895277"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Poradce při potížích s použitím Azure HDInsight

| Apache pracovní zátěž | Časté dotazy |
|---|---|
|![ikona ikony hdinsight apache HBase](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Řešení potíží s Apache HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Nepřiřazené oblasti](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Vypršení časových limitů při použití příkazu hbase hbck ve službě Azure HDInsight](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Problémy s připojením Apache Phoenix v Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[Co způsobí, že se hlavní server nespustí?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException - Adresa, která je již používána](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![ikona ikony hdinsight apache hdfs](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Poradce při potížích s Apache Hadoop HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Jak lze získat přístup k místnímu systému HDFS z clusteru?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Místní HDFS uvízl v nouzovém režimu v clusteru Azure HDInsight](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![ikona ikony hdinsight apache Hive](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[Poradce při potížích s Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Jak exportuji metastore Hive a importuji ho do jiného clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Jak najdu protokoly Apache Hive v clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Jak spusťte shell Apache Hive s konkrétními konfiguracemi v clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Jak lze analyzovat data Apache Tez DAG na cestě kritické pro cluster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Jak lze stáhnout data Apache Tez DAG z clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![ikona ikony hdinsight apache Spark](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Poradce při potížích s Apache Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Ambari?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí poznámkového bloku Jupyter?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Livy?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí příkazu spark-submit?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Jak nakonfiguruji aplikaci Apache Spark pomocí IntelliJ?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Jak nakonfiguruji aplikaci Apache Spark pomocí eclipse?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Jak nakonfiguruji aplikaci Apache Spark pomocí VSCode?](hdinsight-for-vscode.md)<br><br>[Výjimka OutOfMemoryError pro Apache Spark](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![ikona ikony hdinsight apache Storm](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Poradce při potížích Apache Storm](hdinsight-troubleshoot-STORM.md)|<br>[Jak se dostanu k uzlině Apache Storm v clusteru?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Jak přenesu informace o kontrolním bodu centra událostí Apache Storm z jedné topologie do druhé?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Jak lze v clusteru vyhledat binární soubory Storm?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Jak zjistím topologii nasazení clusteru Storm?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Jak najdu binární soubory hubů apache storm pro vývoj?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![ikona ikony hdinsight apache YARN](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Poradce při potížích Apache Hadoop YARN](hdinsight-troubleshoot-YARN.md)|<br>[Jak vytvořím novou frontu Apache Hadoop YARN v clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Jak lze stáhnout protokoly Apache Hadoop YARN z clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Zdroje informací o řešení potíží s hdinsightem

| Informace o | Přečtěte si tyto články |
| --- | --- |
| HDInsight na Linuxu a optimalizace | - [Informace o používání HDInsight na Linuxu](hdinsight-hadoop-linux-information.md)<br>- [Řešení potíží s pamětí apache hadoop a výkonem](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Výkon dotazu Apache Hive](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Protokoly a výpisy | - [Přístup k protokolům aplikací Apache Hadoop YARN na Linuxu](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Povolit výpisy haldy pro služby Apache Hadoop na Linuxu](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analýza protokolů HDInsight](hdinsight-debug-jobs.md)|
| chyby | - [Pochopení a řešení chyb WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Nastavení Apache Hive pro opravu chyby outofmemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Nástroje | - [Optimalizace dotazů Apache Hive](hdinsight-hadoop-optimize-hive-query.md)<br>- [Nástroj HDInsight IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)<br>- [Nástroj HDInsight Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [Nástroj HDInsight VSCode](hdinsight-for-vscode.md)<br>- [Nástroj HDInsight Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).