---
title: Škálování velikosti clusterů – Azure HDInsight
description: Škálování Apache Hadoop clusteru elastické tak, aby odpovídalo vašemu zatížení v Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: dad796c8a7a34a782a4f78260ac38bd966eddde9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105401"
---
# <a name="scale-azure-hdinsight-clusters"></a>Škálování clusterů Azure HDInsight

Služba HDInsight poskytuje pružnost díky možnosti horizontálního navýšení a snížení kapacity počtu pracovních uzlů ve vašich clusterech. Tato flexibilita umožňuje zmenšit cluster po hodinách nebo na víkendech a rozšířit ho během špičkových obchodních požadavků.

Pokud máte periodické dávkové zpracování, cluster HDInsight se dá škálovat několik minut před touto operací, takže cluster má dostatečnou kapacitu paměti a procesoru.  Později až se zpracování dokončí a využití se znovu rozroste, můžete cluster HDInsight škálovat dolů na méně pracovních uzlů.

Cluster můžete škálovat ručně pomocí jedné z metod popsaných níže nebo pomocí možností automatického [škálování](hdinsight-autoscale-clusters.md) nastavit systém tak, aby se v reakci na procesor, paměť a další metriky automaticky škálovat a dolů.

> [!NOTE]  
> Podporují se jenom clustery se službou HDInsight verze 3.1.3 nebo vyšší. Pokud si nejste jistí, jakou verzi clusteru máte, můžete na stránce vlastností zkontrolovat.

## <a name="utilities-to-scale-clusters"></a>Nástroje pro škálování clusterů

Microsoft poskytuje následující nástroje pro škálování clusterů:

