---
title: Škálování clusteru velikosti – Azure HDInsight
description: Škálování clusteru HDInsight vaší úloze.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 3e664fc83fde937b26a4726f997da4c0cb4d8f8a
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407877"
---
# <a name="scale-hdinsight-clusters"></a>Škálování clusterů HDInsight

HDInsight poskytuje pružnost tím, že možnost vertikálně navýšit a snížit počet pracovních uzlů v clusterech služby. To umožňuje zmenšit cluster po hodinách, nebo o víkendech a rozbalte ho během špičky obchodními požadavky.

Například pokud máte nějaké zpracování služby batch, který se stane jednou denně nebo jednou za měsíc, clusteru HDInsight se dá škálovat pár minut před této naplánované události, bude mít dostatečnou paměť a výpočetní výkon procesoru. Škálování pomocí rutiny Powershellu můžete automatizovat [ `Set–AzureRmHDInsightClusterSize` ](hdinsight-administer-use-powershell.md#scale-clusters).  Později po dokončení zpracování a využití ocitne mimo provoz znovu, můžete vertikálně snížit kapacitu clusteru HDInsight na míň pracovních uzlů.

* Škálování clusteru prostřednictvím [Powershellu](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Škálování clusteru prostřednictvím [rozhraní příkazového řádku Azure Classic](hdinsight-administer-use-command-line.md):

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]
    
* Škálování clusteru prostřednictvím [webu Azure portal](https://portal.azure.com), otevřete podokno váš cluster HDInsight, vyberte **škálování clusteru** v nabídce vlevo a pak v podokně škálování clusteru, zadejte počet pracovních uzlů a Vyberte Uložit.

    ![Škálování clusteru](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Pomocí kteréhokoli z těchto metod, můžete škálovat svůj cluster HDInsight navýšit nebo snížit kapacitu během několika minut.

## <a name="scaling-impacts-on-running-jobs"></a>Škálování dopad na probíhající úlohy

Když jste **přidat** uzly do spuštěného clusteru HDInsight, nebudou ovlivněny žádné čekající na vyřízení nebo spuštěné úlohy. Kromě toho nové úlohy můžete bezpečně odeslat při škálování proces běží. Pokud z jakéhokoliv důvodu selže operace škálování, selhání je řádně zpracována, byste museli opustit clusteru ve funkčním stavu.

Nicméně pokud jsou škálování svůj cluster prostřednictvím **odebrání** uzly, všechny čekající na vyřízení nebo spuštěné úlohy selžou po dokončení operace škálování. To je způsobeno některé ze služeb během procesu restartování.

A tento problém vyřešit, můžete počkat na dokončení před snížení kapacity clusteru, ručně ukončit úlohy nebo opakované úlohy, které po operaci škálování uzavřel úloh.

Pokud chcete zobrazit seznam čekajících a spuštěné úlohy, můžete použít uživatelské rozhraní správce prostředků YARN, následujícím postupem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V nabídce vlevo vyberte **Procházet**vyberte **clustery HDInsight**a pak vyberte svůj cluster.
3. Z podokna clusteru HDInsight, vyberte **řídicí panel** v horní nabídce otevřete uživatelské rozhraní Ambari. Zadejte své přihlašovací údaje clusteru.
4. Klikněte na tlačítko **YARN** na seznam služeb v nabídce vlevo. Na stránce YARN vyberte **rychlé odkazy** a ukazatel myši aktivní hlavní uzel a potom klikněte na **uživatelské rozhraní správce prostředků**.

    ![Uživatelské rozhraní správce prostředků](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Může přímý přístup k uživatelské rozhraní správce prostředků s `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Zobrazí se seznam úloh, spolu s jejich aktuální stav. Na snímku obrazovky je jedna úloha aktuálně běží:

![Uživatelské rozhraní správce prostředků aplikace](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

K ukončení ručně spuštěné aplikace, spusťte následující příkaz z prostředí SSH:

```bash
yarn application -kill <application_id>
```

Příklad:

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-apache-hbase-cluster"></a>Opětovné vyvážení clusteru Apache HBase

Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut po dokončení operace škálování. Ručně vyvážit oblastní servery, postupujte následovně:

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte prostředí HBase:

        hbase shell

3. Použijte následující příkaz pro ručně servery oblasti:

        balancer

## <a name="scale-down-implications"></a>Vertikálně snížit kapacitu důsledky

Jak už bylo zmíněno dříve, všechny čekající na vyřízení nebo spuštěné úlohy budou ukončeny po dokončení operace škálování. Existují však další potenciální dopad na škálování dolů, ke kterému může dojít.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Název uzlu HDInsight zůstane v nouzovém režimu po snížení kapacity

![Škálování clusteru](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Pokud váš cluster na minimálně jeden pracovní uzel, zmenšení, jak je znázorněno na předchozím obrázku, Apache HDFS, může zablokovány v nouzovém režimu, když pracovní uzly se restartují, z důvodu opravy nebo bezprostředně po provedení této operace škálování.

Primární příčinou je, že Hive používá několik `scratchdir` soubory a ve výchozím nastavení předpokládá, že tří replik každého bloku, ale existuje pouze jedna replika možné Jestliže vertikálně snížit kapacitu k uzlu minimální jeden pracovní proces. V důsledku toho souborů `scratchdir` stát *under-replikované*. To může způsobit HDFS zůstat v nouzovém režimu, když po provedení této operace škálování se restartují služby.

Když vertikálně pokus se stane, závisí HDInsight Apache Ambari rozhraní pro správu nejprve vyřadit z provozu velmi nežádoucí pracovních uzlů, které jejich HDFS bloky replikuje do dalších uzlů pracovního procesu online, a bezpečně clusteru vertikálně snížit kapacitu. HDFS přejde v nouzovém režimu během časového období údržby a by měl pocházet po dokončení změny velikosti. V tomto okamžiku je, že HDFS můžete zablokovány v nouzovém režimu.

HDFS, nastavena `dfs.replication` nastavení 3. Proto bloky pomocné soubory jsou under-replikované pokaždé, když jsou méně než tři pracovní uzly online, protože nejsou k dispozici není očekávaný tři kopie každého souboru bloku.

Můžete spustit příkaz zpřístupnit HDFS mimo nouzový režim. Například pokud víte, že je pouze z důvodů, proč Nouzový režim zapnutý, protože dočasné soubory under-replikované, pak můžete bezpečně nechat Nouzový režim. Je to proto under-replikované soubory jsou dočasné pomocné soubory Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Po opuštění nouzovém režimu, můžete ručně odstranit dočasné soubory, nebo počkejte Hive nakonec PROČISTIT automaticky.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Příklady chyb po zapnutí Nouzový režim

* H070 nelze otevřít relaci Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Nelze vytvořit adresář** /tmp/hive/hive/819c215c-6d 87 4311 97 c 8-4f0b9d2adcf0. **Název uzlu je v nouzovém režimu**. Ohlášené bloky 75 potřebuje další 12 bloků k dosažení prahové hodnoty 0.9900 celkový počet bloků 87. Počet živých datanodes 10 dosáhl minimální číslo 0. Nouzový režim se vypne automaticky, jakmile bylo dosaženo prahové hodnoty.

* H100 nejde odeslat příkaz Zobrazit databází: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Připojte se k hn0-clustername.servername.internal.cloudapp.net:10001 [hn0 clustername.servername. internal.cloudapp.NET/1.1.1.1] se nezdařilo: **Připojení bylo odmítnuto**

* Není H020 může vytvořit připojení ke službě .net hn0 hdisrv.servername.bx.internal.cloudapp: 10001: org.apache.thrift.transport.TTransportException: Nepodařilo se vytvořit připojení http k http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Připojte se ke hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] se nezdařilo: Připojení bylo odmítnuto: org.apache.thrift.transport.TTransportException: Nepodařilo se vytvořit připojení http k http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Připojte se ke hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] se nezdařilo: **Připojení bylo odmítnuto**

* Z protokolů Hive: UPOZORNIT [main]: server. HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Chyba při spuštění serveru HiveServer2 při pokusu 21, bude akci opakovat v 60 sekundách java.lang.RuntimeException: Chyba při použití zásad autorizace na konfigurace hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Nelze vytvořit adresář** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Název uzlu je v nouzovém režimu**.
    Ohlášené bloky 0 potřebuje další bloky 9 k dosažení prahové hodnoty 0.9900 bloků celkem 9.
    Počet živých datanodes 10 dosáhl minimální číslo 0. **Nouzový režim vypnou bude automaticky po bylo dosaženo prahové hodnoty**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Můžete zkontrolovat protokoly název uzlu z `/var/log/hadoop/hdfs/` složku poblíž čas, kdy byl škálování clusteru, zobrazíte, když zadali Nouzový režim. Protokolové soubory jsou pojmenovány `Hadoop-hdfs-namenode-hn0-clustername.*`.

Původní příčinu předchozí chyby je, že Hive závisí na dočasných souborů v HDFS při spouštění dotazů. Když HDFS přejde do režimu bezpečné, Hive nelze spustit dotazy, protože nemůže ani zapisovat do rozhraní HDFS. Dočasné soubory v HDFS nacházejí v místní disk připojený k jednotlivým pracovním uzlu virtuálních počítačů a replikovat mezi jinými uzly pracovního procesu na třech replikách, minimální.

`hive.exec.scratchdir` Parametr v Hive je nakonfigurované v rámci `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Zobrazení stavu a stavu systému souborů HDFS

Můžete zobrazit zprávu o stavu z každého uzlu název, pokud chcete zjistit, jestli uzly jsou v nouzovém režimu. Chcete-li zobrazit sestavu, SSH do každého hlavnímu uzlu a spusťte následující příkaz:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Nouzový režim vypnuto](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> `-D` Přepínač je potřeba, protože je výchozí systém souborů v HDInsight, Azure Storage nebo Azure Data Lake Store. `-D` Určuje, že příkazy spouštějte místní systém souborů HDFS.

V dalším kroku můžete zobrazit sestavu, která zobrazuje podrobnosti o stavu HDFS:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Tento příkaz vrátí následující na cluster v pořádku, ve kterém se všechny bloky replikují do očekávané míry:

![Nouzový režim vypnuto](./media/hdinsight-scaling-best-practices/report.png)

Podporuje HDFS `fsck` příkaz a zkontrolujte za účelem nalezení nekonzistencí s různými soubory, například chybějící blokuje na soubor nebo under-replikované bloky. Ke spuštění `fsck` příkaz proti `scratchdir` soubory (dočasný disk pomocné):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Při spuštění v žádné under-replikované bloky v dobrém stavu systému souborů HDFS, zobrazí se výstup podobný následujícímu:

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

Naproti tomu, že pokud `fsck` spuštění příkazu v systému souborů HDFS některé under-replikované bloky, zobrazí se výstup podobný následujícímu:

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

Stav HDFS můžete také zobrazit v uživatelském rozhraní Ambari tak, že vyberete **HDFS** služby na levé straně nebo s `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Stav Ambari HDFS](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Nejmíň jedna kritická chyba může zobrazit také na aktivní nebo pohotovostní NameNodes. Chcete-li zobrazit stav blocích NameNode, vyberte odkaz NameNode vedle výstrahy.

![Stav blocích NameNode](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Vyčistit pomocné soubory, které odebere chyby replikace bloku, SSH do každého hlavního uzlu a spusťte následující příkaz:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> Tento příkaz může dojít k narušení Hive některé úlohy jsou spuštěné.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Jak zabránit získávání zablokuje a nespustí z důvodu under-replikované bloky HDInsight

Existuje několik způsobů, jak zabránit ponechán v nouzovém režimu HDInsight:

* Zastavte všechny úlohy Hive před škálování HDInsight. Můžete také naplánujte vertikálně proces, který zabrání konfliktní se spouštěním úloh Hive.
* Ručně vyčistit Hive na začátku `tmp` soubory adresáře v HDFS před škálování.
* Pouze snížit HDInsight do tří pracovních uzlů, minimální. Vyhněte se bude v rozsahu od jednoho pracovního uzlu.
* Spuštěním příkazu ukončit nouzový režim, v případě potřeby.

Následující části popisují tyto možnosti.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy Hive

Zastavte všechny úlohy Hive před vertikální snížení na jednoho pracovního uzlu. Pokud vaše úloha je naplánovaná, provádějí vaše vertikální snížení kapacity po dokončení práce Hive.

Tím se může minimalizovat počet pomocné soubory ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ručně vyčistit pomocné soubory Hive.

Pokud Hive se zachovají dočasné soubory, pak tyto soubory můžete ručně vyčistit před vertikálně snižuje kapacita na vyhněte Nouzový režim.

1. Zastavení služeb Hive a ujistěte se, že jsou dokončeny všechny dotazy a úlohy.

2. Zobrazení obsahu `hdfs://mycluster/tmp/hive/` adresáři a zjistí, pokud obsahuje všechny soubory:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Když soubory existují zde je ukázkový výstup:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Pokud víte, že se tyto soubory se provádí Hive, můžete ho odebrat. Ujistěte se, že Hive nemá žádné dotazy, s pohledem na stránce uživatelského rozhraní správce prostředků Yarn.

    Příklad příkazového řádku k odebrání souborů z HDFS:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Škálování HDInsight do tří pracovních uzlů

Pokud získávání zablokování v nouzovém režimu trvalé potížím, a v předchozích krocích nejsou možnosti, abyste zabránili problémům pomocí pouze změny měřítka až tři pracovní uzly můžete. To nemusí být optimální, z důvodu omezení nákladů ve srovnání s vertikální snížení na jeden uzel. Nicméně s pouze jedním uzlem pracovního procesu, HDFS nemůže zaručit tří replik dat jsou dostupné v clusteru.

#### <a name="run-the-command-to-leave-safe-mode"></a>Pomocí příkazu ukončit nouzový režim

Posledním způsobem je moct vzácných případech mohou oprávnění, ve kterém HDFS zadá Nouzový režim a potom spusťte příkaz nechte Nouzový režim. Jakmile určíte, že se dostala důvod HDFS Nouzový režim je z důvodu soubory Hive se under-replikované, spusťte následující příkaz, ponechte Nouzový režim:

* HDInsight v Linuxu:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight ve Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Škálování clusterů](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
