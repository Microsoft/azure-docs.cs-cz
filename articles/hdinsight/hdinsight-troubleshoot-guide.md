---
title: Průvodci odstraňováním potíží Azure HDInsight
description: Řešení potíží s Azure HDInsight. V podrobné dokumentaci se dozvíte, jak pomocí HDInsight řešit běžné problémy s Apache Hive, Apache Spark, Apache NITĚmi, Apache Hbas, HDFS a Apache Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 5ffd688a60f793ffa497cd0d23ed88437e56a07c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289024"
---
# <a name="troubleshoot-azure-hdinsight"></a>Řešení potíží s Azure HDInsight

| Úlohy Apache | Časté dotazy |
|---|---|
|![ikona ikony HBA pro adaptéry Apache v HDInsight](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Řešení potíží s Apache HBase]()|<br>[Nepřiřazené oblasti](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Vypršení časových limitů při použití příkazu hbase hbck ve službě Azure HDInsight](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Problémy s připojením Apache Phoenix ve službě Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[Co způsobuje neúspěšné spuštění hlavního serveru?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException – adresa se už používá.](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![Ikona pro HDInsight Apache HDFS ikona](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Řešení potíží Apache Hadoop HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Návody přístup k místnímu HDFS v rámci clusteru?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Místní HDFS se zablokoval v bezpečném režimu v clusteru Azure HDInsight.](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![ikona ikony pro podregistr Apache v HDInsight](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[Řešení potíží s Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Návody exportovat metastore Hive a naimportovat ho na jiný cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Návody v clusteru najít protokoly Apache Hive?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Návody spustit prostředí Apache Hive s konkrétní konfigurací v clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Návody analyzovat Apache Tez DAG data na cestě kritické pro cluster?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Návody stáhnout Apache Tez DAG data z clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![ikona ikony Apache Sparku HDInsight](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Řešení potíží s Apache Spark](./spark/apache-troubleshoot-spark.md)|<br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Ambari?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí poznámkového bloku Jupyter?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí Apache Livy?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Jak na clusterech nakonfigurovat aplikaci Apache Spark pomocí příkazu spark-submit?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Návody nakonfigurovat Apache Spark aplikaci pomocí IntelliJ?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Návody nakonfigurovat Apache Spark aplikaci pomocí zatmění?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Návody nakonfigurovat Apache Spark aplikaci pomocí VSCode?](hdinsight-for-vscode.md)<br><br>[Výjimka OutOfMemoryError pro Apache Spark](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![Ikona pro HDInsight Apache – ikona ikony](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Řešení potíží s Apache Storm](./storm/apache-troubleshoot-storm.md)|<br>[Návody přístup k uživatelskému rozhraní Apache Storm v clusteru?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Návody přenést informace o kontrolním bodu centra událostí Apache Storm z jedné topologie do druhé?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Návody najít binární soubory s více podmnožinami v clusteru?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Návody určit topologii nasazení clusteru se zaplavou?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Návody pro vývoj najít Apache Storm binární soubory centra událostí Spout?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![ikona ikony s VLÁKNem pro HDInsight v Apache](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Řešení potíží s Apache Hadoop NITĚmi](hdinsight-troubleshoot-YARN.md)|<br>[Návody vytvořit novou Apache Hadoopovou frontu PŘÍZ v clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Návody stahovat Apache Hadoop PŘÍZové protokoly z clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Prostředky pro řešení potíží HDInsight

| Pro informace o | Přečtěte si tyto články |
| --- | --- |
| HDInsight v systému Linux a optimalizace | - [Informace o použití HDInsight v systému Linux](hdinsight-hadoop-linux-information.md)<br>- [Řešení potíží s Apache Hadoop paměti a výkonu](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive výkon dotazů](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Protokoly a výpisy | - [Přístup k protokolům aplikace Apache Hadoop nitě v systému Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Povolit výpisy haldy pro Apache Hadoop služby v systému Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| Chyby | - [Pochopení a vyřešení chyb WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Nastavení Apache Hive k opravě chyby OutofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| nástroje | - [Optimalizace Apache Hive dotazů](hdinsight-hadoop-optimize-hive-query.md)<br>- [Nástroj HDInsight IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)<br>- [Nástroj HDInsight zatmění](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [Nástroj HDInsight VSCode](hdinsight-for-vscode.md)<br>- [Nástroj HDInsight Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
