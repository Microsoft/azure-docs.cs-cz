---
title: Řešení potíží Storm pomocí Azure HDInsight | Microsoft Docs
description: Získejte odpovědi na časté otázky týkající se používání Apache Storm s Azure HDInsight.
keywords: Azure HDInsight, Storm, – nejčastější dotazy, řešení potíží s průvodce, běžné problémy
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: ''
editor: ''
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 46f07a1512435fd8ad5cae4df1858f948fe017e1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Řešení potíží Storm pomocí Azure HDInsight

Další informace o hlavních problémů a jejich řešení pro práci s Apache Storm datové části v Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak získám přístup k rozhraní Storm v clusteru?
Máte dvě možnosti pro přístup k rozhraní Storm z prohlížeče:

### <a name="ambari-ui"></a>Uživatelské rozhraní Ambari
1. Přejděte na řídicí panel Ambari.
2. V seznamu služeb vyberte **Storm**.
3. V **rychlé odkazy** nabídce vyberte možnost **uživatelské rozhraní Storm**.

### <a name="direct-link"></a>Přímý odkaz
Uživatelské rozhraní Storm na následující adrese URL se můžete dostat:

https://\<název DNS clusteru\>/stormui

Příklad:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak převést Storm událostí hub spout kontrolního bodu informace z jednoho topologie do jiného?

Když budete vyvíjet topologie, které čtou z Azure Event Hubs pomocí centra událostí HDInsight Storm spout souboru .jar, musíte nasadit topologii, která má stejný název na novém clusteru. Však musí zachovat data kontrolního bodu, která byla zapsána do Apache ZooKeeper v původním clusteru.

### <a name="where-checkpoint-data-is-stored"></a>Uložení dat kontrolního bodu
Data kontrolního bodu pro odsazení jsou uložena ve funkcích spout centra událostí v ZooKeeper v dvě kořenové cesty:
- Netransakční spout kontrolní body jsou uloženy v /eventhubspout.
- Data kontrolního bodu transakcí spout se ukládají vstupně -transakcí.

