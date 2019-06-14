---
title: Škálování clusteru velikosti – Azure HDInsight
description: Škálování clusteru Azure HDInsight Elasticky tak, aby odpovídaly vaší úlohy.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: b85277a4238351b6448c2cf29676ae3d8c118385
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077206"
---
# <a name="scale-hdinsight-clusters"></a>Škálování clusterů HDInsight

HDInsight poskytuje pružnost tím, že možnost vertikálně navýšit a snížit počet pracovních uzlů v clusterech služby. Pružnost, můžete zmenšit cluster po hodinách, nebo o víkendech a rozbalte ho během špičky obchodními požadavky.

Pokud máte pravidelné dávkové zpracování, clusteru HDInsight se dá škálovat několik minut, než tuto operaci tak, aby cluster měl adekvátní paměť a procesory.  Později po dokončení zpracování a využití ocitne mimo provoz znovu, můžete vertikálně snížit kapacitu clusteru HDInsight na míň pracovních uzlů.

Škálování clusteru ručně pomocí jedné z metod popsaných níže, nebo použijte [automatického škálování](hdinsight-autoscale-clusters.md) možnosti, jak systém automaticky škálovat nahoru a dolů v reakci na procesoru, paměti a další metriky.

> [!NOTE]  
> Pouze clustery HDInsight verze 3.1.3 nebo vyšší nejsou podporovány. Pokud si nejste jistí verze vašeho clusteru, můžete zkontrolovat na stránce Vlastnosti.

## <a name="utilities-to-scale-clusters"></a>Nástroje pro škálování clusterů

Společnost Microsoft poskytuje následující nástroje, které škálování clusterů:

