---
title: Správa prostředků v clusteru Apache Spark v Azure HDInsight
description: Další informace o použití spravovat prostředky pro clustery Spark v Azure HDInsight pro zajištění lepšího výkonu.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 8072d72dbb164d5012ad42d5cba66c8b425e99a1
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787899"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Správa prostředků v clusteru Apache Spark v Azure HDInsight 

Zjistěte, jak získat přístup k rozhraních, jako je [Apache Ambari](https://ambari.apache.org/) uživatelského rozhraní, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) uživatelského rozhraní a Server historie Sparku, který je přidružený k vaší [Apache Spark](https://spark.apache.org/) clusteru a jak vylaďte konfiguraci clusteru pro zajištění optimálního výkonu.

**Požadavky:**

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Otevřete webové uživatelské rozhraní Ambari

Apache Ambari slouží k monitorování clusteru a provést změny konfigurace. Další informace najdete v tématu [spravovat Apache Hadoop clusterů v HDInsight pomocí webu Azure portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Otevřete Server historie Sparku

Server historie Sparku je ve webovém uživatelském rozhraní pro dokončené a spuštěné aplikace Spark. Je to rozšíření Spark webového uživatelského rozhraní.

**Chcete-li otevřít rozhraní Web Server historie Sparku**

1. Z [webu Azure portal](https://portal.azure.com/), otevřete Spark cluster. Další informace najdete v tématu [výpisu a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **Server historie Sparku**

    ![Server historie sparku](./media/apache-spark-resource-manager/launch-history-server.png "Server historie Sparku")

    Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru Spark. Server historie Sparku můžete otevřít také tak, že přejdete na následující adresu URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Nahraďte <ClusterName> názvem clusteru Spark.

Server historie Sparku webové uživatelské rozhraní vypadá takto:

![Server historie Sparku pro HDInsight](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Otevřete uživatelské rozhraní Yarn
Rozhraní YARN můžete použít k monitorování aplikací, které jsou aktuálně spuštěné v clusteru Spark.

1. Z [webu Azure portal](https://portal.azure.com/), otevřete Spark cluster. Další informace najdete v tématu [výpisu a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Z **rychlé odkazy**, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **YARN**.

    ![Spuštění uživatelského rozhraní YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Alternativně můžete také spustit z uživatelského rozhraní Ambari Uživatelském rozhraní YARN. Chcete-li spustit uživatelské rozhraní Ambari, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **řídicí panel clusteru HDInsight**. Z uživatelského rozhraní Ambari, klikněte na tlačítko **YARN**, klikněte na tlačítko **rychlé odkazy**, klikněte na aktivní Resource Manageru a pak klikněte na **uživatelského rozhraní správce prostředků**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimalizace clusterů pro aplikací Spark
Jsou tři klíče parametry, které lze použít pro konfigurace Spark v závislosti na požadavcích aplikace `spark.executor.instances`, `spark.executor.cores`, a `spark.executor.memory`. Vykonavatele je proces spuštěn z aplikace Spark. Běží na pracovní uzel a zodpovídá provádět úkoly pro aplikaci. Výchozí číslo prováděcí moduly a velikostí prováděcí modul pro každý cluster se počítá na základě počtu pracovních uzlů a velikost uzlu pracovního procesu. Tyto informace jsou uloženy v `spark-defaults.conf` o hlavních uzlech clusteru.

Tři konfigurační parametry se dají konfigurovat na úrovni clusteru (pro všechny aplikace, které běží na clusteru) nebo je možné zadat pro každou jednotlivou aplikaci.

### <a name="change-the-parameters-using-ambari-ui"></a>Změna parametrů pomocí uživatelského rozhraní Ambari
1. Z uživatelského rozhraní Ambari, klikněte na tlačítko **Spark**, klikněte na tlačítko **Configs**a potom rozbalte **vlastní spark – výchozí hodnoty**.

    ![Nastavit parametry pomocí Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Výchozí hodnoty jsou dobré si co čtyři aplikací Spark spuštěné současně v clusteru. Tyto hodnoty můžete změnit z uživatelského rozhraní, jak je znázorněno na následujícím snímku obrazovky:

    ![Nastavit parametry pomocí Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Klikněte na tlačítko **Uložit** se uložit změny konfigurace. V horní části stránky zobrazí se výzva k restartování všechny ovlivněné služby. Klikněte na **Restartovat**.

    ![Restartování služeb](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Změna parametrů pro aplikace běžící v Poznámkový blok Jupyter
Pro aplikace běžící v poznámkovém bloku Jupyter, můžete použít `%%configure` magic k provádění změn konfigurace. V ideálním případě je třeba provést tyto změny na začátku aplikace před spuštěním vaší první buňky kódu. To zajišťuje, že tato konfigurace používá Livy relaci, když se vytvoří. Pokud chcete změnit konfiguraci v pozdější fázi v aplikaci, je nutné použít `-f` parametru. Díky tomu je však ke ztrátě všech průběhu v aplikaci.

Následující fragment kódu ukazuje, jak změnit konfiguraci pro aplikace běžící v Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Parametry konfigurace musí být předán v podobě řetězce JSON a musí být na dalším řádku za všechno, jak je znázorněno v příkladu sloupce.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Změna parametrů pro aplikaci odesílat pomocí skriptu spark-submit
Následující příkaz je příklad toho, jak změnit parametry konfigurace pro aplikaci služby batch, které je odeslána pomocí `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Změna parametrů pro aplikaci odesílat pomocí cURL
Následující příkaz je příklad toho, jak změnit parametry konfigurace pro aplikaci služby batch, které je odeslána pomocí příkazu cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Změna těchto parametrů na Spark Thrift Server
Spark Thrift Server poskytuje JDBC/ODBC přístup ke clusteru Spark a slouží k dotazů Spark SQL service. Nástroje, jako je Power BI, Tableau atd. použijte protokol ODBC ke komunikaci s Server Spark Thrift ke spuštění dotazů Spark SQL jako aplikace Spark. Po vytvoření clusteru Spark jsou spuštěny dva výskyty Spark Thrift Server, jednu na každý hlavního uzlu. Každý Server Spark Thrift se zobrazí jako aplikace Spark v Uživatelském rozhraní YARN.

Přidělení dynamické prováděcí modul Sparku používá Spark Thrift Server a proto `spark.executor.instances` se nepoužívá. Místo toho používá Spark Thrift Server `spark.dynamicAllocation.minExecutors` a `spark.dynamicAllocation.maxExecutors` k určení počtu prováděcího modulu. Parametry konfigurace `spark.executor.cores` a `spark.executor.memory` se používá k úpravě velikosti prováděcího modulu. Tyto parametry můžete změnit, jak je znázorněno v následujícím postupu:

* Rozbalte **Advanced spark thrift-sparkconf** kategorie se aktualizovat parametry `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, a `spark.executor.memory`.

    ![Nakonfigurujte Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Rozbalte **vlastní spark-thrift-sparkconf** kategorii k aktualizaci parametru `spark.executor.cores`.

    ![Nakonfigurujte Spark thrift server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Změna paměti ovladače systému Spark Thrift Server
Spark Thrift Server ovladač paměti je nakonfigurovaný tak, aby 25 % velikost paměti RAM hlavního uzlu, za předpokladu, že celková velikost paměti RAM hlavního uzlu je větší než 14 GB. Uživatelské rozhraní Ambari slouží ke změně konfigurace paměti ovladače, jak je znázorněno na následujícím snímku obrazovky:

* Z uživatelského rozhraní Ambari, klikněte na tlačítko **Spark**, klikněte na tlačítko **Configs**, rozbalte **Advanced spark env**a pak zadejte hodnotu pro **spark_thrift_cmd_opts**.

    ![Nakonfigurujte server Spark thrift paměti RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Uvolnit prostředky clusteru Spark
Z důvodu dynamické přidělování Spark jediné prostředky, které se spotřebovávají thrift serveru prostředků pro dvě aplikace hlavních serverů. Uvolnit tyto prostředky, je potřeba zastavit Thrift Server služby spuštěné v clusteru.

1. Z uživatelského rozhraní Ambari, v levém podokně klikněte na tlačítko **Spark**.
2. Na další stránce klikněte na tlačítko **Spark Thrift servery**.

    ![Restartujte thrift server](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Měli byste vidět dva hlavní uzly, na kterých běží Spark Thrift Server. Klikněte na jeden z hlavních uzlech.

    ![Restartujte thrift server](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Na další stránce uvedeny všechny služby spuštěné v této hlavní uzel. V seznamu klikněte na rozevírací tlačítko vedle Spark Thrift Server a potom klikněte na tlačítko **Zastavit**.

    ![Restartujte thrift server](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Tento postup opakujte u dalších hlavního uzlu i.

## <a name="restart-the-jupyter-service"></a>Restartujte službu Jupyter
Spuštění webové uživatelské rozhraní Ambari, jak je znázorněno na začátku tohoto článku. V levém navigačním podokně klikněte na tlačítko **Jupyter**, klikněte na tlačítko **akce služby**a potom klikněte na tlačítko **restartujte všechny**. To spustí službu Jupyter na všechny hlavní uzly.

![Restartujte Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "restartovat Jupyter")

## <a name="monitor-resources"></a>Sledování prostředků
Spuštění uživatelského rozhraní Yarn, jak je znázorněno na začátku tohoto článku. V tabulce metriky clusteru na obrazovce, zkontrolujte hodnoty **paměti používá** a **celkové paměti** sloupce. Pokud tyto dvě hodnoty jsou si blízké, nemusí být dostatek prostředků ke spuštění další aplikace. Totéž platí i pro **využitých** a **celkových virtuálních jader** sloupce. Také v hlavní zobrazení, pokud je aplikace nechali v **PŘIJATO** stavu a není přechod do **systémem** ani **NEÚSPĚŠNÉ** stavu, může to být i jako ukazatel toho, který nedostává dostatek prostředků ke spuštění.

![Omezení prostředků](./media/apache-spark-resource-manager/resource-limit.png "limitu prostředků")

## <a name="kill-running-applications"></a>Ukončit spuštěné aplikace
1. V Uživatelském rozhraní Yarn na levém panelu klikněte na tlačítko **systémem**. Seznam spuštěných aplikací určete aplikace ukončeny a klikněte na **ID**.

    ![Ukončit App1](./media/apache-spark-resource-manager/kill-app1.png "Kill počítače App1")

2. Klikněte na tlačítko **ukončit aplikace** v pravém horním rohu, pak klikněte na **OK**.

    ![Ukončit počítači App2](./media/apache-spark-resource-manager/kill-app2.png "Kill počítači App2")

## <a name="see-also"></a>Další informace najdete v tématech
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Pro datové analytiky

* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight analýza dat telemetrie používat Apache Spark v HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Použití Caffe pro distribuované obsáhlého learningu v Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Pro vývojáře v Apache Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
