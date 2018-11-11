---
title: Porty používané služby Hadoop v HDInsight – Azure
description: Seznam portů používaných služby Hadoop spuštěné v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e6204933d6b9a4a6b296a141520fc8887c9181f1
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279690"
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Porty používané služby Hadoop v HDInsight

Tento dokument obsahuje seznam portů používaných služby Hadoop spuštěné v clusterech HDInsight založených na Linuxu. Obsahuje také informace o portech používaných pro připojení ke clusteru pomocí SSH.

## <a name="public-ports-vs-non-public-ports"></a>Veřejné porty a porty neveřejné

Clusterů HDInsight se systémem Linux vystavit pouze tři porty veřejně na Internetu. 22, 23 a 443. Tyto porty se používají pro zabezpečený přístup ke clusteru pomocí SSH a služby dostupné přes zabezpečený protokol HTTPS.

Interně, HDInsight je implementována pomocí několika virtuálních počítačích Azure (uzlů v rámci clusteru) běžící na Azure Virtual Network. Z v rámci virtuální sítě, můžete použít porty nejsou přístupná přes internet. Například pokud se připojit k jednomu z hlavní uzly pomocí protokolu SSH z hlavního uzlu můžete pak přímo použít služby spuštěné na uzlech clusteru.

> [!IMPORTANT]
> Pokud nezadáte službě Azure Virtual Network jako možnosti konfigurace pro HDInsight, vytvoří se automaticky. Nelze však přidávání jiných počítačů (například vývojovém počítači klienta nebo jiných virtuálních počítačích Azure) do této virtuální sítě.


