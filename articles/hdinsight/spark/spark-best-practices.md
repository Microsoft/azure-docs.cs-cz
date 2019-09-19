---
title: Apache Spark osvědčené postupy v Azure HDInsight
description: Naučte se osvědčené postupy pro používání Apache Spark ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106122"
---
# <a name="apache-spark-best-practices"></a>Apache Spark osvědčené postupy

Tento článek poskytuje různé osvědčené postupy pro používání Apache Spark v Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Návody spustit nebo odeslat úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| VSCode | [Použití nástrojů pro podregistr Spark & pro Visual Studio Code](../hdinsight-for-vscode.md) |
| Poznámkové bloky Jupyter | [Kurz: Načtení dat a spuštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Kurz: Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Kurz: Vytvoření aplikace Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin poznámkové bloky | [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark ve službě Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Vzdálená úloha odeslání pomocí Livy | [Odesílání vzdálených úloh do clusteru HDInsight Spark pomocí rozhraní Apache Spark REST API](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Návody monitorovat a ladit úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| Azure Toolkit pro IntelliJ | [Selhání ladění úloh Sparku pomocí Azure Toolkit for IntelliJ (Preview)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit for IntelliJ přes SSH | [Místní nebo vzdálené ladění aplikací Apache Spark v clusteru HDInsight pomocí sady Azure Toolkit for IntelliJ přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit for IntelliJ prostřednictvím sítě VPN | [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně ve službě HDInsight prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Graf úlohy na serveru historie Apache Spark | [Ladění a diagnostika aplikací Apache Spark pomocí rozšířeného serveru historie Apache Sparku](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Návody chcete, aby se moje úlohy Sparku spouštěly efektivněji?

| Možnost | Dokumenty |
|---|---|
| Vstupně-výstupní mezipaměť | [Zvýšení výkonu Apache Spark úloh pomocí Azure HDInsight v/v cache (Preview)](./apache-spark-improve-performance-iocache.md) |
| Možnosti konfigurace | [Optimalizace úloh Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Návody se připojit k jiným službám Azure?

| Možnost | Dokumenty |
|---|---|
| Apache Hive v HDInsight | [Integrace Apache Spark a Apache Hive s konektorem skladu podregistru](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase ve službě HDInsight | [Použití Apache Spark ke čtení a zápisu dat Apache HBA](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka ve službě HDInsight | [Kurz: Použití strukturovaného streamování Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementace architektury lambda na platformě Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Jaké jsou možnosti mého úložiště?

| Možnost | Dokumenty |
|---|---|
| Data Lake Storage Gen2 | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Další kroky

* [Konfigurace nastavení Apache Spark](apache-spark-settings.md)
* [Optimalizace úloh Apache Spark v HDInsight](apache-spark-perf.md)
