---
title: Log Analytics pro Apache Kafka – Azure HDInsight
description: Zjistěte, jak používat službu Log Analytics k analýze protokolů z cluster Apache Kafka v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 9421217383f390630687d8416512401487ad3f7e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621384"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka v HDInsight

Zjistěte, jak používat službu Log Analytics k analýze protokolů generovaných Apache Kafka v HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Povolení Log Analytics pro systém Kafka

Postup povolení Log Analytics pro HDInsight je stejný pro všechny clustery HDInsight. Chcete-li pochopit, jak vytvořit a nakonfigurovat požadované služby použijte následující odkazy:

1. Vytvoření pracovního prostoru Log Analytics. Další informace najdete v tématu [Začínáme s pracovním prostorem Log Analytics](https://docs.microsoft.com/azure/log-analytics) dokumentu.

2. Vytvořte systém Kafka na clusteru HDInsight. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurace clusteru Kafka pro použití služby Log Analytics. Další informace najdete v tématu [použití Log Analytics k monitorování HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

    > [!NOTE]
    > Můžete také nakonfigurovat cluster používat Log Analytics s využitím `Enable-AzureRmHDInsightOperationsManagementSuite` rutiny. Tato rutina vyžaduje následující informace:
    >
    > * Název clusteru HDInsight.
    > * ID pracovního prostoru Log Analytics. ID pracovního prostoru můžete najít ve vašem pracovním prostoru Log Analytics.
    > * Primární klíč pro připojení k Log Analytics. Chcete-li najít primární klíč, otevřete pracovní prostor na webu Azure Portal, vyberte __upřesňující nastavení__ v levé nabídce. Upřesňující nastavení, vyberte __připojené zdroje__>__servery s Linuxem__.


> [!IMPORTANT]
> Může trvat přibližně 20 minut, než se data jsou k dispozici pro Log Analytics.

## <a name="query-logs"></a>Protokoly dotazů

1. Z [webu Azure portal](https://portal.azure.com), vyberte pracovní prostor Log Analytics.

2. Vyberte __prohledávání protokolů__. Z tohoto místa můžete prohledávat data shromážděná z Kafka. Tady jsou některé příklady hledání:

    * Využití disku: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Využití procesoru: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Příchozí zprávy za sekundu: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Příchozí bajty za sekundu: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Odchozí bajty za sekundu: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > Nahraďte hodnoty dotazu clusteru konkrétní informace. Například `ClusterName_s` musí být nastavena na název vašeho clusteru. `HostName_s` musí být nastavena na název domény pracovního uzlu v clusteru.

    Můžete také zadat `*` k vyhledání všech typů přihlášení. Tyto protokoly jsou aktuálně k dispozici pro dotazy:

    | Typ protokolu | Popis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log zprostředkovatele Kafka |
    | protokol\_kafkacontroller\_CL | Controller.log zprostředkovatele Kafka |
    | metriky\_kafka\_CL | Metriky Kafka JMX |

    ![Obrázek hledání využití procesoru](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Další postup

Další informace o Log Analytics najdete v článku [Začínáme s pracovním prostorem Log Analytics](../../log-analytics/log-analytics-get-started.md) dokumentu.

Další informace o práci s platformou Kafka najdete v následujících dokumentech:

 * [Zrcadlení mezi clustery HDInsight Kafka](apache-kafka-mirroring.md)
 * [Zvýšení škálovatelnosti systému Kafka v HDInsight](apache-kafka-scalability.md)
 * [Použití Sparku streamování (diskretizovanými streamy) s využitím Kafky](../hdinsight-apache-spark-with-kafka.md)
 * [Použití strukturovaného streamování Sparku se systémem Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
