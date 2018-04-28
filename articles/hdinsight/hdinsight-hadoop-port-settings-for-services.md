---
title: Porty používané služby Hadoop v HDInsight - Azure | Microsoft Docs
description: Seznam portů používaných běžící Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: larryfr
ms.openlocfilehash: 4490ac9bccb406bd2e882fc8afcbaf05aa8ddfab
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Porty používané služby Hadoop v HDInsight

Tento dokument obsahuje seznam portů používaných pro Hadoop služby spuštěné v clusterech HDInsight se systémem Linux. Také obsahuje informace o portech používaných pro připojení ke clusteru pomocí SSH.

## <a name="public-ports-vs-non-public-ports"></a>Veřejné porty oproti neveřejný porty

Clustery HDInsight se systémem Linux vystavit jenom tři porty veřejně na Internetu. 22, 23 a 443. Tyto porty se používají pro bezpečný přístup ke clusteru pomocí SSH a služby, které jsou zveřejněné přes zabezpečený protokol HTTPS.

Interně HDInsight je implementována pomocí několika virtuálních počítačích Azure (uzly v clusteru) spuštěné v Azure Virtual Network. Z virtuální sítě, máte přístup k porty nejsou viditelné prostřednictvím Internetu. Například pokud se připojit k jedním z hlavních uzlech pomocí protokolu SSH, z hlavního uzlu pak přímo přístupné služby spuštěné na uzlech clusteru.

> [!IMPORTANT]
> Pokud nezadáte virtuální síť Azure jako možnost konfigurace pro HDInsight, jeden se vytvoří automaticky. Nelze však přidávání jiných počítačů (například vývojovém počítači klienta nebo jiných virtuálních počítačích Azure) do této virtuální síti.


