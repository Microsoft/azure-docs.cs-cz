---
title: Správa prostředků pro cluster Apache Spark ve službě Azure HDInsight
description: Naučte se spravovat prostředky pro clustery Spark v Azure HDInsight, aby se zajistil vyšší výkon.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929802"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Správa prostředků pro cluster Apache Spark ve službě Azure HDInsight

Naučte se, jak získat přístup k rozhraním, jako je uživatelské rozhraní [Apache Ambari](https://ambari.apache.org/) , [Apache Hadoop příz](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI a [serveru historie Sparku](./apache-azure-spark-history-server.md) přidruženému k vašemu clusteru [Apache Spark](https://spark.apache.org/) a jak vyladit konfiguraci clusteru pro optimální výkon.

## <a name="open-the-spark-history-server"></a>Otevřete Server historie Spark.

Server historie Spark je webové uživatelské rozhraní pro kompletní a běžící aplikace Spark. Jedná se o rozšíření webového uživatelského rozhraní Spark. Úplné informace najdete v tématu [Server historie Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Otevřete uživatelské rozhraní příze.

Pomocí uživatelského rozhraní PŘÍZe můžete monitorovat aplikace, které jsou aktuálně spuštěny v clusteru Spark.

1. Z [Azure Portal](https://portal.azure.com/)otevřete cluster Spark. Další informace najdete v tématech [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Z **řídicích panelů clusteru** vyberte možnost **příze**. Po zobrazení výzvy zadejte přihlašovací údaje správce pro cluster Spark.

    ![Spustit rozhraní PŘÍZe](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternativně můžete také spustit rozhraní PŘÍZe z uživatelského rozhraní Ambari. V uživatelském rozhraní Ambari přejděte do části **nepříze**  >  **Rychlé odkazy**  >  **aktivní**  >  **Správce prostředků uživatelského rozhraní**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimalizace clusterů pro aplikace Spark

Tři klíčové parametry, které lze použít pro konfiguraci Sparku v závislosti na požadavcích aplikace jsou `spark.executor.instances` , `spark.executor.cores` a `spark.executor.memory` . Vykonavatel je proces, který se spustil pro aplikaci Spark. Běží na pracovním uzlu a zodpovídá za provádění úkolů aplikace. Výchozí počet prováděcích modulů a velikosti prováděcího modulu pro jednotlivé clustery se vypočítávají na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tyto informace jsou uloženy v `spark-defaults.conf` uzlech hlavní uzly clusteru.

Tři konfigurační parametry lze nakonfigurovat na úrovni clusteru (pro všechny aplikace, které jsou spuštěny v clusteru), nebo je lze zadat také pro každou jednotlivou aplikaci.

### <a name="change-the-parameters-using-ambari-ui"></a>Změna parametrů pomocí uživatelského rozhraní Ambari

1. V uživatelském rozhraní Ambari přejděte na **Spark2**  >  **config**  >  **Customers Custom Spark2-Defaults**.

    ![Nastavení parametrů pomocí Ambari Custom](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Nastavení parametrů pomocí Ambari Custom")

1. Výchozí hodnoty jsou vhodné k souběžnému spuštění čtyř aplikací Spark v clusteru. Tyto hodnoty můžete změnit z uživatelského rozhraní, jak je znázorněno na následujícím snímku obrazovky:

    ![Nastavení parametrů pomocí Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Nastavení parametrů pomocí Ambari")

1. Kliknutím na **Uložit** uložte změny konfigurace. V horní části stránky budete vyzváni k restartování všech ovlivněných služeb. Vyberte **restartovat**.

    ![Restartovat služby](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů aplikace spuštěné v Jupyter Notebook

Pro aplikace běžící v Jupyter Notebook můžete `%%configure` k provedení změn konfigurace použít Magic. V ideálním případě je nutné provést tyto změny na začátku aplikace před spuštěním první buňky kódu. Tím se zajistí, že se konfigurace použije pro relaci Livy při jejím vytvoření. Pokud chcete změnit konfiguraci v pozdější fázi aplikace, musíte použít `-f` parametr. Nicméně tím dojde ke ztrátě veškerého postupu v aplikaci.

Následující fragment kódu ukazuje, jak změnit konfiguraci aplikace běžící v Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Parametry konfigurace musí být předány jako řetězec JSON a musí být na dalším řádku po Magic, jak je znázorněno v příkladu sloupce.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Změna parametrů aplikace odeslané pomocí Spark-Submit

Následující příkaz je příkladem, jak změnit parametry konfigurace pro aplikaci Batch, která je odeslána pomocí `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Změna parametrů aplikace odeslané pomocí objektu kudrlinkou

Následující příkaz je příkladem, jak změnit parametry konfigurace pro aplikaci Batch, která je odeslána pomocí objektu kudrlinkou.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Zkopírujte soubor JAR do svého účtu úložiště clusteru. Nekopírujte soubor JAR přímo do hlavního uzlu.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Změna těchto parametrů na serveru Spark Thrift

Spark Thrift Server poskytuje přístup JDBC/ODBC ke clusteru Spark a používá se k provozování dotazů Spark SQL. Nástroje jako Power BI, Tableau a tak dále používají protokol ODBC ke komunikaci se serverem Spark Thrift ke spouštění dotazů Spark SQL jako aplikace Spark. Po vytvoření clusteru Spark se spustí dvě instance serveru Spark Thrift, jednu na každý hlavní uzel. Každý server Spark Thrift je v uživatelském rozhraní PŘÍZe viditelný jako aplikace Spark.

Spark Thrift Server používá přidělování dynamických prováděcích modulů Spark, a proto se `spark.executor.instances` nepoužívá. Místo toho používá Spark Thrift server `spark.dynamicAllocation.maxExecutors` a `spark.dynamicAllocation.minExecutors` k určení počtu prováděcích modulů. Parametry konfigurace `spark.executor.cores` a `spark.executor.memory` slouží k úpravě velikosti prováděcího modulu. Tyto parametry můžete změnit, jak je znázorněno v následujícím postupu:

* Rozbalením kategorie **Advanced spark2-Thrift-sparkconf** aktualizujte parametry `spark.dynamicAllocation.maxExecutors` a `spark.dynamicAllocation.minExecutors` .

    ![Konfigurace serveru Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurace serveru Spark Thrift")

* Rozbalením kategorie **Custom spark2-Thrift-sparkconf** aktualizujte parametry `spark.executor.cores` a `spark.executor.memory` .

    ![Konfigurace parametru serveru Spark Thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurace parametru serveru Spark Thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Změna paměti ovladače serveru Spark Thrift

Paměť ovladače serveru Spark Thrift je nakonfigurovaná na 25% velikosti paměti RAM hlavního uzlu, a to za předpokladu, že celková velikost paměti RAM hlavního uzlu je větší než 14 GB. Pomocí uživatelského rozhraní Ambari můžete změnit konfiguraci paměti ovladače, jak je znázorněno na následujícím snímku obrazovky:

V uživatelském rozhraní Ambari přejděte do **Spark2**  >  **config**  >  **Advanced Spark2-ENV**. Pak zadejte hodnotu pro **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Uvolnění prostředků clusteru Spark

Z důvodu dynamického přidělování Spark jsou jediné prostředky, které jsou využívány serverem Thrift, prostředky pro dva hlavní aplikační servery. Chcete-li tyto prostředky získat zpět, je nutné zastavit služby serveru Thrift spuštěné v clusteru.

1. V uživatelském rozhraní Ambari vyberte v levém podokně **Spark2**.

2. Na další stránce vyberte **Spark2 servery Thrift**.

    ![Restartovat Thrift Server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Restartovat Thrift Server1")

3. Měli byste vidět dva hlavních, na kterých běží server Spark2 Thrift. Vyberte jednu z hlavních.

    ![Restartovat Thrift Server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Restartovat Thrift Server2")

4. Na další stránce jsou uvedeny všechny služby běžící na tomto hlavnímu uzlu. V seznamu vyberte rozevírací tlačítko vedle Spark2 Thrift Server a pak vyberte **zastavit**.

    ![Restartovat Thrift Server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Restartovat Thrift Server3")
5. Tyto kroky opakujte i na ostatních hlavnímu uzlu.

## <a name="restart-the-jupyter-service"></a>Restartujte službu Jupyter.

Spusťte webové uživatelské rozhraní Ambari, jak je znázorněno na začátku článku. V levém navigačním podokně vyberte **Jupyter**, vyberte **Akce služby** a pak vyberte **restartovat vše**. Tím se spustí služba Jupyter ve všech hlavních.

![Restartujte Jupyter.](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Restartujte Jupyter.")

## <a name="monitor-resources"></a>Sledování prostředků

Spusťte uživatelské rozhraní příze, jak je znázorněno na začátku článku. V tabulce metriky clusteru v horní části obrazovky ověřte hodnoty **využité paměti** a **Celkový počet sloupců paměti** . Pokud jsou tyto dvě hodnoty blízko, nemusí být k dispozici dostatek prostředků ke spuštění další aplikace. Totéž platí pro sloupce **virtuální jádra použité** a **virtuální jádra celkem** . Pokud je v hlavním zobrazení aplikace nechali v **přijatém** stavu a nepřechází do stavu **spuštěno** ani **selhání** , může to být také označením, že není dostatek prostředků ke spuštění.

![Omezení prostředků](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Omezení prostředků")

## <a name="kill-running-applications"></a>Ukončit běžící aplikace

1. V uživatelském rozhraní příze na levém panelu vyberte **Spustit**. V seznamu spuštěných aplikací určete aplikaci, která se má ukončit, a vyberte **ID**.

    ![Kill app1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill app1")

2. V pravém horním rohu vyberte **ukončit aplikaci** a pak vyberte **OK**.

    ![Kill app2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill app2")

## <a name="see-also"></a>Viz také

* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Pro analytiky dat

* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insight pomocí Apache Spark ve službě HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Pro Apache Spark vývojáře

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
