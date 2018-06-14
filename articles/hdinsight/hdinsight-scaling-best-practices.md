---
title: Škálování clusteru velikosti - Azure HDInsight | Microsoft Docs
description: Škálování clusteru HDInsight na úlohu.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 8b76d7d0441a5c1c25ad17b73083ec0e4feef1fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414316"
---
# <a name="scale-hdinsight-clusters"></a>Škálování clusterů HDInsight

HDInsight poskytuje pružnost tím, že poskytuje možnost navýšit nebo snížit počet uzlů pracovního procesu v clusterech služby. To umožňuje zmenšit cluster po hodinách nebo o víkendech a rozbalte ho během ve špičce obchodní požadavky.

Například pokud máte nějaké zpracování dávky k tomu dojde jednou denně nebo jednou za měsíc, HDInsight cluster, je možné škálovat pár minut před této naplánované události tak, aby bude dostatek paměti a výpočetního výkonu procesoru. Můžete automatizovat pomocí rutiny prostředí PowerShell škálování [ `Set–AzureRmHDInsightClusterSize` ](hdinsight-administer-use-powershell.md#scale-clusters).  Později až se provádí zpracování a využití znovu výpadku, můžete škálovat dolů na míň pracovních uzlů clusteru HDInsight.

* Škálování clusteru prostřednictvím [prostředí PowerShell](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Škálování clusteru prostřednictvím [rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md):

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Škálování clusteru prostřednictvím [portál Azure](https://portal.azure.com), otevřete váš podokno clusteru HDInsight, vyberte **škálování clusteru** na nabídky na levé straně a pak v podokně škálování clusteru, zadejte počet uzlů pracovního procesu, a Vyberte Uložit.

    ![Škálování clusteru](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Pomocí některé z těchto metod, můžete škálovat clusteru HDInsight nahoru nebo dolů v rámci minut.

## <a name="scaling-impacts-on-running-jobs"></a>Škálování dopady na spuštěné úlohy

Pokud jste **přidat** uzly ke spuštěné clusteru HDInsight, nebude mít vliv všechny úlohy čekající na vyřízení nebo spuštěné. Kromě toho nové úlohy lze bezpečně odeslat při škálování proces běží. Pokud z nějakého důvodu selže operace škálování, selhání řádně zpracovává, ponechat clusteru ve funkčním stavu.

Ale pokud jsou škálování dolů tohoto clusteru **odebrání** uzly, všechny úlohy čekající na vyřízení nebo spuštěné selže po dokončení operace škálování. To je způsobeno některé služby během procesu restartování.

Chcete-li vyřešit tento problém, můžete počkat pro úlohy, které mají dokončit před škálování dolů cluster, ručně ukončení úlohy nebo znovu odeslat úlohy po operaci škálování uzavřelo.

Pokud chcete zobrazit seznam čekající na vyřízení a spuštěné úlohy, můžete použít rozhraní YARN ResourceManager, následujícím postupem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce vlevo vyberte **Procházet**, vyberte **clustery HDInsight**a potom vyberte cluster.
3. Z podokna clusteru HDInsight, vyberte **řídicí panel** v horní nabídce otevřete uživatelské rozhraní Ambari. Zadejte své přihlašovací údaje clusteru.
4. Klikněte na tlačítko **YARN** v seznamu služeb v levé nabídce. Na stránce YARN vyberte **rychlé odkazy** a pozastavte ukazatel myši nad hlavního uzlu aktivní, a klikněte na **uživatelského rozhraní ResourceManager**.

    ![ResourceManager uživatelského rozhraní](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Může přímý přístup k rozhraní ResourceManager s `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Zobrazí seznam úloh, spolu s jejich aktuálního stavu. Na snímku obrazovky je jedna úloha aktuálně spuštěna:

![Aplikace ResourceManager uživatelského rozhraní](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Ručně ukončit aplikace spuštěná, spusťte následující příkaz z prostředí SSH:

```bash
yarn application -kill <application_id>
```

Příklad:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Vyrovnává HBase cluster

Oblast servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Ručně vyvážit servery oblasti, použijte následující postup:

1. Připojte ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte prostředí HBase:

        hbase shell

3. Ručně vyvážit servery oblasti, použijte následující příkaz:

        balancer

## <a name="scale-down-implications"></a>Snížit dopad

Jak je uvedeno nahoře, jsou všechny úlohy čekající na vyřízení nebo spuštěné ukončena po dokončení škálování dolů operaci. Existují však další potenciální dopad na škálování dolů, který může nastat.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Název uzlu HDInsight zůstává v nouzovém režimu po škálování směrem dolů

![Škálování clusteru](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Pokud váš cluster dolů minimálně jeden pracovní proces uzlu zmenšit, jak je vidět na předchozím obrázku, HDFS, může zablokovat v nouzovém režimu, když pracovní uzly se restartují, z důvodu opravy nebo bezprostředně po operaci škálování.

Primární příčinou je, že Hive používá několik `scratchdir` soubory a ve výchozím nastavení předpokládá, že tři repliky každého bloku, ale existuje pouze jedna replika možné Pokud snižovat do uzlu minimální jeden pracovní proces. V důsledku toho souborů `scratchdir` stane *under-replikované*. To by mohlo způsobit HDFS zůstat v nouzovém režimu, když služba se restartuje po operaci škálování.

Když se stane vertikálně pokus o, HDInsight závisí na rozhraní Ambari správu nejprve vyřadit z provozu velmi nežádoucí pracovní uzly, které bloky jejich HDFS se replikuje do dalších uzlů pracovního procesu online, a bezpečně clusteru škálovat. HDFS přejde do nouzovém režimu během časového období údržby a má přijdete po dokončení změny velikosti. Je v tomto okamžiku že HDFS může zablokovat v nouzovém režimu.

HDFS je nakonfigurované `dfs.replication` nastavení 3. Proto bloky pomocné soubory jsou under-replikované vždy, když jsou méně než tři uzly pracovního procesu online, protože nejsou k dispozici není očekávaný tři kopie každého souboru bloku.

Můžete spustit příkaz, aby HDFS mimo nouzový režim. Například pokud víte, že, ve které nouzovém režimu je pouze Jelikož under-replikované dočasné soubory, pak můžete bezpečně ponechat nouzovém režimu. To je proto Hive dočasné pomocné soubory jsou under-replikované soubory.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Po opuštění nouzovém režimu, můžete ručně odebrat dočasné soubory, nebo počkejte Hive nakonec vyčistěte je automaticky.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Příklad chyby při nouzovém režimu je zapnutý.

* H070 se nepodařilo otevřít relaci Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **nelze vytvořit adresář**  /tmp/hive/hive / 8 819c215c - 6d 87. 4311 97c-4f0b9d2adcf0. **Název uzlu je v nouzovém režimu**. Hlášené bloky 75 potřebuje další 12 bloky k dosažení 0.9900 celkový počet bloků 87 prahovou hodnotu. Minimální číslo 0 bylo dosaženo počtu za provozu datanodes 10. Nouzový režim se vypne automaticky po bylo dosaženo prahové hodnoty.

* H100 nelze odeslat příkaz Zobrazit databází: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: připojení k hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername . internal.cloudapp.NET/1.1.1.1] se nezdařilo: **odmítl připojení**

* Není H020 může vytvořit připojení k hn0 hdisrv.servername.bx.internal.cloudapp .net: 10001: org.apache.thrift.transport.TTransportException: nebylo možné vytvořit připojení http k http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: připojení k hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] se nezdařilo: odmítl připojení: org.apache.thrift.transport.TTransportException: nebylo možné vytvořit připojení http k http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: připojení k hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] se nezdařilo: **odmítl připojení**

* Z protokolů Hive: UPOZORNIT [hlavní]: server. HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Chyba při spuštění HiveServer2 při pokusu 21, bude akci opakovat v 60 sekund java.lang.RuntimeException: Chyba při použití zásad autorizace v konfiguraci hive: org.apache.hadoop.ipc.RemoteException ( org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **nelze vytvořit adresář** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Název uzlu je v nouzovém režimu**.
    Hlášené bloky 0 potřebuje další 9 bloky k dosažení prahové hodnoty 0.9900 celkový počet bloků 9.
    Minimální číslo 0 bylo dosaženo počtu za provozu datanodes 10. **Nouzový režim se vypne automaticky po bylo dosaženo prahové hodnoty**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Můžete zkontrolovat protokol název uzlu z `/var/log/hadoop/hdfs/` složky téměř čas, kdy byl cluster škálovat zobrazíte, když je zadaný nouzovém režimu. Soubory protokolu jsou pojmenované `Hadoop-hdfs-namenode-hn0-clustername.*`.

Hlavní příčinu předchozí chyby je, že Hive závisí na dočasné soubory v HDFS při spuštění dotazů. Když HDFS zadá nouzovém režimu, nelze Hive dotazy spustit, protože nemůže zapisovat do HDFS. Dočasné soubory v HDFS jsou umístěny do místní jednotky připojené k uzlu pracovníka jednotlivé virtuální počítače a replikují mezi jinými uzly pracovního procesu na tři repliky, minimální.

`hive.exec.scratchdir` Parametr v podregistru je nakonfigurované v rámci `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Zobrazit stav a stav systému souborů HDFS

Můžete zobrazit zprávu o stavu z každého uzlu název zda uzly jsou v nouzovém režimu. Chcete-li zobrazit sestavu, SSH do každé hlavního uzlu a spusťte následující příkaz:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Nouzový režim mimo](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> `-D` Přepínač je nezbytný, protože výchozí systém souborů v prostředí HDInsight je Azure Storage nebo Azure Data Lake Store. `-D` Určuje, že se příkazy provedou proti místního systému souborů HDFS.

Potom můžete zobrazit sestavu, která zobrazuje podrobnosti o stavu HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Tento příkaz vrátí následující na cluster v pořádku, kde jsou všechny bloky replikované očekávané míry:

![Nouzový režim mimo](./media/hdinsight-scaling-best-practices/report.png)

Podporuje HDFS `fsck` příkaz Zkontrolovat konzistenci u různých souborů, například chybějící blokuje na soubor nebo under-replikované bloky. Ke spuštění `fsck` příkaz proti `scratchdir` soubory (dočasný pomocné disku):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Při spuštění v dobrém stavu systému souborů HDFS se žádné under-replikované bloky, zobrazí se výstup podobný následujícímu:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Na rozdíl od, když `fsck` příkaz spuštěn v systému souborů HDFS se některé under-replikované bloky, je výstup podobný následujícímu:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Stav HDFS se také zobrazí v uživatelském rozhraní Ambari výběrem **HDFS** službu na levé straně, nebo s `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Stav Ambari HDFS](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Může se také zobrazit jeden nebo více kritické chyby na aktivní nebo pohotovostní NameNodes. Chcete-li zobrazit stav bloky NameNode, vyberte odkaz NameNode vedle výstrahy.

![NameNode bloky stavu](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

K vyčištění pomocné soubory, které odebere bloku chyby replikace, SSH do každé hlavního uzlu a spusťte následující příkaz:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Tento příkaz můžete rozdělit Hive, pokud některé úlohy jsou pořád spuštěné.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Jak zabránit získávání zablokování v nouzovém režimu z důvodu under-replikované bloky HDInsight

Abyste zabránili HDInsight ponechána v nouzovém režimu několika způsoby:

* Zastavte všechny úlohy Hive před škálování dolů HDInsight. Můžete také naplánujte vertikálně proces, který zabrání konfliktní s spuštěných úloh Hive.
* Ručně vyčistit Hive na začátku `tmp` soubory adresáře v HDFS před škálování směrem dolů.
* Pouze snižovat HDInsight pro tři uzly pracovního procesu, minimální. Vyhněte se budete co nejnižší uzlu jeden pracovní proces.
* V případě potřeby spusťte příkaz opustit nouzovém režimu.

Následující části popisují tyto možnosti.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy Hive

Zastavte všechny úlohy Hive před škálování směrem dolů k uzlu jeden pracovní proces. Pokud vaše úlohy je naplánováno, potom spusťte vaší vertikální snížení kapacity po dokončení práce Hive.

Tím se může minimalizovat počet pomocné soubory ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ručně vyčistit pomocné soubory na Hive

Pokud Hive opustil za dočasné soubory, pak tyto soubory můžete ručně vyčistit před škálování dolů na vyhněte nouzovém režimu.

1. Zastavení služeb Hive a ujistěte se, že jsou dokončeny všechny úlohy a dotazy.

2. Vypíše obsah `hdfs://mycluster/tmp/hive/` adresáři, pokud obsahuje všechny soubory:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Tady je příklad výstupu, kdy soubory existují:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Pokud víte, že se tyto soubory se provádí Hive, můžete je odebrat. Ujistěte se, že Hive nemá žádné dotazy, s hledáním v uživatelském rozhraní Yarn ResourceManager stránky.

    Příklad příkazového řádku k odebrání HDFS soubory:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>HDInsight škálování na tři uzly pracovního procesu

Pokud získávání zablokování v nouzovém režimu trvalé potíže a předchozí kroky nejsou možnosti, pak můžete chtít vyhnout problém pouze škálování dolů tři uzly pracovního procesu. Toto nemusí být optimální, z důvodu omezení náklady, ve srovnání s škálování směrem dolů na jeden uzel. S pouze jedním uzlem pracovního procesu, nemůže však HDFS zaručit, tři repliky dat jsou k dispozici pro cluster.

#### <a name="run-the-command-to-leave-safe-mode"></a>Spuštěním příkazu nechte nouzovém režimu

Posledním způsobem je potřeba sledovat u výjimečných případech, ve kterém HDFS zadá nouzovém režimu a potom spusťte příkaz nouzovém režimu ponechte. Až zjistíte, že z důvodu HDFS zadal Nouzový režim je z důvodu soubory Hive se under-replikované, spusťte následující příkaz opustit nouzovém režimu:

* HDInsight v systému Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight v systému Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Další postup

* [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Škálování clusterů](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů HDInsight pomocí Ambari webového uživatelského rozhraní](hdinsight-hadoop-manage-ambari.md)
