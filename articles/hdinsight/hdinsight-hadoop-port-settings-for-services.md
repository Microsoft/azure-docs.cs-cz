---
title: Porty používané službami Hadoop na HDInsight – Azure
description: Tento článek obsahuje seznam portů používaných službami Apache Hadoop spuštěnými v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314687"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty používané službami Apache Hadoop na HDInsight

Tento dokument obsahuje seznam portů používaných službami Apache Hadoop spuštěnými v clusterech HDInsight. Poskytuje také informace o portech používaných pro připojení ke clusteru pomocí SSH.

## <a name="public-ports-vs-non-public-ports"></a>Veřejné přístavy vs. neveřejné přístavy

Linuxové clustery HDInsight zveřejňují pouze tři porty na internetu; 22, 23 a 443. Tyto porty se používají k bezpečnému přístupu ke clusteru pomocí SSH a služeb vystavených přes zabezpečený protokol HTTPS.

HdInsight je interně implementován několika virtuálními počítači Azure (uzly v rámci clusteru) spuštěnými ve virtuální síti Azure. Z virtuální sítě můžete přistupovat k portům, které nejsou vystaveny přes internet. Pokud se například připojíte k jednomu z hlavního uzlu pomocí SSH, můžete z hlavního uzlu přímo přistupovat ke službám spuštěným v uzlech clusteru.

> [!IMPORTANT]  
> Pokud virtuální síť Azure nezadáte jako možnost konfigurace pro HDInsight, vytvoří se automaticky. Do této virtuální sítě se však nemůžete připojit k jiným počítačům (například k jiným virtuálním počítačům Azure nebo vašemu vývojovému počítači klienta).

