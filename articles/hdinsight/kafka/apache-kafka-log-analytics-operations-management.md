---
title: Protokoly Azure Monitor pro Apache Kafka – Azure HDInsight
description: Naučte se používat protokoly Azure Monitor k analýze protokolů z Apache Kafka clusteru v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: d4263b8b338f057893c9dfcda1541fc338c2577f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894264"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka ve službě HDInsight

Naučte se používat protokoly Azure Monitor k analýze protokolů vygenerovaných Apache Kafka v HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Povolit protokoly Azure Monitor pro Apache Kafka

Postup povolení protokolů Azure Monitor pro HDInsight je stejný pro všechny clustery HDInsight. Pomocí následujících odkazů můžete pochopit, jak vytvořit a nakonfigurovat požadované služby:

1. Vytvořte pracovní prostor Log Analytics. Další informace najdete v části [protokoly v dokumentu Azure monitor](../../azure-monitor/platform/data-platform-logs.md) .

2. Vytvořte Kafka v clusteru HDInsight. Další informace najdete v dokumentu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) .

3. Nakonfigurujte cluster Kafka, aby používal protokoly Azure Monitor. Další informace najdete v tématu [použití protokolů Azure monitor k monitorování dokumentu HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Než budou data k dispozici pro Azure Monitor protokoly, může to trvat přibližně 20 minut.

## <a name="query-logs"></a>Protokoly dotazů

1. V [Azure Portal](https://portal.azure.com)vyberte pracovní prostor Log Analytics.

2. V nabídce vlevo v části **Obecné**vyberte **protokoly**. Odtud můžete vyhledávat data shromážděná z Kafka. V okně dotazu zadejte dotaz a pak vyberte **Spustit**. Následuje několik příkladů hledání:

* Využití disku:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Využití CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Příchozí zprávy za sekundu: (nahraďte `your_kafka_cluster_name` názvem vašeho clusteru.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Příchozí bajty za sekundu: (nahraďte `wn0-kafka` názvem hostitele uzlu pracovního procesu.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Odchozí bajty za sekundu: (nahraďte `your_kafka_cluster_name` názvem vašeho clusteru.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Můžete také zadat `*` k prohledávání všech protokolovaných typů. V současné době jsou k dispozici následující protokoly pro dotazy:

    | Typ protokolu | Popis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server služby Kafka Broker. log |
    | log\_kafkacontroller\_CL | Protokol Kafka Broker Controller. log |
    | metriky\_Kafka\_CL | Kafka JMX metriky |

    ![Apache Kafka – využití procesoru Log Analytics](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Monitor najdete v tématech [přehled Azure monitor](../../log-analytics/log-analytics-get-started.md)a [dotazy Azure monitor protokoly pro monitorování clusterů HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Další informace o práci s Apache Kafka najdete v následujících dokumentech:

* [Zrcadlení Apache Kafka mezi clustery HDInsight](apache-kafka-mirroring.md)
* [Zvýšení škálování Apache Kafka v HDInsight](apache-kafka-scalability.md)
* [Použití streamování Apache Spark (DStreams) s Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Použití strukturovaného streamování Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