K připojení dalších počítačů k virtuální síti, musíte nejprve vytvořit virtuální síť a pak ho zadat při vytváření clusteru HDInsight. Další informace najdete v tématu [možnosti rozšíření HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Veřejné porty

Všechny uzly v clusteru HDInsight se nacházejí ve službě Azure Virtual Network a nelze přistupovat přímo z Internetu. Veřejné brána poskytuje přístup k Internetu pro následující porty, které jsou společné pro všechny typy clusterů HDInsight.

| Služba | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- |
| sshd |22 |SSH |Klienti se připojí k sshd na primárnímu hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Klienti se připojí k sshd na hraničním uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Klienti se připojí k sshd na sekundární hlavní uzel. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Webové uživatelské rozhraní Ambari. Zobrazit [Správa HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Rozhraní Ambari REST API. Zobrazit [Správa HDInsight pomocí rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. Zobrazit [použití Hivu se službou Curl](hadoop/apache-hadoop-use-pig-curl.md), [použití Pigu se službou Curl](hadoop/apache-hadoop-use-pig-curl.md), [použití MapReduce se Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Se připojí k Hive pomocí ovladače ODBC. Zobrazit [připojení Excelu k HDInsight pomocí ovladače Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Připojení k Hivu pomocí JDBC. Zobrazit [připojit k Hive v HDInsight pomocí ovladače Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Tady jsou k dispozici pro specifické typy clusterů:

| Služba | Port | Protocol (Protokol) | Typ clusteru | Popis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |Rozhraní REST API HBase. Zobrazit [Začínáme používat HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Rozhraní Spark REST API. Zobrazit [odesílání Sparkových úloh vzdáleně pomocí Livy](spark/apache-spark-livy-rest-interface.md) |
| Server Spark Thrift |443 |HTTPS |Spark |Server Spark Thrift používaný k odesílání dotazů Hive. Zobrazit [použití Beeline pomocí Hive v HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm webového uživatelského rozhraní. Zobrazit [nasazení a správa topologií Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Všechny služby veřejně zpřístupnit v síti internet musí být ověřené:

| Port | Přihlašovací údaje |
| --- | --- |
| 22 a 23 |Přihlašovacích údajů uživatele SSH zadané při vytváření clusteru |
| 443 |Přihlašovací jméno (výchozí: správce) a heslo, které byly nastaveny při vytváření clusteru |

## <a name="non-public-ports"></a>Neveřejné porty

> [!NOTE]
> Některé služby jsou dostupné jenom pro specifické typy clusterů. Například HBase je dostupná jenom na typy clusterů HBase.

> [!IMPORTANT]
> Některé služby spustit jenom na jeden hlavní uzel v čase. Pokud se pokusíte připojit ke službě na primární hlavní uzel a zobrazí se chyba, zkuste použít sekundární hlavní uzel.

### <a name="ambari"></a>Ambari

| Služba | Uzly | Port | Cesta URL | Protocol (Protokol) | 
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní Ambari | Hlavní uzly | 8080 | / | HTTP |
| Rozhraní Ambari REST API | Hlavní uzly | 8080 | / api/v1 | HTTP |

Příklady:

* Rozhraní Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty HDFS

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| NameNode webového uživatelského rozhraní |Hlavní uzly |30070 |HTTPS |Webové uživatelské rozhraní k zobrazení stavu |
| NameNode metadat služby |Hlavní uzly |8020 |IPC |Metadatech systému souborů |
| DataNode |Všechny uzly pracovního procesu |30075 |HTTPS |Webové uživatelské rozhraní zobrazit stav, protokoly atd. |
| DataNode |Všechny uzly pracovního procesu |30010 |&nbsp; |Přenos dat |
| DataNode |Všechny uzly pracovního procesu |30020 |IPC |Operace s metadaty |
| Sekundární NameNode |Hlavní uzly |50090 |HTTP |Kontrolní bod pro NameNode metadat |

### <a name="yarn-ports"></a>Porty YARN

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Webové uživatelské rozhraní správce prostředků |Hlavní uzly |8088 |HTTP |Webové uživatelské rozhraní pro Resource Manager |
| Webové uživatelské rozhraní správce prostředků |Hlavní uzly |8090 |HTTPS |Webové uživatelské rozhraní pro Resource Manager |
| Rozhraní správce Resource Manageru |Hlavní uzly |8141 |IPC |Pro odeslání aplikace (Hive, server Hive, Pig, atd.) |
| Plánovač Resource Manageru |Hlavní uzly |8030 |HTTP |Rozhraní pro správu |
| Rozhraní správce prostředků aplikace |Hlavní uzly |8050 |HTTP |Adresa správce rozhraní aplikace |
| NodeManager |Všechny uzly pracovního procesu |30050 |&nbsp; |Adresa správce kontejneru |
| NodeManager webového uživatelského rozhraní |Všechny uzly pracovního procesu |30060 |HTTP |Rozhraní správce prostředků |
| Adresa časové osy |Hlavní uzly |10200 |RPC |Služba RPC časovou osu služby. |
| Časová osa webového uživatelského rozhraní |Hlavní uzly |8181 |HTTP |Časová osa služby webového uživatelského rozhraní |

### <a name="hive-ports"></a>Porty Hive

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hlavní uzly |10001 |Thrift |Služby pro připojení k Hivu (Thrift/JDBC) |
| Hive Metastore |Hlavní uzly |9083 |Thrift |Služby pro připojení k metadata Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| WebHCat server |Hlavní uzly |30111 |HTTP |Webových rozhraní API jako nadstavby HCatalog a dalším službám Hadoop |

### <a name="mapreduce-ports"></a>Porty MapReduce

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| JobHistory |Hlavní uzly |19888 |HTTP |MapReduce JobHistory webového uživatelského rozhraní |
| JobHistory |Hlavní uzly |10020 |&nbsp; |Server MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Přenosy zprostředkující mapy výstupy k vyžádání Reduktorů |

### <a name="oozie"></a>Oozie

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Oozie server |Hlavní uzly |11000 |HTTP |Adresa URL služby Oozie |
| Oozie server |Hlavní uzly |11001 |HTTP |Port pro správce Oozie |

### <a name="ambari-metrics"></a>Metriky Ambari

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Časová osa (historie aplikace) |Hlavní uzly |6188 |HTTP |Časová osa služby webového uživatelského rozhraní |
| Časová osa (historie aplikace) |Hlavní uzly |30200 |RPC |Časová osa služby webového uživatelského rozhraní |

### <a name="hbase-ports"></a>Porty HBase

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HMaster |Hlavní uzly |16000 |&nbsp; |&nbsp; |
| Informace o HMaster webového uživatelského rozhraní |Hlavní uzly |16010 |HTTP |Port pro hlavní server HBase webového uživatelského rozhraní |
| Server oblasti |Všechny uzly pracovního procesu |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, který klienti používají pro připojení k ZooKeeper |

### <a name="kafka-ports"></a>Porty systému Kafka

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Service Broker |Pracovní uzly |9092 |[Kafka přenosový protokol](http://kafka.apache.org/protocol.html) |Používá pro komunikaci klientů |
| &nbsp; |Uzly Zookeeper |2181 |&nbsp; |Port, který klienti používají pro připojení k Zookeeper |

### <a name="spark-ports"></a>Porty Spark

| Služba | Uzly | Port | Protocol (Protokol) | Cesta URL | Popis |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift servery |Hlavní uzly |10002 |Thrift | &nbsp; | Služby pro připojení ke Spark SQL (Thrift/JDBC) |
| Livy server | Hlavní uzly | 8998 | HTTP | &nbsp; | Služba pro spouštění příkazů, úlohy a aplikace |
| Jupyter Notebook | Hlavní uzly | 8001 | HTTP | &nbsp; | Web poznámkového bloku Jupyter |

Příklady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. V tomto příkladu `10.0.0.11` je IP adresa hlavního uzlu, který je hostitelem služby Livy.
