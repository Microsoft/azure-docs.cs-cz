---
title: Pokyny pro Apache Spark v Azure HDInsight
description: Přečtěte si pokyny pro použití Apache Spark ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 3818ce9ebae8e6af6ee8bb5ed30c7ab446a6f6b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855170"
---
# <a name="apache-spark-guidelines"></a>Pokyny pro Apache Spark

Tento článek poskytuje různé pokyny k používání Apache Spark v Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Návody spustit nebo odeslat úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| VSCode | [Použití nástrojů pro podregistr Spark & pro Visual Studio Code](../hdinsight-for-vscode.md) |
| Poznámkové bloky Jupyter | [Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Kurz: použití Azure Toolkit for IntelliJ k vytvoření Apache Sparkch aplikací pro cluster HDInsight](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Kurz: Vytvoření aplikace Scala Maven pro Apache Spark v HDInsight pomocí IntelliJ](./apache-spark-create-standalone-application.md) |
| Poznámkové bloky Zeppelin | [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark ve službě Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Vzdálená úloha odeslání pomocí Livy | [Odesílání vzdálených úloh do clusteru HDInsight Spark pomocí rozhraní Apache Spark REST API](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie je pracovní postup a systém koordinace, který spravuje úlohy systému Hadoop.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|Livy můžete použít ke spouštění interaktivních prostředí Spark nebo k odeslání dávkových úloh na Spark.|
|[Azure Data Factory pro Apache Spark](../../data-factory/transform-data-using-spark.md)|Aktivita Spark v kanálu Data Factory spouští program Spark na vlastní nebo [clusteru HDInsight na vyžádání.|
|[Azure Data Factory pro Apache Hive](../../data-factory/transform-data-using-hadoop-hive.md)|Aktivita podregistru HDInsight v kanálu Data Factory spouští dotazy na podregistry na vašem clusteru HDInsight nebo na vyžádání.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Návody monitorovat a ladit úlohy Spark?

| Možnost | Dokumenty |
|---|---|
| Sada Azure Toolkit for IntelliJ | [Selhání ladění úloh Sparku pomocí Azure Toolkit for IntelliJ (Preview)](apache-spark-intellij-tool-failure-debug.md) |
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
| Apache Hive ve službě HDInsight | [Integrace Apache Spark a Apache Hive s konektorem skladu podregistru](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase ve službě HDInsight | [Použití Apache Sparku ke čtení a zápisu dat Apache HBase](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka ve službě HDInsight | [Kurz: Použití strukturovaného streamování Apache Sparku se systémem Apache Kafka ve službě HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: implementace architektury lambda na platformě Azure](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Jaké jsou možnosti mého úložiště?

| Možnost | Dokumenty |
|---|---|
| Azure Data Lake Storage Gen2 | [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Použití Azure Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Použití úložiště Azure s clustery Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Další kroky

* [Konfigurace nastavení Apache Sparku](apache-spark-settings.md)
* [Optimalizace úloh Apache Spark v HDInsight](apache-spark-perf.md)
