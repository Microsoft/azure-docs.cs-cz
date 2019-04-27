---
title: Protokoly Azure monitoru pro Apache Kafka – Azure HDInsight
description: Zjistěte, jak používat Azure Monitor protokoly k analýze protokolů z cluster Apache Kafka v Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: b4eced461f798f3cbf3ce968dae59cfb8f1a0363
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115090"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka v HDInsight

Zjistěte, jak používat Azure Monitor protokoly k analýze protokolů generovaných Apache Kafka v HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Povolit protokoly Azure monitoru pro Apache Kafka

Postup povolení protokolů Azure Monitor pro HDInsight je stejný pro všechny clustery HDInsight. Chcete-li pochopit, jak vytvořit a nakonfigurovat požadované služby použijte následující odkazy:

1. Vytvoření pracovního prostoru Log Analytics. Další informace najdete v tématu [Začínáme s pracovním prostorem Log Analytics](https://docs.microsoft.com/azure/log-analytics) dokumentu.

2. Vytvořte systém Kafka na clusteru HDInsight. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurace clusteru Kafka používat protokoly Azure monitoru. Další informace najdete v tématu [protokoly použití Azure Monitor k monitorování HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

    > [!NOTE]  
    > Můžete také nakonfigurovat cluster používat protokoly Azure monitoru s využitím `Enable-AzHDInsightOperationsManagementSuite` rutiny. Tato rutina vyžaduje následující informace:
    >
    > * Název clusteru HDInsight.
    > * ID pracovního prostoru pro protokoly Azure monitoru. ID pracovního prostoru můžete najít ve vašem pracovním prostoru Log Analytics.
    > * Primární klíč pro připojení k log analytics. Chcete-li najít primární klíč, otevřete pracovní prostor na webu Azure Portal, vyberte __upřesňující nastavení__ v levé nabídce. Upřesňující nastavení, vyberte __připojené zdroje__>__servery s Linuxem__.


> [!IMPORTANT]  
> Může trvat přibližně 20 minut, než se data jsou k dispozici pro protokoly Azure monitoru.

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
     | log\_kafkacontroller\_CL | Controller.log zprostředkovatele Kafka |
     | metrics\_kafka\_CL | Kafka JMX metrics |

     ![Obrázek hledání využití procesoru](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>Další postup

Další informace o monitorování Azure, najdete v článku [Přehled monitorování Azure](../../log-analytics/log-analytics-get-started.md) dokumentu.

Další informace o práci s využitím Apache Kafka najdete v následujících dokumentech:

 * [Apache Kafka zrcadlení mezi clustery HDInsight](apache-kafka-mirroring.md)
 * [Zvýšení škálovatelnosti systému Apache Kafka v HDInsight](apache-kafka-scalability.md)
 * [Použití Apache Sparku s využitím Apache Kafka streamování (diskretizovanými streamy)](../hdinsight-apache-spark-with-kafka.md)
 * [Použití Apache strukturovaného streamování Sparku s využitím Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