|Spuštění | Popis|
|---|---|
|[PowerShell AZ](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -název_clusteru \<cluster Name >-TargetInstanceCount \<NewSize >|
|[AzureRM PowerShellu](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -název_clusteru \<cluster Name >-TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [AZ HDInsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --Resource-Group \<Group >--name \<název_clusteru >--target-instance-Count \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|cluster Azure HDInsight změnil \<velikost clusteru > \<cílový počet instancí > |
|[Azure Portal](https://portal.azure.com)|Otevřete podokno cluster HDInsight, v nabídce vlevo vyberte **Velikost clusteru** a pak v podokně velikost clusteru zadejte počet pracovních uzlů a vyberte Uložit.|  

![Možnost clusteru Azure Portal Scale](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Pomocí kterékoli z těchto metod můžete škálovat cluster HDInsight během několika minut nahoru nebo dolů.

> [!IMPORTANT]  
> * Rozhraní příkazového řádku Classic Azure je zastaralé a mělo by se používat jenom s modelem nasazení Classic. Pro všechna ostatní nasazení použijte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Modul PowerShell AzureRM je zastaralý.  Pokud je to možné, použijte prosím [modul AZ Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) .

## <a name="impact-of-scaling-operations"></a>Dopad operací škálování

Když **přidáte** uzly do spuštěného clusteru HDInsight (horizontální navýšení kapacity), nebudou ovlivněny všechny probíhající nebo spuštěné úlohy. Nové úlohy lze bezpečně odeslat, když je spuštěn proces škálování. Pokud z nějakého důvodu selže operace škálování, bude chyba zpracována, aby váš cluster zůstal ve funkčním stavu.

Při **Odebrání** uzlů (horizontální navýšení kapacity) dojde k selhání všech probíhajících nebo spuštěných úloh po dokončení operace škálování. Tato chyba je způsobena tím, že se některé služby restartují během procesu škálování. Existuje také riziko, že váš cluster může během operace ručního škálování získat zablokování v nouzovém režimu.

Dopad změny počtu datových uzlů se liší pro každý typ clusteru podporovaný službou HDInsight:

* Apache Hadoop

    Můžete bez problémů zvýšit počet pracovních uzlů v clusteru Hadoop, který je spuštěný, aniž by to ovlivnilo nedokončené nebo spuštěné úlohy. Nové úlohy je možné odeslat i v průběhu operace. Selhání operace škálování se řádným způsobem zpracovávají, aby byl cluster vždycky ponechán ve funkčním stavu.

    Pokud je cluster Hadoop škálované dolů snížením počtu datových uzlů, některé ze služeb v clusteru se restartují. Toto chování způsobí, že všechny spuštěné a nedokončené úlohy selžou při dokončení operace škálování. Po dokončení operace ale můžete úlohy znovu odeslat.

* Apache HBase

    Bez problémů můžete přidat nebo odebrat uzly do clusteru HBA v době, kdy je spuštěný. Regionální servery se automaticky vyrovnávají během několika minut od dokončení operace škálování. Můžete ale také ručně vyrovnávat regionální servery přihlášením k hlavnímu uzlu clusteru a spuštěním následujících příkazů z okna příkazového řádku:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Další informace o použití prostředí HBA najdete [v tématu Začínáme s příkladem Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Můžete hladce přidávat nebo odebírat datové uzly do clusteru s více podsystémy, když je spuštěný. Po úspěšném dokončení operace škálování ale budete muset topologii znovu vyvážit.

    Nové Vyrovnávání je možné dosáhnout dvěma způsoby:

  * Webové uživatelské rozhraní pro vyplavení
  * Nástroj rozhraní příkazového řádku (CLI)

    Další podrobnosti najdete v [dokumentaci k Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    Webové uživatelské rozhraní pro zaplavení je k dispozici v clusteru HDInsight:

    ![Rebilance škálování ve službě HDInsight](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad příkazu CLI pro rozložení topologie zatížení:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Jak bezpečně škálovat cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Horizontální navýšení kapacity clusteru se spuštěnými úlohami

Aby se předešlo tomu, že spuštěné úlohy selžou během operace horizontálního škálování, můžete vyzkoušet tři věci:

1. Před škálováním clusteru počkejte na dokončení úloh.
1. Ručně ukončete úlohy.
1. Znovu odešle úlohy po uzavření operace škálování.

Chcete-li zobrazit seznam probíhajících a spuštěných úloh, můžete použít **uživatelské rozhraní příz správce prostředků**následujícím postupem:

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster.  Pokyny najdete v tématu [seznam a zobrazení clusterů](./hdinsight-administer-use-portal-linux.md#showClusters) . Cluster se otevře na nové stránce portálu.
2. V hlavním zobrazení přejděte na **řídicí panely** > clusteru**Ambari domů**. Zadejte přihlašovací údaje clusteru.
3. V uživatelském rozhraní Ambari vyberte možnost **příze** v seznamu služeb v nabídce na levé straně.  
4. Na stránce PŘÍZe vyberte možnost **Rychlé odkazy** a najeďte myší na aktivní hlavní uzel a pak vyberte **uživatelské rozhraní ResourceManager**.

    ![Uživatelské rozhraní ResourceManager pro rychlé odkazy Apache Ambari](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

K uživatelskému rozhraní ResourceManager můžete přistupovat přímo `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`pomocí.

Zobrazí se seznam úloh spolu s jejich aktuálním stavem. Na snímku obrazovky je aktuálně spuštěná jedna úloha:

![Aplikace uživatelského rozhraní ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Pokud chcete tuto spuštěnou aplikaci odstranit ručně, spusťte v prostředí SSH následující příkaz:

```bash
yarn application -kill <application_id>
```

Příklad:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Zablokování v bezpečném režimu

Při horizontálním navýšení kapacity clusteru používá HDInsight rozhraní pro správu Apache Ambari a nejdřív vyřadí další pracovní uzly, které replikují své bloky HDFS do jiných online pracovních uzlů. Služba HDInsight pak cluster bezpečně škáluje. HDFS přechází do bezpečného režimu během operace škálování a měl by se nacházet po dokončení škálování. V některých případech se ale HDFS zablokuje v bezpečném režimu během operace škálování z důvodu blokování souborů v rámci replikace.

Ve výchozím nastavení má HDFS nakonfigurovanou `dfs.replication` hodnotu 3, která určuje, kolik kopií každého bloku souboru je k dispozici. Každá kopie bloku souboru je uložená v jiném uzlu clusteru.

Když HDFS zjistí, že očekávaný počet kopií bloku není k dispozici, HDFS vstoupí do bezpečného režimu a Ambari vygeneruje výstrahy. Pokud HDFS vstupuje do bezpečného režimu pro operaci škálování, ale pak nemůže ukončit nouzový režim, protože pro replikaci není zjištěn požadovaný počet uzlů, cluster se může zablokovat v bezpečném režimu.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Ukázkové chyby, když je zapnutý režim zabezpečení

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

V případě, že se v clusteru změnila `/var/log/hadoop/hdfs/` velikost, můžete zkontrolovat protokoly uzlů názvů z této složky, aby se zjistilo, kdy vstoupila do bezpečného režimu. Soubory protokolu jsou pojmenovány `Hadoop-hdfs-namenode-hn0-clustername.*`.

Hlavní příčinou předchozích chyb je, že při spouštění dotazů závisí podregistr na dočasné soubory v HDFS. Když HDFS vstoupí do bezpečného režimu, podregistr nemůže spustit dotazy, protože nemůže zapisovat do HDFS. Dočasné soubory v HDFS jsou umístěné na místní jednotce připojené k jednotlivým virtuálním počítačům uzlu pracovního procesu a replikují se mezi ostatními pracovními uzly na třech replikách, minimálně.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zabránit službě HDInsight v tom, aby se zablokovala v bezpečném režimu

Existuje několik způsobů, jak zabránit v tom, aby HDInsight zůstalo v bezpečném režimu:

* Před škálováním HDInsight zastavte všechny úlohy podregistru. Případně můžete naplánovat proces horizontálního navýšení kapacity, aby nedocházelo ke konfliktům s běžícími úlohami.
* Před horizontálním navýšení `tmp` kapacity je nutné ručně vyčistit soubory odkládacího adresáře podregistru v HDFS.
* Nahorizontální navýšení kapacity HDInsight na tři pracovní uzly, minimální. Vyhněte se nedostatku na jeden pracovní uzel.
* Spusťte příkaz a v případě potřeby ponechte nouzový režim.

Tyto možnosti jsou popsány v následujících částech.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy podregistru

Před škálováním dolů na jeden pracovní uzel zastavte všechny úlohy podregistru. Pokud je naplánováno vaše úlohy, po dokončení práce na podregistru proveďte své škálování.

Zastavení úloh podregistru před škálováním pomůže minimalizovat počet pomocných souborů ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ruční vyčištění pomocného souboru podregistru

Pokud podregistr opustí dočasné soubory, můžete tyto soubory před horizontálním škálováním ručně vyčistit a vyhnout se bezpečnému režimu.

1. Podívejte se `hive.exec.scratchdir` na vlastnost konfigurace a ověřte, které umístění se používá pro dočasné soubory podregistru. Tento parametr je nastaven v `/etc/hive/conf/hive-site.xml`rámci:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zastavte služby podregistru a ujistěte se, že jsou dokončené všechny dotazy a úlohy.
2. Vypíše obsah pomocného adresáře, který se `hdfs://mycluster/tmp/hive/` našel výše, aby se zjistilo, jestli obsahuje nějaké soubory:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Zde je ukázkový výstup, když soubory existují:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Pokud víte, že je podregistr s těmito soubory hotový, můžete je odebrat. Ujistěte se, že v podregistru nejsou spuštěné žádné dotazy, a to tak, že se vyhledají na stránce uživatelského rozhraní Správce prostředků příz.

    Příklad příkazového řádku pro odebrání souborů z HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Škálování HDInsight na tři nebo více pracovních uzlů

Pokud se vaše clustery v bezpečném režimu často zablokují při horizontálním navýšení kapacity na méně než tři pracovní uzly a předchozí kroky nefungují, můžete se tomuto clusteru vyhnout v bezpečném režimu, a to tak, že zachováte aspoň tři pracovní uzly.

Zachování tří pracovních uzlů je dražší než horizontální snížení kapacity jenom na jeden pracovní uzel, ale zabrání tomu, aby se váš cluster dostal do bezpečného režimu.

#### <a name="run-the-command-to-leave-safe-mode"></a>Spusťte příkaz pro opuštění nouzového režimu.

Poslední možností je spustit příkaz opustit režim nouze. Pokud víte, že důvod HDFS, který vstupuje do bezpečného režimu, je z důvodu nereplikace souboru podregistru, můžete spustit následující příkaz pro opuštění nouzového režimu:

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
* [Seznámení se službou Azure HDInsight](hadoop/apache-hadoop-introduction.md)
