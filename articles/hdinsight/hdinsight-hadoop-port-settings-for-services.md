---
title: Porty používané službami Hadoop ve službě HDInsight – Azure
description: Seznam portů používaných službami Hadoop, které běží na HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 34ab49378f9237a42bed869a6f6d67249b5238f9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464697"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty používané službou Apache Hadoop Services ve službě HDInsight

Tento dokument poskytuje seznam portů používaných Apache Hadoop službami, které běží na clusterech HDInsight se systémem Linux. Poskytuje taky informace o portech používaných pro připojení ke clusteru pomocí SSH.

## <a name="public-ports-vs-non-public-ports"></a>Veřejné porty vs. neveřejné porty

Clustery HDInsight se systémem Linux zveřejňují jenom tři porty, které jsou veřejně na internetu. 22, 23 a 443. Tyto porty se používají pro zabezpečený přístup ke clusteru pomocí SSH a služeb vystavených přes zabezpečený protokol HTTPS.

Služba HDInsight je interně implementovaná několika Virtual Machines Azure (uzly v clusteru), které běží na Azure Virtual Network. V rámci virtuální sítě můžete získat přístup k portům, které nejsou vystaveny přes Internet. Například pokud se připojíte k některému z hlavních uzlů pomocí SSH, z hlavního uzlu pak můžete přímo přistupovat ke službám běžícím na uzlech clusteru.

> [!IMPORTANT]  
> Pokud neurčíte Azure Virtual Network jako možnost konfigurace pro HDInsight, vytvoří se jeden automaticky. Do této virtuální sítě ale nemůžete připojit jiné počítače (například jiné Azure Virtual Machines nebo váš počítač pro vývoj klientů).