Při přidávání dalších počítačů do virtuální sítě, musíte nejprve vytvořit virtuální síť a pak zadejte při vytváření clusteru HDInsight. Další informace najdete v tématu [možnosti rozšíření HDInsight pomocí virtuální síť Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Veřejné porty

Všechny uzly v clusteru služby HDInsight jsou umístěny ve virtuální síti Azure a nelze přistupovat přímo z Internetu. Veřejné brány poskytuje přístup k Internetu pro následující porty, které jsou společné pro všechny typy clusteru HDInsight.

| Služba | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| sshd |22 |SSH |Klienti se připojí k sshd na primární headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Klienti se připojí k sshd na uzlu edge. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Klienti se připojí k sshd na sekundární headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Webovému uživatelskému rozhraní Ambari. V tématu [spravovat HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. V tématu [spravovat HDInsight pomocí Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API. V tématu [použijte Hive s Curl](hadoop/apache-hadoop-use-pig-curl.md), [použijte Pig s Curl](hadoop/apache-hadoop-use-pig-curl.md), [používání nástroje MapReduce s Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Připojí se ke Hive pomocí ovladače ODBC. V tématu [připojení aplikace Excel do HDInsight pomocí ovladače Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Připojí se ke Hive pomocí JDBC. V tématu [připojit k Hive v HDInsight pomocí ovladač Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Tady jsou dostupné pro konkrétní cluster typy:

| Služba | Port | Protocol (Protokol) | Typ clusteru | Popis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. V tématu [Začínáme používat HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Rozhraní API REST Spark. V tématu [úlohy odeslání Spark vzdáleně pomocí Livy](spark/apache-spark-livy-rest-interface.md) |
| Serveru Spark Thrift |443 |HTTPS |Spark |Serveru Spark Thrift používá k odesílání dotazů Hive. V tématu [Beeline pomocí Hive v HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm webového uživatelského rozhraní. V tématu [nasadit a spravovat topologie Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Musí být ověřeny všechny služby veřejně zveřejněné na Internetu:

| Port | Přihlašovací údaje |
| --- | --- |
| 22 nebo 23 |SSH pověření uživatele zadané při vytváření clusteru |
| 443 |Přihlašovací jméno (výchozí: správce) a heslo, které byly nastavené při vytváření clusteru |

## <a name="non-public-ports"></a>Není veřejné porty

> [!NOTE]
> Některé služby jsou dostupné jenom na konkrétní clusteru typy. Například HBase je k dispozici pouze na typy clusteru HBase.

> [!IMPORTANT]
> Některé služby spustit pouze v jedné headnode najednou. Pokud se pokusíte připojit ke službě na primární headnode a zobrazí se chyba, zkuste použít sekundární headnode.

### <a name="ambari"></a>Ambari

| Služba | Uzly | Port | Cesta URL | Protocol (Protokol) | 
| --- | --- | --- | --- | --- |
| Webovému uživatelskému rozhraní Ambari | hlavních uzlech | 8080 | / | HTTP |
| Ambari REST API | hlavních uzlech | 8080 | / api/v1 | HTTP |

Příklady:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| NameNode webového uživatelského rozhraní |hlavních uzlech |30070 |HTTPS |Webové uživatelské rozhraní k zobrazení stavu |
| Služba NameNode metadat |hlavních uzlech |8020 |IPC |Metadatech systému souborů |
| DataNode |Všechny uzly pracovního procesu |30075 |HTTPS |Webové uživatelské rozhraní zobrazit stav, protokoly a podobně. |
| DataNode |Všechny uzly pracovního procesu |30010 |&nbsp; |Přenos dat |
| DataNode |Všechny uzly pracovního procesu |30020 |IPC |Operace s metadaty |
| Sekundární NameNode |hlavních uzlech |50090 |HTTP |Kontrolní bod pro NameNode metadata |

### <a name="yarn-ports"></a>YARN porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Správce prostředků webového uživatelského rozhraní |hlavních uzlech |8088 |HTTP |Webové uživatelské rozhraní pro Resource Manager |
| Správce prostředků webového uživatelského rozhraní |hlavních uzlech |8090 |HTTPS |Webové uživatelské rozhraní pro Resource Manager |
| Rozhraní správce prostředků správce |hlavních uzlech |8141 |IPC |Pro odesílání aplikace (Hive, Pig, Hive serveru atd.) |
| Správce prostředků plánovače |hlavních uzlech |8030 |HTTP |Rozhraní pro správu |
| Rozhraní správce prostředků aplikace |hlavních uzlech |8050 |HTTP |Adresa rozhraní Správce aplikací |
| NodeManager |Všechny uzly pracovního procesu |30050 |&nbsp; |Adresa správce kontejneru |
| NodeManager webového uživatelského rozhraní |Všechny uzly pracovního procesu |30060 |HTTP |Rozhraní správce prostředků |
| Časová osa adresa |hlavních uzlech |10200 |RPC |Služba RPC služby časové osy. |
| Časová osa webového uživatelského rozhraní |hlavních uzlech |8181 |HTTP |Časová osa služby webového uživatelského rozhraní |

### <a name="hive-ports"></a>Porty Hive

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HiveServer2 |hlavních uzlech |10001 |Thrift |Služba pro připojení k Hive (Thrift/JDBC) |
| Metaúložiště Hive |hlavních uzlech |9083 |Thrift |Služby pro připojení pro metadata Hivu (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| WebHCat server |hlavních uzlech |30111 |HTTP |Webové rozhraní API nad HCatalog a další služby Hadoop |

### <a name="mapreduce-ports"></a>MapReduce porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| JobHistory |hlavních uzlech |19888 |HTTP |MapReduce JobHistory webového uživatelského rozhraní |
| JobHistory |hlavních uzlech |10020 |&nbsp; |MapReduce JobHistory serveru |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Přenosy zprostředkující mapy výstupy s vyžádáním přechodky |

### <a name="oozie"></a>Oozie

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Oozie serveru |hlavních uzlech |11000 |HTTP |Adresa URL služby Oozie |
| Oozie serveru |hlavních uzlech |11001 |HTTP |Port pro Oozie správce |

### <a name="ambari-metrics"></a>Metriky Ambari

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Časová osa (historie aplikace) |hlavních uzlech |6188 |HTTP |Časová osa služby webového uživatelského rozhraní |
| Časová osa (historie aplikace) |hlavních uzlech |30200 |RPC |Časová osa služby webového uživatelského rozhraní |

### <a name="hbase-ports"></a>Porty HBase

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| HMaster |hlavních uzlech |16000 |&nbsp; |&nbsp; |
| Informace o HMaster webového uživatelského rozhraní |hlavních uzlech |16010 |HTTP |Port pro hlavní HBase webového uživatelského rozhraní |
| Oblast serveru |Všechny uzly pracovního procesu |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, který klienti používat pro připojení k ZooKeeper |

### <a name="kafka-ports"></a>Kafka porty

| Služba | Uzly | Port | Protocol (Protokol) | Popis |
| --- | --- | --- | --- | --- |
| Zprostředkovatel |Pracovní uzly |9092 |[Kafka přenosový protokol](http://kafka.apache.org/protocol.html) |Použít pro komunikaci klienta |
| &nbsp; |Uzly zookeeper |2181 |&nbsp; |Port, který klienti používat pro připojení k Zookeeper |

### <a name="spark-ports"></a>Spark porty

| Služba | Uzly | Port | Protocol (Protokol) | Cesta URL | Popis |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift servery |hlavních uzlech |10002 |Thrift | &nbsp; | Služba pro připojení k Spark SQL (Thrift/JDBC) |
| Livy server | hlavních uzlech | 8998 | HTTP | &nbsp; | Služba pro příkazy, úlohy a aplikace |
| Poznámkový blok Jupyter | hlavních uzlech | 8001 | HTTP | &nbsp; | Web poznámkového bloku Jupyter |

Příklady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. V tomto příkladu `10.0.0.11` je IP adresa headnode, který je hostitelem služby Livy.
