---
title: Protokoly Azure monitoru pro Apache Kafka – Azure HDInsight
description: Zjistěte, jak používat Azure Monitor protokoly k analýze protokolů z cluster Apache Kafka v Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 1e141aea3b22bfdcb981513f03e595b6c2f15466
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147970"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka v HDInsight

Zjistěte, jak používat Azure Monitor protokoly k analýze protokolů generovaných Apache Kafka v HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Povolit protokoly Azure monitoru pro Apache Kafka

Postup povolení protokolů Azure Monitor pro HDInsight je stejný pro všechny clustery HDInsight. Chcete-li pochopit, jak vytvořit a nakonfigurovat požadované služby použijte následující odkazy:

1. Vytvoření pracovního prostoru Log Analytics. Další informace najdete v tématu [protokolů ve službě Azure Monitor](../../azure-monitor/platform/data-platform-logs.md) dokumentu.

2. Vytvořte systém Kafka na clusteru HDInsight. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurace clusteru Kafka používat protokoly Azure monitoru. Další informace najdete v tématu [protokoly použití Azure Monitor k monitorování HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

> [!IMPORTANT]  
> Může trvat přibližně 20 minut, než se data jsou k dispozici pro protokoly Azure monitoru.

## <a name="query-logs"></a>Protokoly dotazů

1. Z [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor Log Analytics.

2. V levé nabídce v části **Obecné**vyberte **protokoly**. Z tohoto místa můžete prohledávat data shromážděná z Kafka. V okně dotazu zadejte dotaz a pak vyberte **spustit**. Tady jsou některé příklady hledání:

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

* Příchozí zprávy za sekundu:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Příchozí bajty za sekundu:

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Odchozí bajty za sekundu:

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    > [!IMPORTANT]  
    > Nahraďte hodnoty dotazu clusteru konkrétní informace. Například `ClusterName_s` musí být nastavena na název vašeho clusteru. `HostName_s` musí být nastavena na název domény pracovního uzlu v clusteru.
    
    Můžete také zadat `*` k vyhledání všech typů přihlášení. Tyto protokoly jsou aktuálně k dispozici pro dotazy:
    
    | Typ protokolu | Popis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log zprostředkovatele Kafka |
    | log\_kafkacontroller\_CL | Controller.log zprostředkovatele Kafka |
    | metrics\_kafka\_CL | Kafka JMX metrics |
    
    ![Obrázek hledání využití procesoru](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Další postup

Další informace o monitorování Azure, najdete v části [Přehled monitorování Azure](../../log-analytics/log-analytics-get-started.md), a [zaznamená dotaz Azure Monitor k monitorování clusterů HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Další informace o práci s využitím Apache Kafka najdete v následujících dokumentech:

* [Apache Kafka zrcadlení mezi clustery HDInsight](apache-kafka-mirroring.md)
* [Zvýšení škálovatelnosti systému Apache Kafka v HDInsight](apache-kafka-scalability.md)
* [Použití Apache Sparku s využitím Apache Kafka streamování (diskretizovanými streamy)](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache strukturovaného streamování Sparku s využitím Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
