---
title: Řešení Storm pomocí Azure HDInsight
description: Získejte odpovědi na běžné dotazy týkající se použití Apache Storm pomocí Azure HDInsight.
keywords: HDInsight, Storm, nejčastější dotazy k Azure, průvodce, běžné problémy s řešením problémů
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 99ceeea33d3e2d9af798d5eb4161b0c16afc952d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011370"
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Řešení Storm pomocí Azure HDInsight

Přečtěte si nejčastější problémy a jejich řešení pro práci s datovými částmi Apache Storm v Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak získám přístup do uživatelského rozhraní Storm v clusteru?
Máte dvě možnosti pro přístup k rozhraní Storm z prohlížeče:

### <a name="ambari-ui"></a>Uživatelské rozhraní Ambari
1. Přejdete na řídicí panel Ambari.
2. V seznamu služeb vyberte **Storm**.
3. V **rychlé odkazy** nabídce vyberte možnost **uživatelské rozhraní Storm**.

### <a name="direct-link"></a>Přímý odkaz
Je možné otevřít uživatelské rozhraní Storm na následující adrese URL:

https://\<název DNS clusteru\>/stormui

Příklad:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak přenesu o kontrolním bodu spoutu centra událostí Stormu z jedné topologie do jiného?

Při vývoji topologií, které čtou z Azure Event Hubs pomocí centra událostí Stormu v HDInsight spout soubor .jar, je nutné nasadit topologii, která má stejný název v novém clusteru. Musí však zachovat data kontrolního bodu, která byla potvrzena pro Apache ZooKeeper v původním clusteru.

### <a name="where-checkpoint-data-is-stored"></a>Uložení dat kontrolního bodu
Data kontrolního bodu pro posunutí neukládají spoutu centra událostí v ZooKeeper v dvě kořenové cesty:
- Netransakční spout kontrolní body jsou uloženy v /eventhubspout.
- Transakční spout kontrolního bodu data se ukládají v / transakční.

### <a name="how-to-restore"></a>Postup při obnovení
Pokud chcete získat skripty a knihoven, které můžete použít k exportu dat mimo ZooKeeper a potom importovat data zpět do ZooKeeper s novým názvem, naleznete v tématu [příklady použití Stormu v HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib – složka obsahuje soubory .jar, které obsahují implementaci pro operace exportu/importu. Bash složka obsahuje ukázkový skript, který ukazuje, jak exportovat data ze serveru ZooKeeper v původním clusteru a potom naimportovat zpět na server ZooKeeper v novém clusteru.

Spustit [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skript z uzlu ZooKeeper, abyste exportovat a importovat data. Aktualizujte skript na správnou verzi Hortonworks Data Platform (HDP). (Kterých pracujeme na tom, aby tyto skripty Obecné v HDInsight. Obecný skripty můžete spustit z libovolného uzlu v clusteru bez úprav uživatele.)

Příkaz export zapíše metadata do cesty Apache Hadoop Distributed File System (HDFS) (v úložišti Azure Blob Storage nebo Azure Data Lake Store) do umístění, které jste nastavili.

### <a name="examples"></a>Příklady

#### <a name="export-offset-metadata"></a>Export posunu metadat
1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být exportovány pomocí SSH.
2. Spusťte následující příkaz pro export ZooKeeper posunu data do cesty HDFS /stormmetadta/zkdata (po aktualizaci verze řetězce HDP):

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Import metadat posunutí
1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být importovány pomocí SSH.
2. Spusťte následující příkaz k importu dat posunu ZooKeeper z /stormmetadata/zkdata cesty HDFS ZooKeeper server v cílovém clusteru (po aktualizaci verze řetězce HDP):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Odstranit posunu metadata tak, aby topologie začít od začátku nebo od časové razítko, které uživatel vybere možnost zpracování dat
1. Přejděte do clusteru ZooKeeper v clusteru, ze kterého posun kontrolního bodu musí být odstraněny pomocí SSH.
2. Spusťte následující příkaz k odstranění všech dat posunu ZooKeeper v aktuálním clusteru (po aktualizaci verze řetězce HDP):

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak najít binární soubory Stormu v clusteru?
Binární soubory stormu pro aktuální zásobník HDP jsou /usr/hdp/current/storm-client. Umístění je stejný pro hlavní uzly i pro pracovní uzly.
 
Může existovat více binárních souborů pro konkrétní verze HDP v /usr/hdp (například /usr/hdp/2.5.0.1233/storm). Složka /usr/hdp/current/storm-client je symlinked na nejnovější verzi, která běží na clusteru.

Další informace najdete v tématu [připojení ke clusteru HDInsight pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak zjistím topologie nasazení clusteru Storm?
Nejprve Identifikujte všechny součásti, které se instalují s HDInsight Storm. Storm cluster se skládá z čtyři kategorie uzlu:

* Uzly brány
* Hlavní uzly
* Uzly zooKeeper
* Pracovní uzly
 
### <a name="gateway-nodes"></a>Uzly brány
Uzel brány je brány a reverzní proxy server služby, která umožní veřejný přístup ke službě active management Ambari. Také obstará Ambari volba vedoucího procesu.
 
### <a name="head-nodes"></a>Hlavní uzly
Hlavní uzly Storm spusťte tyto služby:
* Nimbus
* Ambari server
* Ambari metrik serveru
* Metriky Ambari kolekcí
 
### <a name="zookeeper-nodes"></a>Uzly zooKeeper
HDInsight se dodává s třemi uzly ZooKeeper kvora. Velikost kvora je pevná a nedá se změnit.
 
Automaticky použít kvorum ZooKeeper jsou nakonfigurované služby Storm v clusteru.
 
### <a name="worker-nodes"></a>Pracovní uzly
Pracovní uzly Storm spusťte tyto služby:
* Dohledový uzel
* Pracovního procesu Java virtual machines (JVMs) pro topologií
* Ambari agent
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Kde najdu Storm binární soubory spoutů centra událostí pro vývoj?
 
Další informace o používání soubory .jar spoutu centra událostí Stormu s vaší topologii najdete v následující prostředky.
 
### <a name="java-based-topology"></a>Topologie založené na jazyce Java
[Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# – na základě topologie (Mono na clustery Linux Storm HDInsight 3.4 +)
[Zpracování událostí z Azure Event Hubs se Stormem v HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nejnovější centra událostí Stormu spout binárních souborů pro clustery HDInsight 3.5 + Linux Storm
Další informace o použití spout event hub nejnovější Storm pracuje s clustery HDInsight 3.5 + Linux Storm, najdete v úložišti mvn [souboru readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Příklady zdrojového kódu
Zobrazit [příklady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) o tom, jak číst a zapisovat z Azure Event Hubs pomocí topologie Apache Storm (napsané v jazyce Java) v clusteru Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Kde najdu soubory konfigurace Storm Log4J v clusterech?
 
K identifikaci Apache Log4J konfigurační soubory pro Storm služby.
 
### <a name="on-head-nodes"></a>Na hlavní uzly.
Konfigurace Nimbus Log4J je pro čtení z/USR/hdp/\<HDP verze\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Na pracovní uzly
Konfigurace správce Log4J se číst z/USR/hdp/\<HDP verze\>/storm/log4j2/cluster.xml.
 
Konfigurační soubor pracovního procesu Log4J se číst z/USR/hdp/\<HDP verze\>/storm/log4j2/worker.xml.
 
Příklady: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Viz také
[Řešení potíží pomocí Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