### <a name="how-to-restore"></a>Postup obnovení
Skripty a knihovny, které slouží k exportu dat mimo ZooKeeper a poté importovat data zpět do ZooKeeper s novým názvem získáte v tématu [příklady Storm v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Složku lib má .jar soubory, které obsahují implementaci pro operace exportu/importu. Složka bash obsahuje ukázkový skript, který ukazuje, jak exportovat data ze serveru ZooKeeper v původním clusteru a importujte ho na server ZooKeeper v novém clusteru.

Spustit [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skript z uzly ZooKeeper na exportovat a importovat data. Skript aktualizujte na správnou verzi Hortonworks Data Platform (HDP). (Tvrdě pracujeme na tom Obecné v prostředí HDInsight tyto skripty. Obecné skripty můžete spustit z libovolného uzlu v clusteru bez úprav uživatelem.)

Příkaz export zapíše metadata na cestu Apache Hadoop Distributed File System (HDFS) (v úložišti Azure Blob Storage nebo Azure Data Lake Store) v umístění, které nastavíte.

### <a name="examples"></a>Příklady

#### <a name="export-offset-metadata"></a>Export metadat posunutí
1. Přejděte do ZooKeeper clusteru v clusteru, ze kterého posun kontrolního bodu musí být exportován pomocí SSH.
2. Spusťte následující příkaz pro export ZooKeeper posunutí data do cesty HDFS /stormmetadta/zkdata (po aktualizaci softwaru HDP řetězec verze):

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Import metadat posunutí
1. Přejděte do ZooKeeper clusteru v clusteru, ze kterého posun kontrolního bodu musí být importovány pomocí SSH.
2. Spusťte následující příkaz pro import dat posunutí ZooKeeper z /stormmetadata/zkdata cesty HDFS ZooKeeper server v cílovém clusteru (po aktualizaci softwaru HDP řetězec verze):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Odstranit posunutí metadata tak, aby topologie spustit od začátku, nebo ze časové razítko, které uživatel vybere zpracování dat
1. Použití SSH přejdete do ZooKeeper clusteru v clusteru, ze kterého posun kontrolního bodu musí být odstraněn.
2. Spusťte následující příkaz k odstranění všech dat posunutí ZooKeeper v aktuální clusteru (po aktualizaci softwaru HDP řetězec verze):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak najít binární soubory Storm v clusteru?
Storm binární soubory pro aktuální zásobník HDP jsou /usr/hdp/current/storm-client. Umístění je stejný pro hlavních uzlech i pro uzly pracovního procesu.
 
Může existovat více binární soubory pro konkrétní verze softwaru HDP v /usr/hdp (například /usr/hdp/2.5.0.1233/storm). Složka /usr/hdp/current/storm-client je symlinked na nejnovější verzi, která běží na clusteru.

Další informace najdete v tématu [připojit ke clusteru HDInsight pomocí protokolu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak je možné zjistit topologii nasazení clusteru Storm?
Nejdřív určete všechny součásti, které jsou nainstalované s HDInsight Storm. Storm cluster se skládá ze čtyř kategorií uzlu:

* Uzly brány
* hlavních uzlech
* Uzly zooKeeper
* Pracovní uzly
 
### <a name="gateway-nodes"></a>Uzly brány
Uzel brány je služba reverzní proxy server, která umožňuje veřejný přístup k aktivní službě správy Ambari a brány. Také obstará Ambari vedoucí volba.
 
### <a name="head-nodes"></a>hlavních uzlech
Storm hlavních uzlech spusťte následující služby:
* Nimbus
* Ambari serveru
* Ambari metriky serveru
* Kolekce Ambari metriky
 
### <a name="zookeeper-nodes"></a>Uzly zooKeeper
HDInsight se dodává s třemi uzly ZooKeeper kvora. Velikost kvora je pevná a nejde ho překonfigurovat.
 
Služby Storm v clusteru jsou nakonfigurovány k automatickému využití ZooKeeper kvora.
 
### <a name="worker-nodes"></a>Pracovní uzly
Storm uzlů pracovního procesu spouštění následujících služeb:
* Dohledový uzel
* Pracovní Java virtuálních počítačů (JVMs) pro spuštění topologie
* Ambari agenta
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak najdu Storm binární soubory funkcí spout centra událostí pro vývoj
 
Další informace o pomocí topologie Storm event hub spout .jar soubory najdete v následujících zdrojích informací.
 
### <a name="java-based-topology"></a>Topologie založené na jazyce Java
[Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# – na základě topologie (Mono u clusterů Linux Storm HDInsight 3.4 +)
[Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nejnovější Storm event hub spout binární soubory pro clustery Linux Storm HDInsight 3.5 +
Naučte se používat nejnovější spout Storm události rozbočovače, který funguje s clustery Linux Storm HDInsight 3.5 +, najdete v tématu mvn úložišti [souboru readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Příklady zdrojového kódu
V tématu [příklady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) o tom, jak číst a zapisovat z centra událostí Azure pomocí topologií Apache Storm (napsanou v jazyce Java) v clusteru Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Jak najdu Storm Log4J konfigurační soubory v clusterech?
 
K identifikaci Storm služeb Apache Log4J konfigurační soubory.
 
### <a name="on-head-nodes"></a>Na hlavních uzlech
Konfigurace Nimbus Log4J je pro čtení z USR/hdp/\<verze softwaru HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>V pracovním uzlům
Supervisor Log4J konfigurace je pro čtení z USR/hdp/\<verze softwaru HDP\>/storm/log4j2/cluster.xml.
 
Konfigurační soubor pracovního procesu Log4J je načten z USR/hdp/\<verze softwaru HDP\>/storm/log4j2/worker.xml.
 
Příklady: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
