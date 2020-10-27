---
title: Porty používané službami Hadoop ve službě HDInsight – Azure
description: Tento článek poskytuje seznam portů používaných Apache Hadoop službami spuštěnými ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: e3e2ca2eca0d258a14365edc97b98d0c827b0b6f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546054"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty používané službou Apache Hadoop Services ve službě HDInsight

Tento dokument poskytuje seznam portů používaných Apache Hadoop službami spuštěnými v clusterech HDInsight. Poskytuje taky informace o portech používaných pro připojení ke clusteru pomocí SSH.

## <a name="public-ports-vs-non-public-ports"></a>Veřejné porty vs. neveřejné porty

Clustery HDInsight se systémem Linux zveřejňují jenom tři porty, které jsou veřejně na internetu: 22, 23 a 443. Tyto porty zabezpečují přístup k clusteru pomocí SSH a služeb vystavených přes zabezpečený protokol HTTPS.

Služba HDInsight je implementovaná několika Virtual Machinesy Azure (uzly clusteru) běžícími na Azure Virtual Network. V rámci virtuální sítě můžete získat přístup k portům, které nejsou vystaveny přes Internet. Pokud se k hlavnímu uzlu připojíte přes SSH, můžete získat přímý přístup ke službám, které jsou spuštěné na uzlech clusteru.

> [!IMPORTANT]  
> Pokud neurčíte Azure Virtual Network jako možnost konfigurace pro HDInsight, vytvoří se jeden automaticky. K této virtuální síti ale nemůžete připojit jiné počítače (například jiné Azure Virtual Machines nebo váš klientský vývojový počítač).

Pokud chcete připojit další počítače k virtuální síti, musíte nejdřív vytvořit virtuální síť a pak ji zadat při vytváření clusteru HDInsight. Další informace najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Veřejné porty

Všechny uzly v clusteru HDInsight se nacházejí v Virtual Network Azure. K uzlům nejde získat přímý pøístup z Internetu. Veřejná brána poskytuje přístup k Internetu na následujících portech, které jsou společné pro všechny typy clusterů HDInsight.