Pokud chcete připojit další počítače k virtuální síti, musíte nejdřív vytvořit virtuální síť a pak ji zadat při vytváření clusteru HDInsight. Další informace najdete v tématu [plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Veřejné porty

Všechny uzly v clusteru HDInsight se nacházejí v Virtual Network Azure a nelze k nim přímo získat z Internetu. Veřejná brána poskytuje přístup k Internetu na následujících portech, které jsou společné pro všechny typy clusterů HDInsight.

| Služba | Port | Protocol | Popis |
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

| Služba | Port | Protocol | Typ clusteru | Popis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBA REST API. Viz Začínáme [s používáním adaptérů Apache HBA](hbase/apache-hbase-tutorial-get-started-linux.md) . |
| Livy |443 |HTTPS |Spark |Spark REST API. [Vzdálená aplikace pomocí Apache Livy najdete v tématu odesílání Apache Spark úloh](spark/apache-spark-livy-rest-interface.md) . |
| Server Spark Thrift |443 |HTTPS |Spark |Server Spark Thrift používaný k odesílání dotazů na podregistr. Viz [použití Beeline s Apache Hive ve službě HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Webové uživatelské rozhraní. Viz [nasazení a Správa topologií Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) . |

### <a name="authentication"></a>Ověřování

Všechny služby veřejně vystavené na internetu musí být ověřené:

| Port | Pověření |
| --- | --- |
| 22 nebo 23 |Přihlašovací údaje uživatele SSH zadané při vytváření clusteru |
| 443 |Přihlašovací jméno (výchozí: admin) a heslo, které bylo nastaveno při vytváření clusteru |

## <a name="non-public-ports"></a>Porty, které nejsou veřejné

> [!NOTE]  
> Některé služby jsou dostupné jenom pro konkrétní typy clusterů. Například adaptéry HBA jsou k dispozici pouze v clusterech typu HBA.

> [!IMPORTANT]  
> Některé služby se v jednom okamžiku spouštějí jenom na jednom hlavnímu uzlu. Pokud se pokusíte připojit ke službě na primárním hlavnímu uzlu a získat chybu, zkuste znovu použít sekundární hlavnímu uzlu.

### <a name="ambari"></a>Ambari

| Služba | Uzly | Port | Cesta URL | Protocol | 
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní Ambari | Hlavní uzly | 8080 | / | HTTP |
| Ambari REST API | Hlavní uzly | 8080 | /api/v1 | HTTP |

Příklady:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty HDFS

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní NameNode |Hlavní uzly |30070 |HTTPS |Webové uživatelské rozhraní pro zobrazení stavu |
| Služba metadat NameNode |hlavní uzly |8020 |IPC |Metadata systému souborů |
| DataNode |Všechny pracovní uzly |30075 |HTTPS |Webové uživatelské rozhraní pro zobrazení stavu, protokolů atd. |
| DataNode |Všechny pracovní uzly |30010 |&nbsp; |Přenos dat |
| DataNode |Všechny pracovní uzly |30020 |IPC |Operace s metadaty |
| Sekundární NameNode |Hlavní uzly |50090 |HTTP |Kontrolní bod pro metadata NameNode |

### <a name="yarn-ports"></a>Porty PŘÍZe

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Správce prostředků webové uživatelské rozhraní |Hlavní uzly |8088 |HTTP |Webové uživatelské rozhraní pro Správce prostředků |
| Správce prostředků webové uživatelské rozhraní |Hlavní uzly |8090 |HTTPS |Webové uživatelské rozhraní pro Správce prostředků |
| Rozhraní pro správu Správce prostředků |hlavní uzly |8141 |IPC |Pro odesílání aplikací (podregistr, server v podregistru, prase atd.) |
| Plánovač Správce prostředků |hlavní uzly |8030 |HTTP |Rozhraní pro správu |
| Správce prostředků aplikační rozhraní |hlavní uzly |8050 |HTTP |Adresa rozhraní Správce aplikací |
| NodeManager |Všechny pracovní uzly |30050 |&nbsp; |Adresa správce kontejnerů |
| Webové uživatelské rozhraní NodeManager |Všechny pracovní uzly |30060 |HTTP |Rozhraní Správce prostředků |
| Adresa Timeline |Hlavní uzly |10200 |RPC |Služba RPC služby Timeline. |
| Webové uživatelské rozhraní Timeline |Hlavní uzly |8188 |HTTP |Webové uživatelské rozhraní služby Timeline |

### <a name="hive-ports"></a>Porty podregistru

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hlavní uzly |10001 |Thrift |Služba pro připojení k podregistru (Thrift/JDBC) |
| Metastore Hive |Hlavní uzly |9083 |Thrift |Služba pro připojení k metadatům podregistru (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat porty

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Server WebHCat |Hlavní uzly |30111 |HTTP |Webové rozhraní API nad HCatalog a dalšími službami Hadoop |

### <a name="mapreduce-ports"></a>MapReduce porty

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| JobHistory |Hlavní uzly |19888 |HTTP |Webové uživatelské rozhraní MapReduce JobHistory |
| JobHistory |Hlavní uzly |10020 |&nbsp; |Server MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Přenáší výstupy mezilehlé mapy na požadavky reduktorů |

### <a name="oozie"></a>Oozie

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Server Oozie |Hlavní uzly |11000 |HTTP |Adresa URL služby Oozie |
| Server Oozie |Hlavní uzly |11001 |HTTP |Port pro správce Oozie |

### <a name="ambari-metrics"></a>Metriky Ambari

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Časová osa (Historie aplikací) |Hlavní uzly |6188 |HTTP |Webové uživatelské rozhraní služby TimeLine |
| Časová osa (Historie aplikací) |Hlavní uzly |30200 |RPC |Webové uživatelské rozhraní služby TimeLine |

### <a name="hbase-ports"></a>Porty HBA

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| HMaster |Hlavní uzly |16000 |&nbsp; |&nbsp; |
| Webové uživatelské rozhraní HMaster info |Hlavní uzly |16010 |HTTP |Port pro HBase Master webové uživatelské rozhraní |
| Server oblasti |Všechny pracovní uzly |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, který klienti používají pro připojení k ZooKeeper |

### <a name="kafka-ports"></a>Kafka porty

| Služba | Uzly | Port | Protocol | Popis |
| --- | --- | --- | --- | --- |
| Nese |Pracovní uzly |9092 |[Kafka přenosový protokol](https://kafka.apache.org/protocol.html) |Používá se pro komunikaci klientů. |
| &nbsp; |Uzly Zookeeper |2181 |&nbsp; |Port, který klienti používají pro připojení k Zookeeper |

### <a name="spark-ports"></a>Porty Spark

| Služba | Uzly | Port | Protocol | Cesta URL | Popis |
| --- | --- | --- | --- | --- | --- |
| Servery Spark Thrift |Hlavní uzly |10002 |Thrift | &nbsp; | Služba pro připojení k Spark SQL (Thrift/JDBC) |
| Server Livy | Hlavní uzly | 8998 | HTTP | &nbsp; | Služba pro spouštění příkazů, úloh a aplikací |
| Jupyter Notebook | Hlavní uzly | 8001 | HTTP | &nbsp; | Web poznámkového bloku Jupyter |

Příklady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. V tomto příkladu `10.0.0.11` je IP adresa hlavnímu uzlu, která je hostitelem služby Livy.
