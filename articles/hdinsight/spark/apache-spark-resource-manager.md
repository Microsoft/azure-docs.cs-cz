---
title: Správa prostředků pro cluster Apache Spark ve službě Azure HDInsight
description: Naučte se používat správu prostředků pro clustery Spark v Azure HDInsight pro lepší výkon.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 0d97ca91466516b8722ecca77d19078399a258f7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814097"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Správa prostředků pro cluster Apache Spark ve službě Azure HDInsight 

Naučte se, jak získat přístup k rozhraním, jako je uživatelské rozhraní [Apache Ambari](https://ambari.apache.org/) , [Apache Hadoop příz](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI a serveru historie Sparku přidruženému k vašemu clusteru [Apache Spark](https://spark.apache.org/) a jak vyladit konfiguraci clusteru pro optimální výkon.

**Požadavky:**

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Otevřete webové uživatelské rozhraní Ambari.

Apache Ambari slouží k monitorování clusteru a provádění změn konfigurace. Další informace najdete v tématu [správa Apache Hadoop clusterů ve službě HDInsight pomocí Azure Portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Otevřete Server historie Spark.

Server historie Spark je webové uživatelské rozhraní pro kompletní a běžící aplikace Spark. Jedná se o rozšíření webového uživatelského rozhraní Spark.

**Otevření webového uživatelského rozhraní serveru historie Sparku**

1. Z [Azure Portal](https://portal.azure.com/)otevřete cluster Spark. Další informace najdete v tématech [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **panelu Rychlé odkazy**klikněte na **řídicí panel clusteru**a pak klikněte na **Server historie Spark** .

    ![Server historie Sparku](./media/apache-spark-resource-manager/launch-history-server.png "Server historie Sparku")

    Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark. Server historie Sparku můžete otevřít také tak, že přejdete na následující adresu URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Nahraďte `<ClusterName>` názvem vašeho clusteru Spark.

Webové uživatelské rozhraní serveru historie Sparku vypadá takto:

![Server historie HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Otevřete uživatelské rozhraní příze.
Pomocí uživatelského rozhraní PŘÍZe můžete monitorovat aplikace, které jsou aktuálně spuštěny v clusteru Spark.

1. Z [Azure Portal](https://portal.azure.com/)otevřete cluster Spark. Další informace najdete v tématech [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **panelu Rychlé odkazy**klikněte na **řídicí panel clusteru**a pak klikněte na **příze**.

    ![Spustit rozhraní PŘÍZe](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Alternativně můžete také spustit rozhraní PŘÍZe z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, klikněte na **řídicí panel clusteru**a pak klikněte na **řídicí panel clusteru HDInsight**. V uživatelském rozhraní Ambari klikněte na **příze**, klikněte na **Rychlé odkazy**, klikněte na aktivní Správce prostředků a potom klikněte na **Správce prostředků uživatelské rozhraní**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimalizace clusterů pro aplikace Spark
Tři klíčové parametry, které lze použít pro konfiguraci Sparku v závislosti na požadavcích aplikace jsou `spark.executor.instances`, `spark.executor.cores`a `spark.executor.memory`. Vykonavatel je proces, který se spustil pro aplikaci Spark. Běží na pracovním uzlu a zodpovídá za provádění úkolů aplikace. Výchozí počet prováděcích modulů a velikosti prováděcího modulu pro jednotlivé clustery se vypočítávají na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tyto informace jsou uloženy v `spark-defaults.conf` uzlech hlavní uzly clusteru.

Tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace, které jsou spuštěny v clusteru), nebo je lze zadat také pro každou jednotlivou aplikaci.

### <a name="change-the-parameters-using-ambari-ui"></a>Změna parametrů pomocí uživatelského rozhraní Ambari
1. V uživatelském rozhraní Ambari klikněte na **Spark**, klikněte na **Konfigurace**a pak na **vlastní Spark – výchozí**.

    ![Nastavení parametrů pomocí Ambari Custom](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Nastavení parametrů pomocí Ambari Custom")
2. Výchozí hodnoty jsou vhodné k souběžnému spuštění čtyř aplikací Spark v clusteru. Tyto hodnoty můžete změnit z uživatelského rozhraní, jak je znázorněno na následujícím snímku obrazovky:

    ![Nastavení parametrů pomocí Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png "Nastavení parametrů pomocí Ambari")

3. Kliknutím na **Uložit** uložte změny konfigurace. V horní části stránky se zobrazí výzva k restartování všech ovlivněných služeb. Klikněte na **Restartovat**.

    ![Restartovat služby](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů aplikace spuštěné v Jupyter poznámkovém bloku
Pro aplikace spuštěné v poznámkovém bloku Jupyter můžete provést změny `%%configure` konfigurace pomocí Magic. V ideálním případě je nutné provést tyto změny na začátku aplikace před spuštěním první buňky kódu. Tím se zajistí, že se konfigurace použije pro relaci Livy při jejím vytvoření. Pokud chcete změnit konfiguraci v pozdější fázi aplikace, musíte použít `-f` parametr. Nicméně tím dojde ke ztrátě veškerého postupu v aplikaci.

Následující fragment kódu ukazuje, jak změnit konfiguraci aplikace běžící v Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfigurace musí být předány jako řetězec JSON a musí být na dalším řádku po Magic, jak je znázorněno v příkladu sloupce.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Změna parametrů aplikace odeslané pomocí Spark-Submit
Následující příkaz je příkladem, jak změnit parametry konfigurace pro aplikaci Batch, která je odeslána pomocí `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Změna parametrů aplikace odeslané pomocí objektu kudrlinkou
Následující příkaz je příkladem, jak změnit parametry konfigurace pro aplikaci Batch, která je odeslána pomocí objektu kudrlinkou.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Změna těchto parametrů na serveru Spark Thrift
Spark Thrift Server poskytuje přístup JDBC/ODBC ke clusteru Spark a používá se k provozování dotazů Spark SQL. Nástroje, jako je Power BI, Tableau atd. použijte protokol ODBC ke komunikaci se serverem Spark Thrift ke spouštění dotazů Spark SQL jako aplikace Spark. Po vytvoření clusteru Spark se spustí dvě instance serveru Spark Thrift, jednu na každý hlavní uzel. Každý server Spark Thrift je v uživatelském rozhraní PŘÍZe viditelný jako aplikace Spark.

Spark Thrift Server používá přidělování dynamického prováděcího modulu Spark `spark.executor.instances` , a proto se nepoužívá. Místo toho používá `spark.dynamicAllocation.minExecutors` Spark Thrift Server a `spark.dynamicAllocation.maxExecutors` k určení počtu prováděcích modulů. Parametry `spark.executor.cores` konfigurace a `spark.executor.memory` slouží k úpravě velikosti prováděcího modulu. Tyto parametry můžete změnit, jak je znázorněno v následujícím postupu:

* Rozbalením kategorie **Advanced Spark-Thrift-sparkconf** aktualizujte parametry `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`a `spark.executor.memory`.

    ![Konfigurace serveru Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-1.png "Konfigurace serveru Spark Thrift")
* Chcete-li aktualizovat parametr `spark.executor.cores`, rozbalte kategorii **Custom Spark-Thrift-sparkconf** .

    ![Konfigurace parametru serveru Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-2.png "Konfigurace parametru serveru Spark Thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Změna paměti ovladače serveru Spark Thrift
Paměť ovladače serveru Spark Thrift je nakonfigurovaná na 25% velikosti paměti RAM hlavního uzlu, a to za předpokladu, že celková velikost paměti RAM hlavního uzlu je větší než 14 GB. Pomocí uživatelského rozhraní Ambari můžete změnit konfiguraci paměti ovladače, jak je znázorněno na následujícím snímku obrazovky:

* V uživatelském rozhraní Ambari klikněte na **Spark**, klikněte na **Konfigurace**, rozbalte **Upřesnit Spark-ENV**a zadejte hodnotu pro **spark_thrift_cmd_opts**.

    ![Konfigurace paměti RAM serveru Spark Thrift](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Uvolnění prostředků clusteru Spark
Z důvodu dynamického přidělování Spark jsou jediné prostředky, které jsou využívány serverem Thrift, prostředky pro dva hlavní aplikační servery. Chcete-li tyto prostředky získat zpět, je nutné zastavit služby serveru Thrift spuštěné v clusteru.

1. V uživatelském rozhraní Ambari klikněte v levém podokně na **Spark**.
2. Na další stránce klikněte na **Thrift servery Spark**.

    ![Restartovat Thrift Server1](./media/apache-spark-resource-manager/restart-thrift-server-1.png "Restartovat Thrift Server1")
3. Měli byste vidět dva hlavních, na kterých běží server Spark Thrift. Klikněte na jeden z hlavních.

    ![Restartovat Thrift Server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Restartovat Thrift Server2")
4. Na další stránce jsou uvedeny všechny služby běžící na tomto hlavnímu uzlu. V seznamu klikněte na rozevírací nabídku vedle Thrift serveru Spark a pak klikněte na **zastavit**.

    ![Restartovat Thrift Server3](./media/apache-spark-resource-manager/restart-thrift-server-3.png "Restartovat Thrift Server3")
5. Tyto kroky opakujte i na ostatních hlavnímu uzlu.

## <a name="restart-the-jupyter-service"></a>Restartujte službu Jupyter.
Spusťte webové uživatelské rozhraní Ambari, jak je znázorněno na začátku článku. V levém navigačním podokně klikněte na **Jupyter**, klikněte na **Akce služby**a pak klikněte na **restartovat vše**. Tím se spustí služba Jupyter ve všech hlavních.

![Restartovat Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restartovat Jupyter")

## <a name="monitor-resources"></a>Sledování prostředků
Spusťte uživatelské rozhraní příze, jak je znázorněno na začátku článku. V tabulce metriky clusteru v horní části obrazovky ověřte hodnoty **využité paměti** a **Celkový počet sloupců paměti** . Pokud jsou tyto dvě hodnoty blízko, nemusí být k dispozici dostatek prostředků ke spuštění další aplikace. Totéž platí pro sloupce **virtuální jádra použité** a **virtuální jádra celkem** . Pokud je v hlavním zobrazení aplikace nechali v **přijatém** stavu a nepřechází do stavu **spuštěno** nebo **selhání** , může to také znamenat, že není dostatek prostředků ke spuštění.

![Omezení prostředků](./media/apache-spark-resource-manager/resource-limit.png "Omezení prostředků")

## <a name="kill-running-applications"></a>Ukončit běžící aplikace
1. V uživatelském rozhraní příze na levém panelu klikněte na **Spustit**. V seznamu spuštěných aplikací určete aplikaci, kterou chcete ukončit, a klikněte na **ID**.

    ![Kill app1](./media/apache-spark-resource-manager/kill-app1.png "Kill app1")

2. Klikněte na tlačítko **ukončit aplikaci** v pravém horním rohu a pak klikněte na **OK**.

    ![Kill app2](./media/apache-spark-resource-manager/kill-app2.png "Kill app2")

## <a name="see-also"></a>Viz také:
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Pro analytiky dat

* [Apache Spark s Machine Learning: Použití Sparku ve službě HDInsight k analýze teploty budovy pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insight pomocí Apache Spark ve službě HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Použití Caffe na Azure HDInsight Spark pro distribuované hloubkové učení](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Pro Apache Spark vývojáře

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
