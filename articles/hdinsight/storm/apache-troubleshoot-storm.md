---
title: Řešení potíží s využitím Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se použití Apache Storm se službou Azure HDInsight.
keywords: Azure HDInsight, vyplavení, nejčastější dotazy, Průvodce odstraňováním potíží, běžné problémy
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946310"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Řešení potíží s Apache Storm pomocí Azure HDInsight

Přečtěte si o hlavních problémech a jejich řešeních pro práci s [Apache Stormmi](https://storm.apache.org/) datovými částmi v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Návody přístup k uživatelskému rozhraní pro zaplavení v clusteru?

Máte dvě možnosti pro přístup k uživatelskému rozhraní s více podmnožinami z prohlížeče:

### <a name="apache-ambari-ui"></a>Uživatelské rozhraní Apache Ambari

1. Přejít na řídicí panel Ambari
2. V seznamu **služeb vyberte možnost** zaplavit.
3. V nabídce **Rychlé odkazy** vyberte **uživatelské rozhraní** pro zaplavení.

### <a name="direct-link"></a>Přímý odkaz

K uživatelskému rozhraní zaplavení máte přístup na následující adrese URL:

`https://<cluster DNS name>/stormui`

Příklad: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Návody přenést informace o kontrolním bodu centra událostí Spout z jedné topologie do druhé?

Když vyvíjíte topologie, které se čtou z Azure Event Hubs pomocí souboru Spout. jar centra událostí HDInsight, musíte nasadit topologii, která má stejný název v novém clusteru. Je však nutné zachovat data kontrolního bodu, která byla potvrzena k [Apache Zookeeper](https://zookeeper.apache.org/) v původním clusteru.

### <a name="where-checkpoint-data-is-stored"></a>Kde jsou uložená data kontrolního bodu

Data kontrolního bodu pro posuny se ukládají do centra událostí Spout v ZooKeeper ve dvou kořenových cestách:

- Kontrolní body netransakčního Spout jsou uloženy v `/eventhubspout` .

- Data kontrolního bodu transakčního Spout se ukládají v `/transactional` .

### <a name="how-to-restore"></a>Postup obnovení

Pokud chcete získat skripty a knihovny, které slouží k exportu dat z ZooKeeper, a pak data importovat zpět do ZooKeeper s novým názvem, přečtěte si [Příklady HDInsight pro HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Složka lib obsahuje soubory. jar, které obsahují implementaci pro operaci exportu/importu. Složka bash obsahuje ukázkový skript, který ukazuje, jak exportovat data ze serveru ZooKeeper v původním clusteru a pak ji znovu naimportovat zpět na server ZooKeeper v novém clusteru.

Spusťte skript [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z uzlů Zookeeper pro export a import dat. Aktualizujte skript na správnou verzi Hortonworks data Platform (HDP). (Pracujeme na tom, aby tyto skripty byly Obecné v HDInsight. Obecné skripty lze spustit z libovolného uzlu v clusteru bez úprav uživatelem.)

Příkaz pro export zapíše metadata do cesty Apache Hadoop systém souborů DFS (Distributed File System) (HDFS) (v Azure Blob Storage nebo Azure Data Lake Storage) do umístění, které jste nastavili.

### <a name="examples"></a>Příklady

#### <a name="export-offset-metadata"></a>Exportovat metadata posunutí

1. Pomocí SSH můžete přejít do clusteru ZooKeeper v clusteru, ze kterého se musí exportovat posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) exportujte data posunutí ZooKeeper na `/stormmetadta/zkdata` cestu HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importovat metadata posunutí

1. Pomocí SSH můžete přejít do clusteru ZooKeeper v clusteru, ze kterého se musí naimportovat posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) importujte ZooKeeper posunutí data z cesty HDFS `/stormmetadata/zkdata` na server Zookeeper v cílovém clusteru:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Odstraní metadata posunutí, aby topologie mohly začít zpracovávat data od začátku, nebo z časového razítka, které uživatel zvolí.

1. Pomocí SSH můžete přejít do clusteru ZooKeeper v clusteru, ze kterého se musí odstranit posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) odstraňte všechna data posunutí ZooKeeper v aktuálním clusteru:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Návody najít binární soubory s více podmnožinami v clusteru?

Binární soubory pro aktuální HDP zásobník jsou v `/usr/hdp/current/storm-client` . Umístění je stejné pro hlavní uzly i pro pracovní uzly.

Pro konkrétní verze HDP v/usr/HDP může existovat více binárních souborů (například `/usr/hdp/2.5.0.1233/storm` ). `/usr/hdp/current/storm-client`Složka je symlinked na nejnovější verzi, která je spuštěna v clusteru.

Další informace najdete v tématu [připojení ke clusteru HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) a [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Návody určit topologii nasazení clusteru se zaplavou?

Nejprve identifikujte všechny součásti, které jsou nainstalovány se službou HDInsight. Cluster pro zaplavení se skládá ze čtyř kategorií uzlů:

* Uzly brány
* Hlavní uzly
* Uzly ZooKeeper
* Pracovní uzly

### <a name="gateway-nodes"></a>Uzly brány

Uzel brány je brána a reverzní proxy služba, která umožňuje veřejný přístup ke službě Active Ambari Management Service. Také zpracovává Ambari vedoucího procesu.

### <a name="head-nodes"></a>Hlavní uzly

Hlavní uzly pro zaplavování spouštějí následující služby:
* Nimbus
* Server Ambari
* Server metrik Ambari
* Kolektor metrik Ambari
 
### <a name="zookeeper-nodes"></a>Uzly ZooKeeper

HDInsight přináší ZooKeeper kvorum se třemi uzly. Velikost kvora je pevná a nelze ji překonfigurovat.

Služby pro zaplavení v clusteru jsou nakonfigurovány tak, aby automaticky používaly kvorum ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

V pracovních uzlech s více uzly spusťte následující služby:
* Supervisor
* Virtuální počítače Java Worker (JVMs) pro spuštěné topologie
* Agent Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Návody najít binární soubory Spout centra událostí pro vývoj?

Další informace o použití souborů. jar centra událostí Spout s vaší topologií najdete v následujících zdrojích.

### <a name="java-based-topology"></a>Topologie založená na jazyce Java

[Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologie založená na C# (mono v clusterech HDInsight 3.4 + Linux)

[Zpracování událostí z Azure Event Hubs s využitím Apache Storm v HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nejnovější Apache Storm binární soubory Spout centra událostí pro clustery HDInsight 3.5 + Linux

Další informace o tom, jak používat nejnovější Spout centra událostí, která funguje s clustery HDInsight 3.5 + Linux, najdete v [souboru Readme pro MVN-úložiště](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Příklady zdrojového kódu

Podívejte se na [Příklady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) čtení a zápisu z centra událostí azure pomocí Apache Storm topologie (napsané v jazyce Java) v clusteru Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Návody najít v clusterech konfigurační soubory s nastavením Log4J 2?

Identifikujte konfigurační soubory [Apache log4j 2](https://logging.apache.org/log4j/2.x/) pro služby pro zaplavení.

### <a name="on-head-nodes"></a>U hlavních uzlů

Konfigurace Nimbus Log4J je čtena z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>V pracovních uzlech

Konfigurace Log4J správce je načítána z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

Konfigurační soubor Work Log4J je načten z `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

4.6 `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Není výjimkou vedoucího procesu.

Při odesílání topologie může uživatel obdržet chybovou zprávu podobnou této: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

Aby uživatel mohl řešení vyřešit, může být potřeba, aby si museli pořídit lístek, aby se uzly restartovaly nebo restartovaly. Další informace najdete na webu [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

- Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).