Chcete-li připojit další počítače k virtuální síti, musíte nejprve vytvořit virtuální síť a pak ji zadat při vytváření clusteru HDInsight. Další informace najdete [v tématu Plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Veřejné přístavy

Všechny uzly v clusteru HDInsight jsou umístěné ve virtuální síti Azure a nelze k nim přímo přistupovat z internetu. Veřejná brána poskytuje přístup k internetu k následujícím portům, které jsou běžné ve všech typech clusterů HDInsight.

| Služba | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- |
| Sshd |22 |SSH |Připojuje klienty k sshd na primárním headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |22 |SSH |Připojuje klienty k sshd na hraničním uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |23 |SSH |Připojuje klienty k sshd na sekundárním headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Webové uživatelské uzla Ambari. Viz [Správa HDInsightpomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Rozhraní API Ambari REST. Viz [Správa HDInsightu pomocí rozhraní APACHE Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Viz [Použití MapReduce s curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Připojí se k Hive pomocí rozhraní ODBC. Viz [Připojení Excelu k HDInsightu pomocí ovladače Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Připojuje se k ApacheHive pomocí JDBC. Viz [Připojení k Apache Hive na HDInsight pomocí ovladače Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Pro konkrétní typy clusterů jsou k dispozici následující:

| Služba | Port | Protocol (Protokol) | Typ clusteru | Popis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. Viz [Začínáme používat Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Rozhraní API SPARK REST. Viz [Odeslat úlohy Apache Spark na dálku pomocí Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift server |443 |HTTPS |Spark |Spark Thrift server slouží k odesílání dotazů Hive. Viz [Použití beeline s Apache Hive na HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Uživatelské i uživatelské ho uživatelského uživatelského misce Storm web Viz [Nasazení a správa topologie Apache Storm na HDInsightu](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Kafka Zbytek proxy |443 |HTTPS |Kafka |API Kafka REST. Viz [Interakce s clustery Apache Kafka v Azure HDInsight pomocí proxy rest](kafka/rest-proxy.md) |

### <a name="authentication"></a>Ověřování

Všechny služby veřejně exponované na internetu musí být ověřeny:

| Port | Přihlašovací údaje |
| --- | --- |
| 22 nebo 23 |Pověření uživatele SSH zadaná při vytváření clusteru |
| 443 |Přihlašovací jméno (výchozí: admin) a heslo, které byly nastaveny při vytváření clusteru |

## <a name="non-public-ports"></a>Neveřejné přístavy

> [!NOTE]  
> Některé služby jsou k dispozici pouze u určitých typů clusterů. Například HBase je k dispozici pouze na typy clusteru HBase.

> [!IMPORTANT]  
> Některé služby běží pouze na jednom headnode najednou. Pokud se pokusíte připojit ke službě na primární mnoše headnode a obdržíte chybu, opakujte použití sekundárního headnode.

### <a name="ambari"></a>Ambari

| Služba | Uzly | Port | Cesta url | Protocol (Protokol) |
| --- | --- | --- | --- | --- |
| Webové uživatelské uzlina Ambari | Hlavní uzly | 8080 | / | HTTP |
| Ambari REST API | Hlavní uzly | 8080 | /api/v1 | HTTP |

Příklady:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HdFS porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| NameNode web UI |Hlavní uzly |30070 |HTTPS |Webové uživatelské uživatelské pásmo pro zobrazení stavu |
| Služba metadat NameNode |hlavní uzly |8020 |Ipc |Metadata systému souborů |
| Datový uzel |Všechny pracovní uzly |30075 |HTTPS |Webové uživatelské uživatelské pásmo pro zobrazení stavu, protokolů atd. |
| Datový uzel |Všechny pracovní uzly |30010 |&nbsp; |Přenos dat |
| Datový uzel |Všechny pracovní uzly |30020 |Ipc |Operace metadat |
| Sekundární názevnode |Hlavní uzly |50090 |HTTP |Kontrolní bod pro metadata NameNode |

### <a name="yarn-ports"></a>Porty YARN

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Webové uživatelské nastavení Správce prostředků |Hlavní uzly |8088 |HTTP |Webové uživatelské nastavení pro Správce prostředků |
| Webové uživatelské nastavení Správce prostředků |Hlavní uzly |8090 |HTTPS |Webové uživatelské nastavení pro Správce prostředků |
| Rozhraní správce Správce prostředků |hlavní uzly |8141 |Ipc |Pro podání žádosti (Hive, Hive server, Pig, atd.) |
| Plánovač Správce zdrojů |hlavní uzly |8030 |HTTP |Administrativní rozhraní |
| Rozhraní aplikace Správce prostředků |hlavní uzly |8050 |HTTP |Adresa rozhraní správce aplikací |
| NodeManager |Všechny pracovní uzly |30050 |&nbsp; |Adresa správce kontejnerů |
| NodeManager web UI |Všechny pracovní uzly |30060 |HTTP |Rozhraní Správce prostředků |
| Adresa časové osy |Hlavní uzly |10200 |RPC |Služba Vzdáleného volání procedur služby Časové osy. |
| Timeline web UI |Hlavní uzly |8188 |HTTP |The Timeline service web UI |

### <a name="hive-ports"></a>Hive porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hlavní uzly |10001 |Šetrnost |Služba pro připojení k Hive (Thrift/JDBC) |
| Metastore Hive |Hlavní uzly |9083 |Šetrnost |Služba pro připojení k metadatům Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| WebHCat server |Hlavní uzly |30111 |HTTP |Webové rozhraní API nad HCatalog a dalšími službami Hadoop |

### <a name="mapreduce-ports"></a>MapReduce porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Historie úloh |Hlavní uzly |19888 |HTTP |Webové uživatelské rozhraní MapReduce JobHistory |
| Historie úloh |Hlavní uzly |10020 |&nbsp; |MapReduce JobHistory server |
| Náhodné manipulátory |&nbsp; |13562 |&nbsp; |Přenese zprostředkující výstupy mapy na požadující reduktory. |

### <a name="oozie"></a>Oozie

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Oozie server |Hlavní uzly |11000 |HTTP |ADRESA URL služby Oozie |
| Oozie server |Hlavní uzly |11001 |HTTP |Port pro Oozie admin |

### <a name="ambari-metrics"></a>Metriky Ambari

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Časová osa (historie aplikací) |Hlavní uzly |6188 |HTTP |The TimeLine service web UI |
| Časová osa (historie aplikací) |Hlavní uzly |30200 |RPC |The TimeLine service web UI |

### <a name="hbase-ports"></a>HBase porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HMaster |Hlavní uzly |16000 |&nbsp; |&nbsp; |
| HMaster info Webové uživatelské uzlení |Hlavní uzly |16010 |HTTP |The port for the HBase Master web UI |
| Oblastní server |Všechny pracovní uzly |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, který klienti používají pro připojení k ZooKeeper |

### <a name="kafka-ports"></a>Kafka porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Broker |Pracovní uzly |9092 |[Drátěný protokol Kafka](https://kafka.apache.org/protocol.html) |Používá se pro komunikaci s klientem |
| &nbsp; |Zookeeper uzly |2181 |&nbsp; |Port, který klienti používají pro připojení k Zookeeper |
| Rest proxy | Uzly pro správu Kafka |9400 |HTTPS |[Specifikace Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Zapalovací porty

| Služba | Uzly | Port | Protocol (Protokol) | Cesta url | Popis |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift servery |Hlavní uzly |10002 |Šetrnost | &nbsp; | Služba pro připojení k Spark SQL (Thrift/JDBC) |
| Livy server | Hlavní uzly | 8998 | HTTP | &nbsp; | Služba pro spouštění příkazů, úloh a aplikací |
| Poznámkový blok Jupyter | Hlavní uzly | 8001 | HTTP | &nbsp; | Jupyter notebook webové stránky |

Příklady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. V tomto `10.0.0.11` příkladu je IP adresa headnode, který je hostitelem služby Livy.
