---
title: Škálování velikosti clusterů – Azure HDInsight
description: Elastické škálování clusteru Apache Hadoop tak, aby odpovídal o vaší pracovní vytížení v Azure HDInsightu
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234738"
---
# <a name="scale-azure-hdinsight-clusters"></a>Škálování clusterů Azure HDInsight

HDInsight poskytuje pružnost tím, že vám možnost vertikálně navýšit kapacitu a vertikálně snížit počet pracovních uzlů ve vašich clusterech. Tato elasticita umožňuje zmenšit cluster po hodinách nebo o víkendech a rozšířit jej během špičkových obchodních požadavků.

Pokud máte pravidelné dávkové zpracování, cluster HDInsight lze škálovat několik minut před tuto operaci, takže váš cluster má dostatek paměti a výkonu procesoru. Později po dokončení zpracování a využití se opět vypne, můžete vertikálně snížit kapacitu clusteru HDInsight na méně pracovních uzlů.

Můžete škálovat cluster ručně pomocí jedné z níže uvedených metod nebo pomocí možností [automatického škálování,](hdinsight-autoscale-clusters.md) aby se systém automaticky zvětšoval nahoru a dolů v reakci na procesor, paměť a další metriky.

> [!NOTE]  
> Podporovány jsou pouze clustery s hdinsight verze 3.1.3 nebo vyšší. Pokud si nejste jisti verzí clusteru, můžete zkontrolovat stránku Vlastnosti.

## <a name="utilities-to-scale-clusters"></a>Nástroje pro škálování clusterů

Společnost Microsoft poskytuje následující nástroje pro škálování clusterů:

|Nástroj | Popis|
|---|---|
|[Modul Az PowerShellu](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -Název \<clusteru> -TargetInstanceCount \<NewSize>|
|[Modul AzureRM PowerShellu](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -Název \<clusteru název clusteru> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight změna velikosti](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) \<--resource-group \<Skupina prostředků> --name \<Název clusteru> --workernode-count NewSize>|
|[Klasické cli Azure](hdinsight-administer-use-command-line.md)|Azure HDINSIGHT cluster \<uzměnit \<velikost clusteruNázev> počet cílových instancí> |
|[Portál Azure](https://portal.azure.com)|Otevřete podokno clusteru HDInsight, v levé nabídce vyberte **velikost clusteru,** pak v podokně velikostclusteru zadejte počet pracovních uzlů a vyberte Uložit.|  

![Možnost clusteru škálování portálu Azure](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Pomocí některé z těchto metod můžete škálovat cluster HDInsight nahoru nebo dolů během několika minut.

> [!IMPORTANT]  
> * Klasické velsecí číslo Vzato azure je zastaralé a by se mělo používat jenom s klasickým modelem nasazení. Pro všechna ostatní nasazení použijte [azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Modul PowerShell AzureRM je zastaralé.  Pokud je to možné, použijte [modul Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)

## <a name="impact-of-scaling-operations"></a>Dopad operací škálování

Když **přidáte** uzly do spuštěného clusteru HDInsight (navýšit kapacitu), nebudou ovlivněny žádné čekající nebo spuštěné úlohy. Nové úlohy lze bezpečně odeslat, zatímco proces škálování je spuštěn. Pokud se operace škálování z nějakého důvodu nezdaří, selhání bude zpracována tak, aby cluster ve funkčním stavu.

Pokud **odeberete** uzly (škálování, všechny čekající nebo spuštěné úlohy se nezdaří po dokončení operace škálování. Toto selhání je způsobeno některými službami, které se restartují během procesu škálování. Existuje také riziko, že váš cluster může uvíznout v nouzovém režimu během operace ruční škálování.

Dopad změny počtu datových uzlů se liší u každého typu clusteru podporovaného službou HDInsight:

* Apache Hadoop

    Můžete bez problémů zvýšit počet pracovních uzlů v clusteru Hadoop, který je spuštěn bez dopadu na všechny čekající nebo spuštěné úlohy. Nové úlohy lze také odeslat, zatímco operace probíhá. Chyby v operaci škálování jsou řádně zpracovány tak, aby cluster je vždy ponechána ve funkčním stavu.

    Při škálování clusteru Hadoop snížením počtu datových uzlů, některé služby v clusteru jsou restartovány. Toto chování způsobí, že všechny spuštěné a čekající úlohy nezdaří při dokončení operace škálování. Po dokončení operace však můžete úlohy znovu odeslat.

* Apache HBase

    Můžete bez problémů přidávat nebo odebírat uzly do clusteru HBase, když je spuštěn. Regionální servery jsou automaticky vyváženy během několika minut po dokončení operace škálování. Regionální servery však můžete také ručně vyvážit přihlášením k hlavnímu uzlu clusteru a spuštěním následujících příkazů z okna příkazového řádku:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Další informace o používání prostředí HBase [najdete v tématu Začínáme s příkladem Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Datové uzly můžete bez problémů přidávat nebo odebírat do clusteru Storm, když je spuštěn. Po úspěšném dokončení operace škálování však budete muset znovu vyvážit topologii.

    Vyvažování lze dosáhnout dvěma způsoby:

  * Uživatelské i uživatelské i uživatelské a uživatelské
  * Nástroj rozhraní příkazového řádku (CLI)

    Další podrobnosti naleznete v [dokumentaci k Apache Storm.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Vyvážení měřítka HDInsight Storm](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Zde je příklad příkazu příkazu příkazu příkazu příkazu pro obnovení rovnováhy topologie bouře:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpečně škálovat cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Škálování clusteru pomocí spuštěné úlohy

Chcete-li se vyhnout selhání spuštěné úlohy během operace škálování, můžete zkusit tři věci:

1. Před škálováním clusteru počkejte na dokončení úloh.
1. Ručně ukončite úlohy.
1. Znovu odešlete úlohy po ukončení operace škálování.

Chcete-li zobrazit seznam čekajících a spuštěných úloh, můžete použít **ui Správce prostředků**YARN , postupujte takto:

1. Na [portálu Azure](https://portal.azure.com/)vyberte svůj cluster.  Pokyny naleznete [v tématu Seznam a zobrazení clusterů.](./hdinsight-administer-use-portal-linux.md#showClusters) Cluster se otevře na nové stránce portálu.
2. V hlavním zobrazení přejděte na **řídicí panely clusteru** > **Ambari home**. Zadejte pověření clusteru.
3. V uzdu Ambari vyberte **yarn** v seznamu služeb v levém menu.  
4. Na stránce YARN vyberte **rychlé odkazy** a najeďte na aktivní hlavní uzel a pak vyberte **UI ResourceManager**.

    ![Apache Ambari rychlé odkazy ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Můžete přímo přistupovat k uzpřešení ResourceManager pomocí `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`aplikace .

Zobrazí se seznam úloh spolu s jejich aktuálním stavem. Na snímku obrazovky je právě spuštěna jedna úloha:

![Aplikace ui ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Chcete-li ručně udat tuto spuštěnou aplikaci, spusťte následující příkaz z prostředí SSH:

```bash
yarn application -kill <application_id>
```

Například:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Uvíznutí v nouzovém režimu

Když zmenšujete kapacitu clusteru, HDInsight používá rozhraní pro správu Apache Ambari k prvnímu vyřazení dalších pracovních uzlů, které replikují své bloky HDFS do jiných online pracovních uzlů. Poté HDInsight bezpečně škáluje cluster dolů. HDFS přejde do nouzového režimu během operace škálování a má vyjít po dokončení škálování. V některých případech se však hdfs uvízne v nouzovém režimu během operace škálování z důvodu nedostatečné replikace blokování souborů.

Ve výchozím nastavení je služba `dfs.replication` HDFS konfigurována s nastavením 1, které určuje, kolik kopií každého bloku souborů je k dispozici. Každá kopie bloku souborů je uložena v jiném uzlu clusteru.

Když HDFS zjistí, že očekávaný počet blokových kopií nejsou k dispozici, HDFS přejde do nouzového režimu a Ambari generuje výstrahy. Pokud HDFS přejde do nouzového režimu pro operaci škálování, ale pak nelze ukončit nouzový režim, protože požadovaný počet uzlů nejsou zjištěny pro replikaci, cluster uvízne v nouzovém režimu.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Příklad chyb při zapnutí nouzového režimu

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Můžete zkontrolovat protokoly uzlů názvů `/var/log/hadoop/hdfs/` ze složky v blízkosti doby, kdy byl cluster uměřítka, zobrazíte, kdy vstoupil do nouzového režimu. Soubory protokolu jsou `Hadoop-hdfs-namenode-<active-headnode-name>.*`pojmenovány .

Hlavní příčinou předchozích chyb je, že Hive závisí na dočasných souborech v HDFS při spouštění dotazů. Když HDFS přejde do nouzového režimu, Hive nelze spustit dotazy, protože nelze zapisovat do HDFS. Dočasné soubory v HDFS jsou umístěny v místní jednotce připojené k jednotlivým pracovním uzlům virtuálních počítače a replikovány mezi ostatními pracovními uzly na třech replikách, minimálně.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zabránit tomu, aby se HDInsight zasekl v nouzovém režimu

Existuje několik způsobů, jak zabránit tomu, aby byl HDInsight ponechán v nouzovém režimu:

* Před škálováním HDInsight zastavte všechny úlohy Hive. Alternativně naplánujte proces škálování, abyste se vyhnuli konfliktu se spuštěnými úlohami Hive.
* Před zmenšením měřítka `tmp` ručně vyčistěte soubory odkládacích adresářů Hive v HDFS.
* Škálování hdinsight pouze na tři pracovní uzly, minimálně. Vyhněte se tak nízké, jako jeden uzel pracovního procesu.
* V případě potřeby spusťte příkaz, abyste opustili nouzový režim.

Následující části popisují tyto možnosti.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy Hive

Zastavte všechny úlohy Hive před škálování dolů na jeden pracovní uzel. Pokud je naplánováno vaše úloha, spusťte horizontální navýšení kapacity po dokončení práce Hive.

Zastavení úloh y Hive před změnou měřítka pomáhá minimalizovat počet odkládacích souborů ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ruční vyčištění odkládacích souborů Hive

Pokud Hive zanechal dočasné soubory, můžete tyto soubory před škálováním ručně vyčistit, abyste se vyhnuli nouzovému režimu.

1. Zkontrolujte, které umístění se používá pro dočasné `hive.exec.scratchdir` soubory Hive, a to tak, že se podíváte na vlastnost konfigurace. Tento parametr je `/etc/hive/conf/hive-site.xml`nastaven v rámci :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zastavit služby Hive a ujistěte se, že jsou dokončeny všechny dotazy a úlohy.
2. Seznam obsahu scratch adresáře našel výše, aby zjistili, `hdfs://mycluster/tmp/hive/` zda obsahuje nějaké soubory:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Zde je ukázkový výstup, pokud existují soubory:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Pokud víte, že Hive se s těmito soubory provádí, můžete je odebrat. Ujistěte se, že Hive nemá žádné dotazy spuštěny při pohledu na stránce ui Yarn ResourceManager.

    Příklad příkazového řádku pro odebrání souborů z HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Škálování HDInsight u tří nebo více pracovních uzlů

Pokud vaše clustery uvíznou v nouzovém režimu často při škálování dolů na méně než tři pracovní uzly a předchozí kroky nefungují, pak můžete vyhnout clusteru přechodu do nouzového režimu úplně udržováním alespoň tři pracovní uzly.

Zachování tří pracovních uzlů je nákladnější než škálování na pouze jeden pracovní uzel, ale zabrání tomu, aby se cluster zasekl v nouzovém režimu.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Škálování HDInsight dolů na jeden pracovní uzel

I v případě, že cluster je zmenšen na 1 uzel, pracovní uzel 0 bude stále přežít. Pracovní uzel 0 nelze nikdy vyřazenz provozu.

#### <a name="run-the-command-to-leave-safe-mode"></a>Spuštění příkazu opustit nouzový režim

Poslední možností je spustit příkaz opustit nouzový režim. Pokud víte, že důvod pro vstup hdfs do nouzového režimu je z důvodu podreplikace souboru Hive, můžete spustit následující příkaz opustit nouzový režim:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Škálování clusteru Apache HBase

Servery oblasti jsou automaticky vyváženy během několika minut po dokončení operace škálování. Chcete-li ručně vyvážit servery oblastí, proveďte následující kroky:

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte prostředí HBase:

    ```bash
    hbase shell
    ```

3. Pomocí následujícího příkazu můžete ručně vyvážit servery oblasti:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Další kroky

* [Automatické škálování clusterů Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md)
