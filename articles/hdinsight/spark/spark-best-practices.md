---
title: Osvědčené postupy Apache Spark na Azure HDInsight
description: Seznamte se s doporučenými postupy pro používání Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106122"
---
# <a name="apache-spark-best-practices"></a>Osvědčené postupy Apache Spark

Tento článek obsahuje různé osvědčené postupy pro používání Apache Spark na Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Jak spustím nebo odešlu úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| VSCode | [Použití nástrojů Spark & Hive pro kód sady Visual Studio](../hdinsight-for-vscode.md) |
| Poznámkové bloky Jupyter | [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Kurz: Pomocí sady Nástrojů Azure pro IntelliJ vytvořte aplikace Apache Spark pro cluster HDInsight.](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Kurz: Vytvoření aplikace Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ](./apache-spark-create-standalone-application.md) |
| Poznámkové bloky Zeppelin | [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark ve službě Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Vzdálené odesílání úloh s Livy | [Odesílání vzdálených úloh do clusteru HDInsight Spark pomocí rozhraní Apache Spark REST API](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Jak můžu monitorovat a ladit úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| Azure Toolkit for IntelliJ | [Ladění úlohy jiskry selhání pomocí sady Azure Toolkit pro IntelliJ (preview)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit pro IntelliJ přes SSH | [Místní nebo vzdálené ladění aplikací Apache Spark v clusteru HDInsight pomocí sady Azure Toolkit for IntelliJ přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit pro IntelliJ přes VPN | [Použití sady Azure Toolkit for IntelliJ k vzdálenému ladění aplikací Apache Spark v HDInsightu prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Graf úloh na serveru Historie Apache Spark | [Ladění a diagnostika aplikací Apache Spark pomocí rozšířeného serveru historie Apache Sparku](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Jak můžu zefektivnit svoje úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| Mezipaměť vi | [Zlepšete výkon úloh Apache Spark pomocí azure hdinsight ové mezipaměti (preview)](./apache-spark-improve-performance-iocache.md) |
| Možnosti konfigurace | [Optimalizace úloh Apache Spark](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Jak se připojím k jiným službám Azure?

| Možnost | Dokumenty |
|---|---|
| Apache Hive na HDInsight | [Integrace Apache Spark a Apache Hive s konektorem Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase ve službě HDInsight | [Použití Apache Sparku ke čtení a zápisu dat Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka na HDInsight | [Kurz: Použití strukturovaného streamování Apache Sparku se systémem Apache Kafka ve službě HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementace architektury lambda na platformě Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Jaké mám možnosti úložiště?

| Možnost | Dokumenty |
|---|---|
| Data Lake Storage Gen2 | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Použití data lake storage gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Další kroky

* [Konfigurace nastavení Apache Sparku](apache-spark-settings.md)
* [Optimalizace pracovních míst Apache Spark v HDInsightu](apache-spark-perf.md)
