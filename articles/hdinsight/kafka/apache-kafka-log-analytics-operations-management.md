---
title: Protokoly Azure Monitor pro Apache Kafka – Azure HDInsight
description: Zjistěte, jak pomocí protokolů Azure Monitoru analyzovat protokoly z clusteru Apache Kafka na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471176"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka ve službě HDInsight

Zjistěte, jak pomocí protokolů Azure Monitoru analyzovat protokoly generované Apache Kafka na HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Umístění protokolů

Protokoly Apache Kafka v clusteru jsou umístěny na adrese `/var/log/kafka`. Protokoly Kafka nejsou uloženy nebo trvalé v průběhu životního cyklu clusteru, bez ohledu na to, zda se používají spravované disky. V následující tabulce jsou uvedeny protokoly k dispozici.

|Protokol |Popis |
|---|---|
|kafka.out|stdout a stderr procesu Kafka. V tomto souboru najdete protokoly spuštění a vypnutí Kafka.|
|server.log|Hlavní protokol serveru Kafka. Všechny záznamy makléřů Kafka skončí tady.|
|řadič.log|Řadič protokoly, pokud zprostředkovatel jedná jako správce.|
|statechange.log|Všechny události změny stavu zprostředkovatelům jsou zaznamenány v tomto souboru.|
|kafka-gc.log|Statistiky uvolňování odpadků kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Povolení protokolů Azure Monitor pro Apache Kafka

Kroky k povolení protokolů Azure Monitor pro HDInsight jsou stejné pro všechny clustery HDInsight. Pomocí následujících odkazů porozumíte způsobu vytvoření a konfiguraci požadovaných služeb:

1. Vytvořte pracovní prostor Analýzy protokolů. Další informace najdete v dokumentu [protokoly v Azure Monitoru.](../../azure-monitor/platform/data-platform-logs.md)

2. Vytvořte kafka v clusteru HDInsight. Další informace najdete v [dokumentu Start with Apache Kafka on HDInsight.](apache-kafka-get-started.md)

3. Nakonfigurujte cluster Kafka tak, aby používal protokoly Azure Monitoru. Další informace najdete v [tématu použití protokolů Azure Monitor pro monitorování dokumentu HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Může trvat přibližně 20 minut, než jsou data dostupná pro protokoly Azure Monitoru.

## <a name="query-logs"></a>Protokoly dotazů

1. Na [portálu Azure](https://portal.azure.com)vyberte pracovní prostor Analýzy protokolů.

2. V levé nabídce v části **Obecné**vyberte **Protokoly**. Odtud můžete vyhledávat data shromážděná z Kafky. Zadejte dotaz do okna dotazu a pak vyberte **Spustit**. Níže jsou uvedeny některé příklady vyhledávání:

* Využití disku:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Využití procesoru:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Příchozí zprávy za sekundu: (Nahraďte `your_kafka_cluster_name` názvem clusteru.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Příchozí bajty za sekundu: `wn0-kafka` (Nahrazení názvem hostitele pracovního uzlu.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Odchozí bajty za sekundu: `your_kafka_cluster_name` (Nahraďte názvem clusteru.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Můžete také `*` zadat pro vyhledávání všech přihlášených typů. V současné době jsou pro dotazy k dispozici následující protokoly:

    | Typ protokolu | Popis |
    | ---- | ---- |
    | protokol\_kafkaserver\_CL | Kafka broker server.log |
    | protokol\_kafkacontroller\_CL | Kafka broker controller.log |
    | metriky\_kafka\_CL | Metriky Kafka JMX |

    ![Apache kafka log analytics využití procesoru](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Další kroky

Další informace o Azure Monitoru najdete v [tématu Přehled Azure Monitoru](../../log-analytics/log-analytics-get-started.md)a [Protokoly Sledování Azure dotazu pro monitorování clusterů HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Další informace o práci s Apache Kafka naleznete v následujících dokumentech:

* [Zrcadlení Apache Kafka mezi clustery HDInsight](apache-kafka-mirroring.md)
* [Zvyšte rozsah Apache Kafka na HDInsight](apache-kafka-scalability.md)
* [Používejte apache spark streaming (DStreams) s Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Používejte strukturované streamování Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
