---
title: Analýza protokolu Apache Kafka - Azure HDInsight | Microsoft Docs
description: Další informace o použití Log Analytics k analýze protokolů z clusteru Apache Kafka v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: larryfr
ms.openlocfilehash: 15107a0fbcd6242ac13f366b16be10efaeaad6bb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analýza protokolů pro Apache Kafka v HDInsight

Další informace o použití Log Analytics k analýze protokolů generovaných Apache Kafka v HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Povolit analýzy protokolů pro Kafka

Postup povolení analýzy protokolů pro HDInsight jsou stejné pro všechny clustery HDInsight. Pomocí následujících odkazů pochopit, jak vytvořit a nakonfigurovat požadované služby:

1. Vytvořte pracovní prostor analýzy protokolů. Další informace najdete v tématu [začít pracovat s pracovní prostor analýzy protokolů](https://docs.microsoft.com/azure/log-analytics) dokumentu.

2. Vytvořte Kafka na clusteru HDInsight. Další informace najdete v tématu [začínat Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

3. Konfigurace clusteru Kafka, aby používaly analýzy protokolů. Další informace najdete v tématu [analýzy protokolů použít ke sledování HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) dokumentu.

    > [!NOTE]
    > Můžete také nakonfigurovat cluster používat analýzy protokolů pomocí `Enable-AzureRmHDInsightOperationsManagementSuite` rutiny. Tento příkaz cmdlet vyžaduje následující informace:
    >
    > * Název clusteru HDInsight.
    > * ID pracovního prostoru analýzy protokolů. ID pracovního prostoru můžete najít v pracovním prostoru analýzy protokolů.
    > * Primární klíč pro připojení k analýze protokolů. Primární klíč, vyberete instanci analýzy protokolů a potom __portálu OMS__. Na portálu OMS vyberte __nastavení__, __připojené zdroje__a potom __servery se systémem Linux__.


> [!IMPORTANT]
> To může trvat přibližně 20 minut, než se data nejsou k dispozici pro analýzu protokolu.

## <a name="query-logs"></a>Protokoly dotazu

1. Z [portál Azure](https://portal.azure.com), vyberte pracovní prostor analýzy protokolů.

2. Vyberte __protokolu vyhledávání__. Tady můžete hledat data shromážděná z Kafka. Tady jsou některé příklady hledání:

    * Využití disku: `Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * Využití procesoru: `Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * Příchozích zpráv za sekundu: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * Příchozí bajty za sekundu: `Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * Odchozí bajty za sekundu: `Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > Nahraďte hodnoty dotazu clusteru konkrétní informace. Například `ClusterName_s` musí být nastavena na název clusteru. `HostName_s` musí být nastavena na název domény pracovního uzlu v clusteru.

    Můžete také zadat `*` k vyhledání všech typů protokolována. Tyto protokoly jsou aktuálně k dispozici pro dotazy:

    | Typ protokolu | Popis |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Server.log Kafka zprostředkovatele |
    | log\_kafkacontroller\_CL | Controller.log Kafka zprostředkovatele |
    | metrics\_kafka\_CL | Kafka JMX metriky |

    ![Obrázek hledání využití procesoru](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Další postup

Další informace o analýzy protokolů, najdete v článku [začít pracovat s pracovní prostor analýzy protokolů](../../log-analytics/log-analytics-get-started.md) dokumentu.

Další informace o práci s Kafka najdete v následujících dokumentech:

 * [Zrcadlení Kafka mezi clustery HDInsight](apache-kafka-mirroring.md)
 * [Zvýšení škálovatelnosti Kafka v HDInsight](apache-kafka-scalability.md)
 * [Pomocí vysílání datového proudu (DStreams) s Kafka Spark](../hdinsight-apache-spark-with-kafka.md)
 * [Používejte Spark strukturovaná streamování s Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)