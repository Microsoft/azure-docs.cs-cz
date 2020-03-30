---
title: Správa prostředků pro cluster Apache Spark na Azure HDInsight
description: Zjistěte, jak spravovat prostředky pro clustery Spark na Azure HDInsight pro lepší výkon.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932109"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Správa prostředků pro cluster Apache Spark na Azure HDInsight

Zjistěte, jak přistupovat k rozhraním, jako je uživatelské rozhraní [Apache Ambari,](https://ambari.apache.org/) uživatelské rozhraní [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) a [Spark History Server](./apache-azure-spark-history-server.md) přidružený k vašemu clusteru [Apache Spark,](https://spark.apache.org/) a jak optimalizovat konfiguraci clusteru pro optimální výkon.

## <a name="open-the-spark-history-server"></a>Otevření serveru historie Spark

Spark History Server je webové uživatelské uživatelské nastavení pro dokončené a spuštěné aplikace Spark. Je to rozšíření webového uživatelského uživatelského prostředí Spark. Úplné informace naleznete v tématu [Spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Otevření ui příze

UI YARN můžete použít ke sledování aplikací, které jsou aktuálně spuštěny v clusteru Spark.

1. Na [portálu Azure](https://portal.azure.com/)otevřete cluster Spark. Další informace naleznete v [tématu Seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).

2. V **řídicích panelech clusteru**vyberte **položku Příze**. Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark.

    ![Spuštění YARN UI](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Případně můžete také spustit UI YARN z ui Ambari. V uzdu Ambari přejděte na**Quick Links** > **Active** >  **aktivní** > **uj.**

## <a name="optimize-clusters-for-spark-applications"></a>Optimalizace clusterů pro aplikace Spark

Tři klíčové parametry, které lze použít pro konfiguraci `spark.executor.cores`Spark `spark.executor.memory`v závislosti na požadavcích aplikace jsou `spark.executor.instances`, a . Exekutor je proces spuštěný pro aplikaci Spark. Spustí se na pracovní uzel a je zodpovědný za provádění úkolů pro aplikaci. Výchozí počet vykonavatelů a velikosti vykonavatelů pro každý cluster se vypočítá na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tyto informace jsou `spark-defaults.conf` uloženy v hlavní uzly clusteru.

Tři konfigurační parametry lze konfigurovat na úrovni clusteru (pro všechny aplikace spuštěné v clusteru) nebo mohou být určeny také pro každou jednotlivou aplikaci.

### <a name="change-the-parameters-using-ambari-ui"></a>Změna parametrů pomocí ui.

1. Z uzlového nastavení Ambari přejděte na výchozí hodnoty **Spark2** > **Configs** > **.**

    ![Nastavení parametrů pomocí vlastního nastavení Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Nastavení parametrů pomocí vlastního nastavení Ambari")

1. Výchozí hodnoty jsou dobré mít čtyři aplikace Spark spustit souběžně v clusteru. Tyto hodnoty můžete změnit z uživatelského rozhraní, jak je znázorněno na následujícím snímku obrazovky:

    ![Nastavení parametrů pomocí Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Nastavení parametrů pomocí Ambari")

1. Chcete-li uložit změny konfigurace, vyberte **uložit.** V horní části stránky budete vyzváni k restartování všech ohrožených služeb. Vyberte **restartovat**.

    ![Restartovat služby](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů aplikace spuštěné v notebooku Jupyter

Pro aplikace spuštěné v poznámkovém bloku `%%configure` Jupyter, můžete použít kouzlo k provádění změn konfigurace. V ideálním případě je nutné provést takové změny na začátku aplikace před spuštěním první buňky kódu. Tím zajistíte, že konfigurace se použije na relaci Livy, když se vytvoří. Pokud chcete změnit konfiguraci v pozdější fázi aplikace, musíte `-f` použít parametr. Tím však veškerý pokrok v aplikaci je ztracen.

Následující výstřižek ukazuje, jak změnit konfiguraci pro aplikaci spuštěnou v Jupyteru.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurační parametry musí být předány jako řetězec JSON a musí být na dalším řádku za magic, jak je znázorněno v příkladu sloupce.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Změna parametrů žádosti podané pomocí spark-submit

Následující příkaz je příkladem toho, jak změnit parametry konfigurace dávkové aplikace, která je odeslána pomocí `spark-submit`aplikace .

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Změna parametrů žádosti podané pomocí cURL

Následující příkaz je příkladem toho, jak změnit parametry konfigurace dávkové aplikace, která je odeslána pomocí cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Změna těchto parametrů na serveru Spark Thrift

Spark Thrift Server poskytuje jdbc/ODBC přístup ke clusteru Spark a používá se ke správě dotazů Spark SQL. Nástroje jako Power BI, Tableau a tak dále používají protokol ODBC ke komunikaci se Spark Thrift Server a spouštějí dotazy Spark SQL jako aplikaci Spark. Při vytvoření clusteru Spark jsou spuštěny dvě instance serveru Spark Thrift Server, jedna na každém hlavním uzlu. Každý Spark Thrift Server je viditelný jako aplikace Spark v ui YARN.

Spark Thrift Server používá dynamickou alokaci vykonavatelů `spark.executor.instances` Spark, a proto se nepoužívá. Místo toho Spark Thrift Server používá `spark.dynamicAllocation.maxExecutors` a `spark.dynamicAllocation.minExecutors` určit počet vykonavatel. Konfigurační `spark.executor.cores`parametry a `spark.executor.memory` slouží k úpravě velikosti vykonavatele. Tyto parametry můžete změnit, jak je znázorněno v následujících krocích:

* Rozbalte kategorii **Advanced spark2-srift-sparkconf** a `spark.dynamicAllocation.maxExecutors`aktualizujte `spark.dynamicAllocation.minExecutors`parametry a .

    ![Konfigurace šetrnějšího serveru Spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurace šetrnějšího serveru Spark")

* Rozbalte vlastní **spark2-šetrnost-sparkconf** kategorie `spark.executor.cores`aktualizovat `spark.executor.memory`parametry a .

    ![Konfigurace parametru serveru spořivosti Spark](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurace parametru serveru spořivosti Spark")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Změna paměti ovladače serveru Spark Thrift Server

Paměť ovladače Spark Thrift Server je nakonfigurována na 25 % velikosti paměti RAM hlavního uzlu za předpokladu, že celková velikost paměti RAM hlavního uzlu je větší než 14 GB. Pomocí ui Ambari můžete změnit konfiguraci paměti ovladače, jak je znázorněno na následujícím snímku obrazovky:

Z ui Ambari přejděte na **Spark2** > **Configs** > **Advanced spark2-env**. Poté zadejte hodnotu pro **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Rekultivace prostředků clusteru Spark

Z důvodu dynamického přidělení Spark jsou jedinými prostředky, které jsou spotřebovány šetrnostserverem prostředky pro dvě hlavní aplikační servery. Chcete-li tyto prostředky získat zpět, je nutné zastavit služby serveru thrift server spuštěné v clusteru.

1. V uzu Ambari v levém podokně vyberte **Spark2**.

2. Na další stránce vyberte **možnost Spark2 Thrift Servers**.

    ![Restartovat šetrný server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Restartovat šetrný server1")

3. Měli byste vidět dva headnodes, na kterém Spark2 Thrift Server běží. Vyberte jeden z hlavových uzlů.

    ![Restartovat šetrný server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Restartovat šetrný server2")

4. Na další stránce jsou uvedeny všechny služby spuštěné na tomto headnode. V seznamu vyberte rozevírací tlačítko vedle sparku 2 Thrift Server a pak vyberte **Zastavit**.

    ![Restartovat šetrný server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Restartovat šetrný server3")
5. Opakujte tyto kroky i na druhém headnode.

## <a name="restart-the-jupyter-service"></a>Restartujte službu Jupyter

Launch the Ambari Web UI as shown in the beginning of the article. V levém navigačním podokně vyberte **Jupyter**, vyberte **Akce služby**a pak vyberte **Restartovat vše**. Tím se spustí služba Jupyter na všech headuzeldes.

![Restartujte Jupyter.](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Restartujte Jupyter.")

## <a name="monitor-resources"></a>Sledování prostředků

Spusťte ui příze, jak je znázorněno na začátku článku. V tabulce Metriky clusteru v horní části obrazovky zkontrolujte hodnoty **sloupců Využitá paměť** a **Celkový počet paměti.** Pokud jsou tyto dvě hodnoty blízko, nemusí být dostatek prostředků ke spuštění další aplikace. Totéž platí pro **sloupce Použitá virtuální jádra** a **Celkový počet virtuálních jader.** Také v hlavním zobrazení, pokud je aplikace zůstala ve stavu **PŘIJAT** a není přechod do **spuštěné** nebo **neúspěšný** stav, může to být také údaj, že není dostatek prostředků pro spuštění.

![Limit zdroje](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limit zdroje")

## <a name="kill-running-applications"></a>Ustlat spuštěné aplikace

1. V uzlina příze, z levého panelu, vyberte **Běh**. Ze seznamu spuštěných aplikací určete aplikaci, která má být ustupována, a vyberte **ID**.

    ![Zabít App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Zabít App1")

2. V pravém horním rohu vyberte **Příkaz zabít aplikaci** a pak vyberte **OK**.

    ![Zabít App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Zabít App2")

## <a name="see-also"></a>Viz také

* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Pro datové analytiky

* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza telemetrických dat Application Insight pomocí Apache Spark v HDInsightu](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Pro vývojáře Apache Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
