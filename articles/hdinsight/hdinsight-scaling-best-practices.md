---
title: Škálování velikosti clusterů – Azure HDInsight
description: Škálování Apache Hadoop clusteru elastické tak, aby odpovídalo vašemu zatížení v Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 3524b5d2274c52aa94fa1c3420fb0d3245d9b730
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932056"
---
# <a name="scale-azure-hdinsight-clusters"></a>Škálování clusterů Azure HDInsight

HDInsight poskytuje flexibilitu s možnostmi horizontálního navýšení a snížení kapacity počtu pracovních uzlů v clusterech. Tato pružnost umožňuje zmenšit cluster po hodinách nebo na víkendech. A rozbalíte je během špičkových obchodních požadavků.

Škálovat cluster před pravidelným zpracováním dávek, aby cluster měl dostatečné prostředky.  Po dokončení zpracování a využití se rozroste a zmenšuje cluster HDInsight dolů na méně pracovních uzlů.

Cluster můžete škálovat ručně pomocí jedné z metod popsaných níže. Možnosti automatického [škálování](hdinsight-autoscale-clusters.md) můžete použít také k automatickému horizontálnímu navýšení kapacity směrem nahoru a dolů v reakci na určité metriky.

> [!NOTE]  
> Podporují se jenom clustery se službou HDInsight verze 3.1.3 nebo vyšší. Pokud si nejste jistí, jakou verzi clusteru máte, můžete na stránce vlastností zkontrolovat.

## <a name="utilities-to-scale-clusters"></a>Nástroje pro škálování clusterů

Microsoft poskytuje následující nástroje pro škálování clusterů:

