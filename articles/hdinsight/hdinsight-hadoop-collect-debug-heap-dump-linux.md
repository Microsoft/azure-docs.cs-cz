---
title: Povolení výpisů hald pro služby Apache Hadoop na HDInsight – Azure
description: Povolte výpisy hald y pro služby Apache Hadoop z clusterů HDInsight založených na Linuxu pro ladění a analýzu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75658793"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Povolení výpisů hald pro služby Apache Hadoop na Linuxu založeném na HDInsightu

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Výpisy haldy obsahují snímek paměti aplikace, včetně hodnot proměnných v době, kdy byl vytvořen výpis. Takže jsou užitečné pro diagnostiku problémů, ke kterým dochází za běhu.

## <a name="services"></a>Služby

Můžete povolit výpisy haldy pro následující služby:

* **Apache hcatalog** - tempelton
* **Apache úl** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Apache příze** - resourcemanager, nodemanager, timelineserver
* **Apache hdfs** - datanode, sekundárnínázevnod, namenode

Můžete také povolit výpisy haldy pro mapu a snížit procesy spuštěné HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Principy konfigurace výpisu haldy

Výpisy haldy jsou povoleny předávání maže možnosti (někdy označované jako opts nebo parametry) na JVM při spuštění služby. U většiny služeb [Apache Hadoop](https://hadoop.apache.org/) můžete upravit skript prostředí použitý ke spuštění služby k předání těchto možností.

V každém skriptu je export pro ** \* \_OPTS**, který obsahuje možnosti předané JVM. Například ve **skriptu hadoop-env.sh** řádek, který `export HADOOP_NAMENODE_OPTS=` začíná obsahuje možnosti pro službu NameNode.

Mapovat a snížit procesy se mírně liší, protože tyto operace jsou podřízený proces MapReduce služby. Každé mapování nebo snížit proces běží v podřízeném kontejneru a existují dvě položky, které obsahují možnosti JVM. Oba obsažené v **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Doporučujeme použít [Apache Ambari](https://ambari.apache.org/) upravit skripty a mapred-site.xml nastavení, jako Ambari popisovat replikovat změny mezi uzly v clusteru. Konkrétní kroky najdete v části [Použití Apache Ambari.](#using-apache-ambari)

### <a name="enable-heap-dumps"></a>Povolení výpisů paměti haldy

Následující možnost povolí výpisy haldy, když dojde k OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

Označuje, **+** že tato možnost je povolena. Ve výchozím nastavení je tato možnost zakázána.

> [!WARNING]  
> Výpisy haldy nejsou povoleny pro služby Hadoop na HDInsight ve výchozím nastavení, jako soubory výpisu může být velký. Pokud je povolíte pro řešení potíží, nezapomeňte je po reprodukování problému a shromáždění souborů s výpisem stavu paměti zakázat.

### <a name="dump-location"></a>Umístění výpisu

Výchozí umístění souboru s výpisem stavu paměti je aktuální pracovní adresář. Pomocí následující možnosti můžete určit, kde je soubor uložen:

    -XX:HeapDumpPath=/path

Například použití `-XX:HeapDumpPath=/tmp` způsobí, že výpisy, které mají být uloženy v adresáři /tmp.

### <a name="scripts"></a>Scripts

Můžete také aktivovat skript, když dojde k **outOfMemoryError.** Například aktivace oznámení, abyste věděli, že došlo k chybě. Pomocí následující možnosti můžete spustit skript v __chybě OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Vzhledem k tomu, že Apache Hadoop je distribuovaný systém, musí být všechny použité skripty umístěny na všech uzlech v clusteru, ve které je služba spuštěna.
> 
> Skript musí být také v umístění, které je přístupné účet služba běží jako a musí poskytnout oprávnění ke spuštění. Můžete například chtít ukládat skripty `/usr/local/bin` `chmod go+rx /usr/local/bin/filename.sh` a použít k udělení oprávnění ke čtení a spouštění.

## <a name="using-apache-ambari"></a>Použití Apache Ambari

Chcete-li upravit konfiguraci služby, postupujte takto:

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

2. Pomocí seznamu vlevo vyberte oblast služeb, kterou chcete upravit. Například **HDFS**. Ve střední oblasti vyberte kartu **Konfigurace.**

    ![Obrázek webu Ambari s vybranou kartou HDFS Configs](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Pomocí položky **Filtr...** zadejte **opts**. Zobrazí se pouze položky obsahující tento text.

    ![Apache Ambari config filtrovaný seznam](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Najděte položku ** \* \_OPTS** pro službu, pro kterou chcete povolit výpisy haldy, a přidejte možnosti, které chcete povolit. Na následujícím obrázku jsem `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` přidal do položky **HADOOP\_NAMENODE\_OPTS:**

    ![Apache Ambari hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Při povolení výpisů haldy pro mapu nebo snížit podřízený proces, vyhledejte pole s názvem **mapreduce.admin.map.child.opts** a **mapreduce.admin.reduce.child.java.opts**.

    Změny můžete uložit pomocí tlačítka **Uložit.** Můžete zadat krátkou poznámku popisující změny.

5. Po použití změn se vedle jedné nebo více služeb zobrazí ikona **Požadované restartování.**

    ![ikona požadované restartování a tlačítko restartovat](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Vyberte každou službu, která vyžaduje restartování, a pomocí tlačítka **Akce služby** **zapněte režim údržby**. Režim údržby zabraňuje generování výstrah ze služby při restartování.

    ![Zapnutí nabídky režimu údržby hdi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Jakmile aktivujete režim údržby, použijte tlačítko **Restartovat** pro službu, abyste **restartovali všechny efektní**

    ![Apache Ambari restartovat všechny ovlivněné položky](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Položky tlačítka **Restart** ovat se mohou lišit pro ostatní služby.

8. Po restartování služeb vypněte **režim údržby**pomocí tlačítka **Akce služby** . Tento Ambari obnovit monitorování výstrah pro službu.