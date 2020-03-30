---
title: Poradce při potížích s bouří pomocí Azure HDInsight
description: Získejte odpovědi na časté otázky týkající se používání Apache Storm u Azure HDInsight.
keywords: Azure HDInsight, Storm, nejčastější dotazy, průvodce odstraňováním problémů, běžné problémy
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271925"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Poradce při potížích s Apache Storm pomocí Azure HDInsight

Seznamte se s nejlepšími problémy a jejich předsevzetími pro práci s datovými zatíženími [Apache Storm](https://storm.apache.org/) v [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak se dostanu k ui storm u clusteru?

Máte dvě možnosti pro přístup k storm uznané z prohlížeče:

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. Přejděte na řídicí panel Ambari.
2. V seznamu služeb vyberte **Storm**.
3. V nabídce **Rychlé odkazy** vyberte **Storm UI**.

### <a name="direct-link"></a>Přímý odkaz

K uzlina Storm můžete přistupovat na následující adrese URL:

`https://<cluster DNS name>/stormui`

Příklad: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak přenesu informace o kontrolním bodu centra událostí Storm z jedné topologie do druhé?

Při vývoji topologie, které čtou z Centra událostí Azure pomocí hubu událostí HDInsight Storm hub .jar soubor, musíte nasadit topologii, která má stejný název v novém clusteru. Je však nutné zachovat data kontrolního bodu, která byla potvrzena [Apache ZooKeeper](https://zookeeper.apache.org/) na staré clusteru.

### <a name="where-checkpoint-data-is-stored"></a>Kde jsou uložena data kontrolního bodu

Data kontrolního bodu pro posuny jsou uložena výtokem centra událostí v ZooKeeper ve dvou kořenových cestách:

- V aplikaci jsou uloženy `/eventhubspout`netransakční výtokové body.

- Data kontrolního bodu transakční `/transactional`výtok je uložena v .

### <a name="how-to-restore"></a>Jak obnovit

Chcete-li získat skripty a knihovny, které používáte k exportu dat ze ZooKeeper a potom importovat data zpět do ZooKeeper s novým názvem, viz [PŘÍKLADY HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Složka lib obsahuje soubory .jar, které obsahují implementaci pro operaci exportu a importu. Složka bash má ukázkový skript, který ukazuje, jak exportovat data ze serveru ZooKeeper ve starém clusteru a potom je importovat zpět na server ZooKeeper v novém clusteru.

Spusťte [skript stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z uzlů ZooKeeper pro export a import dat. Aktualizujte skript na správnou verzi Hortonworks Data Platform (HDP). (Pracujeme na tom, aby tyto skripty obecné v HDInsight. Obecné skripty lze spustit z libovolného uzlu v clusteru bez úprav uživatelem.)

Příkaz exportu zapíše metadata do cesty distribuovaného souborového systému Apache Hadoop (HDFS) (v azure blob storage nebo Azure Data Lake Storage) v umístění, které nastavíte.

### <a name="examples"></a>Příklady

#### <a name="export-offset-metadata"></a>Exportovat metadata posunu

1. Pomocí SSH přejděte do clusteru ZooKeeper v clusteru, ze kterého je třeba exportovat posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) `/stormmetadta/zkdata` pro export dat posunu ZooKeeper do cesty HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importovat odsazená metadata

1. Pomocí SSH přejděte do clusteru ZooKeeper v clusteru, ze kterého je třeba importovat posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) importujte data `/stormmetadata/zkdata` posunu ZooKeeper z cesty HDFS na server ZooKeeper v cílovém clusteru:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Odstranit metadata posunu tak, aby topologií můžete spustit zpracování dat od začátku, nebo z časového razítka, které uživatel zvolí

1. Pomocí SSH přejděte do clusteru ZooKeeper v clusteru, ze kterého je třeba odstranit posun kontrolního bodu.
2. Spusťte následující příkaz (po aktualizaci řetězce verze HDP) a odstraňte všechna data posunu ZooKeeper v aktuálním clusteru:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak lze v clusteru vyhledat binární soubory Storm?

Binární soubory stormu pro aktuální `/usr/hdp/current/storm-client`zásobník HDP jsou v . Umístění je stejné jak pro hlavní uzly, tak pro pracovní uzly.

V /usr/hdp `/usr/hdp/2.5.0.1233/storm`(například). Složka `/usr/hdp/current/storm-client` je symlinked na nejnovější verzi, která je spuštěna v clusteru.

Další informace najdete [v tématu Připojení ke clusteru HDInsight pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak zjistím topologii nasazení clusteru Storm?

Nejprve identifikujte všechny součásti, které jsou nainstalovány pomocí technologie HDInsight Storm. Cluster Storm se skládá ze čtyř kategorií uzlů:

* Uzly brány
* Hlavní uzly
* Uzly ZooKeeper
* Pracovní uzly

### <a name="gateway-nodes"></a>Uzly brány

Uzel brány je služba brány a reverzního proxy serveru, která umožňuje veřejný přístup k aktivní službě správy Ambari. To také zvládá Ambari vůdce voleb.

### <a name="head-nodes"></a>Hlavní uzly

Hlavní uzly Storm spouštějí následující služby:
* Nimbus
* Server Ambari
* Server Ambari Metrics
* Sběratel metrik Ambari
 
### <a name="zookeeper-nodes"></a>Uzly ZooKeeper

HDInsight je dodáván s kvorem ZooKeeper se třemi uzlemi. Velikost kvora je pevná a nelze ji překonfigurovat.

Storm služby v clusteru jsou nakonfigurovány tak, aby automaticky používaly kvorum ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Uzly pracovníka bouře spustit následující služby:
* Supervisor
* Virtuální počítače Worker Java (JVMs), pro spuštění topologie
* Ambari agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak najdu binární soubory výtoku hubů událostí Storm pro vývoj?

Další informace o používání souborů hubu událostí Storm .jar s topologii najdete v následujících zdrojích.

### <a name="java-based-topology"></a>Topologie založená na javě

[Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologie založená na jazyce C (Mono na clusterech HDInsight 3.4+ Linux Storm)

[Zpracování událostí z Centra událostí Azure pomocí Apache Storm na HDInsightu (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Nejnovější binární soubory centra událostí Apache Storm pro clustery HDInsight 3.5+ Linux Storm

Chcete-li se dozvědět, jak používat nejnovější hub událostí Storm, který funguje s clustery HDInsight 3.5+ Linux Storm, podívejte se na [soubor readme mvn-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Příklady zdrojového kódu

Podívejte se na [příklady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) čtení a zápisu z Azure Event Hub pomocí topologie Apache Storm (napsané v Jazyce Java) v clusteru Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Jak najdu konfigurační soubory Storm Log4J 2 v clusterech?

K identifikaci konfiguračních souborů [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) pro služby Storm.

### <a name="on-head-nodes"></a>Na hlavové uzly

Konfigurace Nimbus Log4J je `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`čtena z aplikace .

### <a name="on-worker-nodes"></a>Na pracovních uzlech

Konfigurace vedoucího Log4J `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`je čtena z .

Pracovní konfigurační soubor Log4J je přečten z aplikace `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Příklady:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Není výjimkou vedoucího

Při odesílání topologie se uživateli může zobrazit chybová zpráva podobná: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Chcete-li vyřešit, uživatel může být nutné soubor lístek mít uzly restartován nebo restartován. Další informace naleznete [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)v tématu .

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

- Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

- Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