| Služba | Port | Protokol | Popis |
| --- | --- | --- | --- |
| SSHD |22 |SSH |Připojí klienty k sshd primárnímu hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |22 |SSH |Připojí klienty k sshd na hraničním uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| SSHD |23 |SSH |Připojí klienty k sshd na sekundární hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Webové uživatelské rozhraní Ambari Viz [Správa HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Viz [Správa HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Viz [použití MapReduce s kudrlinkou](hadoop/apache-hadoop-use-mapreduce-curl.md) . |
| HiveServer2 |443 |ODBC |Připojí se k podregistru pomocí rozhraní ODBC. Přečtěte si téma [připojení Excelu k HDInsight pomocí ovladače Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Připojí se k ApacheHive pomocí JDBC. Další informace najdete v tématu [připojení k Apache Hive v HDInsight pomocí ovladače JDBC pro podregistr](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) . |

Pro konkrétní typy clusterů jsou k dispozici následující:

| Služba | Port | Protokol | Typ clusteru | Popis |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |HBase |HBA REST API. Viz Začínáme [s používáním adaptérů Apache HBA](hbase/apache-hbase-tutorial-get-started-linux.md) . |
| Livy |443 |HTTPS |Spark |Spark REST API. [Vzdálená aplikace pomocí Apache Livy najdete v tématu odesílání Apache Spark úloh](spark/apache-spark-livy-rest-interface.md) . |
| Server Spark Thrift |443 |HTTPS |Spark |Server Spark Thrift používaný k odesílání dotazů na podregistr. Viz [použití Beeline s Apache Hive ve službě HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Webové uživatelské rozhraní. Viz [nasazení a Správa topologií Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) . |
| Kafka REST proxy |443 |HTTPS |Kafka |Kafka REST API. Viz [interakce s clustery Apache Kafka ve službě Azure HDInsight pomocí proxy REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Authentication

Všechny služby veřejně vystavené na internetu musí být ověřené:

| Port | Credentials |
| --- | --- |
| 22 nebo 23 |Přihlašovací údaje uživatele SSH zadané při vytváření clusteru |
| 443 |Přihlašovací jméno (výchozí: admin) a heslo, které bylo nastaveno při vytváření clusteru |

## <a name="non-public-ports"></a>Porty, které nejsou veřejné

> [!NOTE]  
> Některé služby jsou dostupné jenom pro konkrétní typy clusterů. Například adaptéry HBA jsou k dispozici pouze v clusterech typu HBA.

> [!IMPORTANT]  
> Některé služby se v jednom okamžiku spouštějí jenom na jednom hlavnímu uzlu. Pokud se pokusíte připojit ke službě na primárním hlavnímu uzlu a získat chybu, zkuste znovu použít sekundární hlavnímu uzlu.

### <a name="ambari"></a>Ambari

| Služba | Uzly | Port | Cesta URL | Protokol |
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní Ambari | Hlavní uzly | 8080 | / | HTTP |
| Ambari REST API | Hlavní uzly | 8080 | /api/v1 | HTTP |

Příklady:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty HDFS

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní NameNode |Hlavní uzly |30070 |HTTPS |Webové uživatelské rozhraní pro zobrazení stavu |
| Služba metadat NameNode |hlavní uzly |8020 |IPC |Metadata systému souborů |
| Datauzel |Všechny pracovní uzly |30075 |HTTPS |Webové uživatelské rozhraní pro zobrazení stavu, protokolů a tak dále. |
| Datauzel |Všechny pracovní uzly |30010 |&nbsp; |Přenos dat |
| Datauzel |Všechny pracovní uzly |30020 |IPC |Operace s metadaty |
| Sekundární NameNode |Hlavní uzly |50090 |HTTP |Kontrolní bod pro metadata NameNode |

### <a name="yarn-ports"></a>Porty PŘÍZe

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Správce prostředků webové uživatelské rozhraní |Hlavní uzly |8088 |HTTP |Webové uživatelské rozhraní pro Správce prostředků |
| Správce prostředků webové uživatelské rozhraní |Hlavní uzly |8090 |HTTPS |Webové uživatelské rozhraní pro Správce prostředků |
| Rozhraní pro správu Správce prostředků |hlavní uzly |8141 |IPC |Pro odesílání aplikací (podregistr, server pro podregistr, prase atd.) |
| Plánovač Správce prostředků |hlavní uzly |8030 |HTTP |Rozhraní pro správu |
| Správce prostředků aplikační rozhraní |hlavní uzly |8050 |HTTP |Adresa rozhraní Správce aplikací |
| NodeManager |Všechny pracovní uzly |30050 |&nbsp; |Adresa správce kontejnerů |
| Webové uživatelské rozhraní NodeManager |Všechny pracovní uzly |30060 |HTTP |Rozhraní Správce prostředků |
| Adresa Timeline |Hlavní uzly |10200 |RPC |Služba RPC služby Timeline. |
| Webové uživatelské rozhraní Timeline |Hlavní uzly |8188 |HTTP |Webové uživatelské rozhraní služby Timeline |

### <a name="hive-ports"></a>Porty podregistru

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hlavní uzly |10001 |Thrift |Služba pro připojení k podregistru (Thrift/JDBC) |
| Metastore Hive |Hlavní uzly |9083 |Thrift |Služba pro připojení k metadatům podregistru (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat porty

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Server WebHCat |Hlavní uzly |30111 |HTTP |Webové rozhraní API nad HCatalog a dalšími službami Hadoop |

### <a name="mapreduce-ports"></a>MapReduce porty

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| JobHistory |Hlavní uzly |19888 |HTTP |Webové uživatelské rozhraní MapReduce JobHistory |
| JobHistory |Hlavní uzly |10020 |&nbsp; |Server MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Přenáší výstupy mezilehlé mapy na požadavky reduktorů |

### <a name="oozie"></a>Oozie

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Server Oozie |Hlavní uzly |11000 |HTTP |Adresa URL služby Oozie |
| Server Oozie |Hlavní uzly |11001 |HTTP |Port pro správce Oozie |

### <a name="ambari-metrics"></a>Metriky Ambari

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Časová osa (Historie aplikací) |Hlavní uzly |6188 |HTTP |Webové uživatelské rozhraní služby TimeLine |
| Časová osa (Historie aplikací) |Hlavní uzly |30200 |RPC |Webové uživatelské rozhraní služby TimeLine |

### <a name="hbase-ports"></a>Porty HBA

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| HMaster |Hlavní uzly |16000 |&nbsp; |&nbsp; |
| Webové uživatelské rozhraní HMaster info |Hlavní uzly |16010 |HTTP |Port pro HBase Master webové uživatelské rozhraní |
| Server oblasti |Všechny pracovní uzly |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, který klienti používají pro připojení k ZooKeeper |

### <a name="kafka-ports"></a>Kafka porty

| Služba | Uzly | Port | Protokol | Popis |
| --- | --- | --- | --- | --- |
| Nese |Pracovní uzly |9092 |Kafka přenosový protokol |Používá se pro komunikaci klientů. |
| &nbsp; |Uzly Zookeeper |2181 |&nbsp; |Port, který klienti používají pro připojení k Zookeeper |
| Proxy REST | Uzly správy Kafka |9400 |HTTPS |[Specifikace REST Kafka](/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Porty Spark

| Služba | Uzly | Port | Protokol | Cesta URL | Popis |
| --- | --- | --- | --- | --- | --- |
| Servery Spark Thrift |Hlavní uzly |10002 |Thrift | &nbsp; | Služba pro připojení k Spark SQL (Thrift/JDBC) |
| Server Livy | Hlavní uzly | 8998 | HTTP | &nbsp; | Služba pro spouštění příkazů, úloh a aplikací |
| Poznámkový blok Jupyter | Hlavní uzly | 8001 | HTTP | &nbsp; | Web poznámkového bloku Jupyter |

Příklady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"` . V tomto příkladu `10.0.0.11` je IP adresa hlavnímu uzlu, která je hostitelem služby Livy.