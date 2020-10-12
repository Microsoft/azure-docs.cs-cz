---
title: Protokoly Azure Monitor pro Apache Kafka – Azure HDInsight
description: Naučte se používat protokoly Azure Monitor k analýze protokolů z Apache Kafka clusteru v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 593d6861ee5913fffb25bfdea4829e1b1ce6ddc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087397"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka ve službě HDInsight

Naučte se používat protokoly Azure Monitor k analýze protokolů vygenerovaných Apache Kafka v HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Umístění protokolů

Protokoly Apache Kafka v clusteru se nacházejí v umístění `/var/log/kafka` . Protokoly Kafka se neukládají ani neukládají mezi životním cyklům clusteru bez ohledu na to, jestli se používají spravované disky. V následující tabulce jsou uvedeny dostupné protokoly.

|Protokol |Description |
|---|---|
|Kafka. out|stdout a stderr procesu Kafka V tomto souboru najdete protokoly spouštění a vypínání Kafka.|
|Server. log|Hlavní protokol serveru Kafka Všechny protokoly služby Kafka Broker tady zakončí.|
|Controller. log|Kontroler protokolů, pokud zprostředkovatel funguje jako kontroler.|
|StateChange. log|Do tohoto souboru se zaznamenávají všechny události změny stavu ke zprostředkovatelům.|
|Kafka-GC. log|Kafka shromažďování paměti.|

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

    Můžete také zadat `*` pro prohledávání všech protokolovaných typů. V současné době jsou k dispozici následující protokoly pro dotazy:

    | Typ protokolu | Description |
    | ---- | ---- |
    | protokol \_ kafkaserver \_ CL | Server služby Kafka Broker. log |
    | protokol \_ kafkacontroller \_ CL | Protokol Kafka Broker Controller. log |
    | metriky \_ Kafka \_ CL | Kafka JMX metriky |

    ![Apache Kafka – využití procesoru Log Analytics](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Monitor najdete v tématech [přehled Azure monitor](../../log-analytics/log-analytics-get-started.md)a [dotazy Azure monitor protokoly pro monitorování clusterů HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Další informace o práci s Apache Kafka najdete v následujících dokumentech:

* [Zrcadlení Apache Kafka mezi clustery HDInsight](apache-kafka-mirroring.md)
* [Zvýšení škálování Apache Kafka v HDInsight](apache-kafka-scalability.md)
* [Použití streamování Apache Spark (DStreams) s Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Použití strukturovaného streamování Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