|Nástroj | Popis|
|---|---|
|[Modul Az PowerShellu](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Modul AzureRM PowerShellu](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure Classic CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure Portal](https://portal.azure.com)|Otevřete podokno cluster HDInsight, v nabídce vlevo vyberte **Velikost clusteru** a pak v podokně velikost clusteru zadejte počet pracovních uzlů a vyberte Uložit.|  

![Možnost clusteru Azure Portal Scale](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Pomocí kterékoli z těchto metod můžete škálovat cluster HDInsight během několika minut nahoru nebo dolů.

> [!IMPORTANT]  
> * Rozhraní příkazového řádku Azure Classic je zastaralé a mělo by se používat jenom s modelem nasazení Classic. Pro všechna ostatní nasazení použijte rozhraní příkazového [řádku Azure](/cli/azure/).
> * Modul PowerShell AzureRM je zastaralý.  Pokud je to možné, použijte prosím [modul AZ Module](/powershell/azure/new-azureps-module-az) .

## <a name="impact-of-scaling-operations"></a>Dopad operací škálování

Když **přidáte** uzly do spuštěného clusteru HDInsight (horizontální navýšení kapacity), úlohy nebudou ovlivněny. Nové úlohy lze bezpečně odeslat, když je spuštěn proces škálování. Pokud dojde k selhání operace škálování, při selhání zůstane cluster ve funkčním stavu.

Pokud **odeberete** uzly (horizontální snížení kapacity), nevyřízené nebo spuštěné úlohy selžou po dokončení operace škálování. Příčinou této chyby je to, že některé služby se restartují během procesu škálování. Cluster může být zablokovaný v bezpečném režimu během operace ručního škálování.

Dopad změny počtu datových uzlů se liší pro každý typ clusteru podporovaný službou HDInsight:

* Apache Hadoop

    Můžete plynule zvýšit počet pracovních uzlů v běžícím clusteru Hadoop, aniž by to ovlivnilo žádné úlohy. Nové úlohy je možné odeslat i v průběhu operace. Chyby v operaci škálování jsou řádně zpracovávány. Cluster je vždy ponechán ve funkčním stavu.

    Pokud je cluster Hadoop škálované dolů a méně datovými uzly, jsou některé služby restartovány. Toto chování způsobí, že všechny spuštěné a nedokončené úlohy selžou při dokončení operace škálování. Po dokončení operace ale můžete úlohy znovu odeslat.

* Apache HBase

    Bez problémů můžete přidat nebo odebrat uzly do clusteru HBA v době, kdy je spuštěný. Regionální servery se automaticky vyrovnávají během několika minut od dokončení operace škálování. Regionální servery ale můžete vyrovnávat ručně. Přihlaste se ke clusteru hlavnímu uzlu a spusťte následující příkazy:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Další informace o použití prostředí HBA najdete [v tématu Začínáme s příkladem Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Můžete hladce přidávat nebo odebírat datové uzly, když je spuštěná. Po úspěšném dokončení operace škálování ale budete muset topologii znovu vyvážit. Nové vyrovnávání umožňuje, aby topologie znovu nastavila [Nastavení paralelismu](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) na základě nového počtu uzlů v clusteru. Chcete-li znovu vyrovnávat spuštěné topologie, použijte jednu z následujících možností:

  * Webové uživatelské rozhraní pro vyplavení

    Pomocí následujícího postupu můžete znovu vyvážit topologii pomocí uživatelského rozhraní.

    1. Otevřete `https://CLUSTERNAME.azurehdinsight.net/stormui` ve webovém prohlížeči, kde `CLUSTERNAME` je název vašeho clusteru s více podsítěmi. Po zobrazení výzvy zadejte název správce clusteru HDInsight (správce) a heslo, které jste zadali při vytváření clusteru.

    1. Vyberte topologii, kterou chcete znovu vyvážit, a pak vyberte tlačítko pro **vyvážení** . Zadejte zpoždění před provedením operace obnovení rovnováhy.

        ![Rebilance škálování ve službě HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

  * Nástroj rozhraní příkazového řádku (CLI)

    Připojte se k serveru a použijte následující příkaz k opětovnému vyvážení topologie:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    Můžete také zadat parametry pro přepsání pomocného parametru paralelismus původně poskytnuté topologie. Například kód níže překonfiguruje `mytopology` topologii na 5 pracovních procesů, 3 vykonavatele pro komponentu Blue-Spout a 10 prováděcích modulů pro komponentu žlutého šroubu.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    Po operaci škálování byste měli znovu vyrovnávat repliky oddílů. Další informace najdete v článku o [vysoké dostupnosti dat s Apache Kafka v dokumentu HDInsight](./kafka/apache-kafka-high-availability.md) .

* Apache Hive LLAP

    Po škálování na `N` pracovní uzly bude HDInsight automaticky nastavit následující konfigurace a restartovat podregistr.

  * Maximální počet souběžných dotazů: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Počet uzlů používaných LLAP podregistru: `num_llap_nodes  = N`
  * Počet uzlů pro spuštění démona LLAP podregistru: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpečně škálovat cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Horizontální navýšení kapacity clusteru se spuštěnými úlohami

Aby se předešlo tomu, že spuštěné úlohy selžou během operace horizontálního škálování, můžete vyzkoušet tři věci:

1. Před škálováním clusteru počkejte na dokončení úloh.
1. Ručně ukončete úlohy.
1. Znovu odešle úlohy po uzavření operace škálování.

Chcete-li zobrazit seznam probíhajících a spuštěných úloh, můžete použít **uživatelské rozhraní příz správce prostředků** následujícím postupem:

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster.  Cluster se otevře na nové stránce portálu.
2. V hlavním zobrazení přejděte na **řídicí panely clusteru**  >  **Ambari domů**. Zadejte přihlašovací údaje clusteru.
3. V uživatelském rozhraní Ambari vyberte možnost **příze** v seznamu služeb v nabídce na levé straně.  
4. Na stránce PŘÍZe vyberte možnost **Rychlé odkazy** a najeďte myší na aktivní hlavní uzel a pak vyberte **Správce prostředků uživatelské rozhraní**.

    ![Rychlé odkazy na Apache Ambari Správce prostředků uživatelské rozhraní](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

K uživatelskému rozhraní Správce prostředků můžete přistupovat přímo pomocí `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Zobrazí se seznam úloh spolu s jejich aktuálním stavem. Na snímku obrazovky je aktuálně spuštěná jedna úloha:

![Správce prostředků aplikace uživatelského rozhraní](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Pokud chcete tuto spuštěnou aplikaci odstranit ručně, spusťte v prostředí SSH následující příkaz:

```bash
yarn application -kill <application_id>
```

Příklad:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Zablokování v bezpečném režimu

Při horizontálním navýšení kapacity clusteru používá HDInsight rozhraní pro správu Apache Ambari a nejdřív vyřadí další pracovní uzly. Uzly replikují své bloky HDFS do jiných online pracovních uzlů. Služba HDInsight pak cluster bezpečně škáluje. HDFS přejde do bezpečného režimu během operace škálování. HDFS by se mělo vycházet po dokončení škálování. V některých případech se ale HDFS zablokuje v bezpečném režimu během operace škálování z důvodu blokování souborů v rámci replikace.

Ve výchozím nastavení je HDFS nakonfigurován s `dfs.replication` nastavením 1, které určuje, kolik kopií každého bloku souboru je k dispozici. Každá kopie bloku souboru je uložená v jiném uzlu clusteru.

Pokud není k dispozici očekávaný počet kopií bloku, HDFS vstoupí do bezpečného režimu a Ambari vygeneruje výstrahy. HDFS může pro operaci škálování přejít do bezpečného režimu. Cluster se může zablokovat v bezpečném režimu, pokud není zjištěn požadovaný počet uzlů pro replikaci.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Ukázkové chyby, když je zapnutý režim zabezpečení

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

V případě, že se v clusteru změnila velikost, můžete zkontrolovat protokoly uzlů názvů z této `/var/log/hadoop/hdfs/` složky, aby se zjistilo, kdy vstoupila do bezpečného režimu. Soubory protokolu jsou pojmenovány `Hadoop-hdfs-namenode-<active-headnode-name>.*` .

Hlavní příčinou bylo, že při spouštění dotazů závisí podregistr na dočasné soubory v HDFS. Když HDFS vstoupí do bezpečného režimu, podregistr nemůže spustit dotazy, protože nemůže zapisovat do HDFS. Dočasné soubory v HDFS jsou umístěné na místní jednotce připojené k virtuálním počítačům jednotlivých pracovních uzlů. Soubory jsou replikovány mezi ostatními pracovními uzly ve třech replikách, minimum.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zabránit službě HDInsight v tom, aby se zablokovala v bezpečném režimu

Existuje několik způsobů, jak zabránit v tom, aby HDInsight zůstalo v bezpečném režimu:

* Před škálováním HDInsight zastavte všechny úlohy podregistru. Případně můžete naplánovat proces horizontálního navýšení kapacity, aby nedocházelo ke konfliktům s běžícími úlohami.
* Před horizontálním navýšení kapacity je nutné ručně vyčistit soubory odkládacího adresáře podregistru `tmp` v HDFS.
* Nahorizontální navýšení kapacity HDInsight na tři pracovní uzly, minimální. Vyhněte se nedostatku na jeden pracovní uzel.
* Spusťte příkaz a v případě potřeby ponechte nouzový režim.

Tyto možnosti jsou popsány v následujících částech.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy podregistru

Před škálováním dolů na jeden pracovní uzel zastavte všechny úlohy podregistru. Pokud je naplánováno vaše úlohy, po dokončení práce na podregistru proveďte své škálování.

Zastavení úloh podregistru před škálováním pomůže minimalizovat počet pomocných souborů ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ruční vyčištění pomocného souboru podregistru

Pokud podregistr opustí dočasné soubory, můžete tyto soubory před horizontálním škálováním ručně vyčistit a vyhnout se bezpečnému režimu.

1. Podívejte se na vlastnost konfigurace a ověřte, které umístění se používá pro dočasné soubory podregistru `hive.exec.scratchdir` . Tento parametr je nastaven v rámci `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zastavte služby podregistru a ujistěte se, že jsou dokončené všechny dotazy a úlohy.

1. Vypíše obsah pomocného adresáře, který se našel výše, `hdfs://mycluster/tmp/hive/` aby se zjistilo, jestli obsahuje nějaké soubory:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Zde je ukázkový výstup, když soubory existují:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Pokud víte, že je podregistr s těmito soubory hotový, můžete je odebrat. Ujistěte se, že v podregistru nejsou spuštěné žádné dotazy, a to tak, že na stránce Správce prostředků uživatelské rozhraní příze.

    Příklad příkazového řádku pro odebrání souborů z HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Škálování HDInsight na tři nebo více pracovních uzlů

Pokud se vaše clustery zablokují v bezpečném režimu často, když se škáluje na méně než tři pracovní uzly, zachovejte aspoň tři pracovní uzly.

Mají tři pracovní uzly dražší než horizontální snížení kapacity jenom na jeden pracovní uzel. Tato akce ale zabrání tomu, aby se váš cluster dostal do bezpečného režimu.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Škálování HDInsight dolů na jeden pracovní uzel

I když se cluster rozškáluje na jeden uzel, pracovní uzel 0 se pořád zachová. Pracovní uzel 0 nelze nikdy vyřadit z provozu.

#### <a name="run-the-command-to-leave-safe-mode"></a>Spusťte příkaz pro opuštění nouzového režimu.

Poslední možností je spustit příkaz opustit režim nouze. Pokud HDFS vstoupila do bezpečného režimu z důvodu souboru podreplikace, spusťte následující příkaz pro opuštění nouzového režimu:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Horizontální navýšení kapacity clusteru Apache HBA

Servery oblastí se po dokončení operace škálování automaticky vyrovnávají během několika minut. Chcete-li ručně vyrovnávat servery oblastí, proveďte následující kroky:

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte prostředí HBA:

    ```bash
    hbase shell
    ```

3. Chcete-li ručně vyrovnávat servery oblastí, použijte následující příkaz:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Další kroky

* [Automatické škálování clusterů Azure HDInsight](hdinsight-autoscale-clusters.md)

Konkrétní informace o škálování clusteru HDInsight najdete v těchto tématech:

* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí rozhraní příkazového řádku Azure](hdinsight-administer-use-command-line.md#scale-clusters)