|Nástroj | Popis|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<název clusteru > - TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [Změna velikosti az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --& gt; resource-group \<skupinu prostředků > – název \<název clusteru > – cílový počet instancí \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<clusterName> \<Target Instance Count> |
|[Azure Portal](https://portal.azure.com)|Otevřete podokno váš cluster HDInsight, vyberte **velikost clusteru** v nabídce vlevo a pak v podokně velikost clusteru, zadejte počet pracovních uzlů a vyberte Uložit.|  

![Škálování clusteru](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Pomocí kteréhokoli z těchto metod, můžete škálovat svůj cluster HDInsight navýšit nebo snížit kapacitu během několika minut.

> [!IMPORTANT]  
> * Rozhraní příkazového řádku Azure classic je zastaralá a by měla sloužit pouze pomocí modelu nasazení classic. Pro všechna ostatní nasazení, použijte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Modul AzureRM Powershellu je zastaralý.  Použijte prosím [Az modulu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) kdykoli je to možné.

## <a name="impact-of-scaling-operations"></a>Dopad operace škálování

Když jste **přidat** uzlů spuštěného clusteru HDInsight (škálování nahoru), všechny úlohy čekající na vyřízení nebo spuštěné tyto zásady neovlivní. Nové úlohy můžete bezpečně odeslané během procesu změny velikosti. Pokud z nějakého důvodu selže operace škálování, chyby se zpracuje, aby se zachovala váš cluster ve funkčním stavu.

Pokud jste **odebrat** uzly (vertikálně), budou všechny čekající na vyřízení nebo spuštěné úlohy selžou po dokončení operace škálování. To je způsobeno některé ze služeb restartování během procesu změny velikosti. Je také riziko, že váš cluster dostat zablokované v nouzovém režimu během ruční operace škálování.

Změna počtu datových uzlů se liší pro každý typ clusteru podporuje HDInsight:

* Apache Hadoop

    Bezproblémově můžete zvýšit počet pracovních uzlů v clusteru Hadoop, na kterém běží bez dopadu na všechny úlohy čekající na vyřízení nebo spuštěné. Nové úlohy můžete odeslat také když probíhá operace. Selhání v rámci operace škálování jsou zpracovány bez výpadku v tak, aby cluster zůstane vždy ve funkčním stavu.

    Pokud je Hadoop cluster je kapacitu vertikálně snížit snížením počtu datových uzlů, jsou restartovat některé ze služeb v clusteru. Toto chování způsobí, že všechny spuštěné a čekající úlohy selhání po dokončení operace škálování. Můžete, ale neúspěšné úlohy po dokončení operace.

* Apache HBase

    Bezproblémově můžete přidat nebo odebrat uzly do clusteru HBase během jejího běhu. Oblastní servery jsou automaticky rovnoměrně rozdělen do několika minut od dokončení operace škálování. Oblastní servery však můžete také ručně vyvážit změnou přihlášení k hlavnímu uzlu clusteru a spustíte tento příkaz z okna příkazového řádku:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Další informace o používání prostředí HBase najdete v tématu [Začínáme s příkladem Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Bezproblémově můžete přidat nebo odebrat datových uzlů do clusteru Storm během jejího běhu. Ale po úspěšném dokončení operace škálování, je potřeba obnovit rovnováhu topologie.

    Opětovné vyvážení lze provést dvěma způsoby:

  * Webové uživatelské rozhraní Storm
  * Nástroje rozhraní příkazového řádku (CLI)

    Odkazovat [dokumentaci Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) další podrobnosti.

    Webové uživatelské rozhraní Storm je k dispozici v clusteru HDInsight:

    ![Obnovení rovnováhy škálování HDInsight Storm](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Tady je příklad příkazu rozhraní příkazového řádku, chcete-li obnovit rovnováhu topologie Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Tom, jak bezpečně vertikální snížení kapacity clusteru

### <a name="scale-down-a-cluster-with-running-jobs"></a>Vertikálně snížit kapacitu clusteru s běžící úlohy

Abyste nemuseli spuštěné úlohy selžou během vertikálního snižování kapacity operace, můžete zkusit tři věci:

1. Počkejte na dokončení před snížení kapacity clusteru úloh.
1. Ručně ukončení úlohy.
1. Znovu odešlete úlohy po operaci škálování uzavřel.

Pokud chcete zobrazit seznam čekajících a spuštěné úlohy, můžete použít YARN **uživatelského rozhraní správce prostředků**, následujícím postupem:

1. Z [webu Azure portal](https://portal.azure.com/), vyberte svůj cluster.  Zobrazit [výpisu a zobrazení clusterů](./hdinsight-administer-use-portal-linux.md#showClusters) pokyny. Cluster se otevře v nové stránky portálu.
2. V hlavní zobrazení, přejděte na **řídicí panely clusteru** > **Ambari domácí**. Zadejte svoje přihlašovací údaje clusteru.
3. Z uživatelského rozhraní Ambari, vyberte **YARN** na seznam služeb v nabídce vlevo.  
4. Na stránce YARN vyberte **rychlé odkazy** a ukazatel myši aktivní hlavní uzel a potom vyberte **uživatelské rozhraní správce prostředků**.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

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

### <a name="getting-stuck-in-safe-mode"></a>Získávání zablokování v nouzovém režimu

Když vertikálně snížit kapacitu clusteru HDInsight používá rozhraní správy Apache Ambari nejprve vyřadit z provozu navíc pracovních uzlů, které jejich HDFS bloky replikovat do jiné online pracovních uzlů. Za HDInsight bezpečně škáluje cluster. HDFS přejde do režimu bezpečného během operace škálování a by měl pocházet po dokončení změny velikosti. V některých případech však HDFS zablokuje v nouzovém režimu během operace škálování z důvodu snížení replikace souborů bloku.

Ve výchozím nastavení, má nakonfigurovanou HDFS `dfs.replication` nastavení 3, který řídí, kolik kopií každé blokování souborů jsou k dispozici. Každá kopie bloku soubor je uložen na jiném uzlu clusteru.

Když HDFS zjistí, že nejsou k dispozici, očekávaný počet kopií bloku, HDFS přejde do režimu bezpečné a generuje výstrahy, Ambari. Pokud HDFS přejde do režimu bezpečného pro operaci škálování, ale nemůže ukončete Nouzový režim vzhledem k tomu, že pro replikaci nejsou zjištěny požadovaný počet uzlů, clusteru můžete zablokovány v nouzovém režimu.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Příklady chyb po zapnutí Nouzový režim

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Můžete zkontrolovat protokoly název uzlu z `/var/log/hadoop/hdfs/` složku poblíž čas, kdy byl škálování clusteru, zobrazíte, když zadali Nouzový režim. Protokolové soubory jsou pojmenovány `Hadoop-hdfs-namenode-hn0-clustername.*`.

Původní příčinu předchozí chyby je, že Hive závisí na dočasných souborů v HDFS při spouštění dotazů. Když HDFS přejde do režimu bezpečné, Hive nelze spustit dotazy, protože nemůže ani zapisovat do rozhraní HDFS. Dočasné soubory v HDFS nacházejí v místní disk připojený k jednotlivým pracovním uzlu virtuálních počítačů a replikovat mezi jinými uzly pracovního procesu na třech replikách, minimální.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Jak zabránit získávání zablokování v nouzovém režimu HDInsight

Existuje několik způsobů, jak zabránit ponechán v nouzovém režimu HDInsight:

* Zastavte všechny úlohy Hive před škálování HDInsight. Můžete také naplánujte vertikálně proces, který zabrání konfliktní se spouštěním úloh Hive.
* Ručně vyčistit Hive na začátku `tmp` soubory adresáře v HDFS před škálování.
* Pouze snížit HDInsight do tří pracovních uzlů, minimální. Vyhněte se bude v rozsahu od jednoho pracovního uzlu.
* Spuštěním příkazu ukončit nouzový režim, v případě potřeby.

Následující části popisují tyto možnosti.

#### <a name="stop-all-hive-jobs"></a>Zastavit všechny úlohy Hive

Zastavte všechny úlohy Hive před vertikální snížení na jednoho pracovního uzlu. Pokud vaše úloha je naplánovaná, provádějí vaše vertikální snížení kapacity po dokončení práce Hive.

Zastavení úlohy Hive před Škálováním, pomáhá minimalizovat počet pomocné soubory ve složce tmp (pokud existuje).

#### <a name="manually-clean-up-hives-scratch-files"></a>Ručně vyčistit pomocné soubory Hive.

Pokud Hive se zachovají dočasné soubory, pak tyto soubory můžete ručně vyčistit před vertikálně snižuje kapacita na vyhněte Nouzový režim.

1. Zkontrolujte, jaké umístění se používá pro podregistr dočasných souborů pohledem `hive.exec.scratchdir` vlastnost konfigurace. Tento parametr je nastaven v rámci `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Zastavení služeb Hive a ujistěte se, že jsou dokončeny všechny dotazy a úlohy.
2. Zobrazení obsahu pomocný adresář dříve, nalezené `hdfs://mycluster/tmp/hive/` zobrazíte, pokud obsahuje všechny soubory:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Když soubory existují zde je ukázkový výstup:

    ```output
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

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Škálování HDInsight pro tři nebo více uzlů pracovního procesu

Pokud své clustery zaseknou v nouzovém režimu často při škálování na méně než tři pracovní uzly a předchozí postup nefunguje, můžete vyhnout do toho pustit v nouzovém režimu úplně udržováním aspoň tři pracovní uzly clusteru.

Zachování tři uzly pracovního procesu je proto dražší než vertikální snížení na uzlu jen jeden pracovní proces, ale zabrání načítání zablokování v nouzovém režimu clusteru.

#### <a name="run-the-command-to-leave-safe-mode"></a>Pomocí příkazu ukončit nouzový režim

Posledním způsobem je ke spuštění příkazu nechte Nouzový režim. Pokud víte, že z důvodu pro přechod do režimu bezpečného HDFS je z důvodu nedostatečného replikace souborů Hive, můžete spustit následující příkaz, který ponechte Nouzový režim:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Vertikálně snížit kapacitu clusteru Apache HBase

Oblastní servery jsou automaticky rovnoměrně rozdělen během několika minut po dokončení operaci škálování. Ručně vyvážit oblastní servery, proveďte následující kroky:

1. Připojte se ke clusteru HDInsight pomocí SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte prostředí HBase:

    ```bash
    hbase shell
    ```

3. Použijte následující příkaz pro ručně servery oblasti:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Další postup

* [Automatické škálování clusterů Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md)
