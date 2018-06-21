---
title: Řešení potíží s příručky Azure HDInsight | Microsoft Docs
description: Řešení potíží úloh Hadoop pomocí Azure HDInsight. Krok za krokem dokumentace ukazuje, jak používat službu HDInsight k řešení běžných problémů s Hive, Spark, YARN, HBase, HDFS a Storm.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: ''
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: cff2dbca1afc0b37d183b2ea48669eea12b0a9f3
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286744"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Řešení potíží pomocí Azure HDInsight

| Apache zatížení | Horní otázky |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Řešení potíží s HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Jak spouštět sestavy příkaz hbck s několika nepřiřazené oblastí?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Jak se při použití příkazů hbck oblasti přiřazení opravte problémy vypršení časového limitu?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Jak I vynucení zakázání HDFS nouzovém režimu v clusteru?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Jak opravit JDBC nebo SQLLine připojením k problémům s Apache Phoenix?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Co způsobí, že hlavní server nezdaří spustit?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Co způsobí selhání restartování na oblast serveru?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Řešení potíží s HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Jak získám přístup k místní službě HDFS z v clusteru s podporou?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Jak I vynucení zakázání HDFS nouzovém režimu v clusteru?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Řešení potíží s HBase](hdinsight-troubleshoot-hive.md)|<br>[Jak exportovat metaúložiště Hive a importovat na jiný cluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Jak najít protokoly Hive v clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Jak spuštění prostředí Hive s konkrétní konfigurací v clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Jak můžu analyzovat data Tez DAG v clusteru kritické cestě?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Jak lze stáhnout Tez DAG data z clusteru?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Řešení potíží s Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Konfigurování aplikací Spark pomocí Ambari v clusterech](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Konfigurování aplikací Spark v clusterech pomocí poznámkového bloku Jupyter](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Konfigurování aplikací Spark pomocí Livy v clusterech](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Konfigurování odeslání spark aplikace pomocí Spark v clusterech](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Konfigurování aplikací Spark pomocí IntelliJ](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Konfigurování aplikací Spark pomocí Eclipse](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Konfigurování aplikací Spark pomocí VSCode](hdinsight-for-vscode.md)<br><br>[Co způsobí, že Spark výjimka OutOfMemoryError aplikace?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Řešení potíží s Storm](hdinsight-troubleshoot-STORM.md)|<br>[Jak získám přístup k rozhraní Storm v clusteru?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Jak převést Storm událostí hub spout kontrolního bodu informace z jednoho topologie do jiného?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Jak najít binární soubory Storm v clusteru?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Jak je možné zjistit topologii nasazení clusteru Storm?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Jak najdu Storm binární soubory funkcí spout centra událostí pro vývoj](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Jak najdu Storm Log4J konfigurační soubory v clusterech?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Řešení potíží s YARN](hdinsight-troubleshoot-YARN.md)|<br>[Jak vytvořit novou frontu YARN v clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Jak lze stáhnout protokoly YARN z clusteru?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Řešení potíží prostředků služby HDInsight

| Informace o | Naleznete v článcích |
| --- | --- |
| HDInsight na Linuxu a optimalizace | - [Informace o používání HDInsight v Linuxu](hdinsight-hadoop-linux-information.md)<br>- [Paměť Hadoop a řešení potíží s výkonem](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Výkon dotazů Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Protokoly a výpisy | - [Protokoly aplikací Hadoop YARN přístup v systému Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Povolit výpisů paměti haldy pro služby Hadoop v systému Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analýza protokolů HDInsight](hdinsight-debug-jobs.md)|
| Chyby | - [Rady pro pochopení a řešení chyb při WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Opravte chybu OutofMemory nastavení Hive](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Nástroje | - [Použití zobrazení Ambari k ladění úlohách Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Optimalizace dotazů Hive](hdinsight-hadoop-optimize-hive-query.md)<br>- [Nástroje HDInsight IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)<br>- [Nástroje HDInsight Eclipse](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [Nástroje HDInsight VSCode](hdinsight-for-vscode.md)<br>- [Nástroje HDInsight sady Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |
