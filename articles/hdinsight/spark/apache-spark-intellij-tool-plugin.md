---
title: 'Azure Toolkit pro IntelliJ: Aplikace Spark - HDInsight'
description: Pomocí sady Azure Toolkit for IntelliJ můžete vyvíjet aplikace Spark napsané v Scale a odesílat je do clusteru HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a3884fdfbbc215c305053d8615d690880f4026ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314149"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Použití sady Nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight

Tento článek ukazuje, jak vyvíjet aplikace Apache Spark na Azure HDInsight pomocí modulu plug-in **Azure Toolkit** pro IntelliJ IDE. [Azure HDInsight](../hdinsight-overview.md) je spravovaná analytická služba s otevřeným zdrojovým kódem v cloudu. Služba umožňuje používat open-source architektury jako Hadoop, Apache Spark, Apache Hive a Apache Kafka.

Modul plug-in **Azure Toolkit** můžete použít několika způsoby:

* Vyvíjejte a odesílejte aplikaci Scala Spark do clusteru HDInsight Spark.
* Získejte přístup k prostředkům clusteru Azure HDInsight Spark.
* Vyvíjejte a spouštějte aplikaci Scala Spark místně.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Použití modulu plug-in Azure Toolkit for IntelliJ
> * Vývoj aplikací Apache Spark
> * Odeslání aplikace do clusteru Azure HDInsight

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Tento článek používá java verzi 8.0.202.

* IntelliJ IDEA. Tento článek používá [IntelliJ IDEA Společenství ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit pro IntelliJ.  Viz [Instalace sady nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA

Postup instalace modulu plug-in Scala:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce přejděte na **Konfigurovat** > **moduly plug-in** a otevřete okno **Moduly plug-in.**

    ![IntelliJ IDEA umožňuje scala plugin](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Vyberte **Instalovat** pro modul plug-in Scala, který se objevuje v novém okně.  

    ![IntelliJ IDEA instaluje plugin scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Spark Scala pro cluster HDInsight Spark

1. Spusťte IntelliJ IDEA a vyberte **Vytvořit nový projekt,** chcete-li otevřít okno **Nový projekt.**

2. V levém podokně vyberte **Azure Spark/HDInsight.**

3. V hlavním okně vyberte **Spark Project (Scala).**

4. V rozevíracím seznamu **Nástroj sestavení** vyberte jednu z následujících voleb:
   * **Podpora** průvodce vytvořením projektu Scala.
   * **SBT** pro správu závislostí a vytváření pro projekt Scala.

     ![Dialogové okno IntelliJ IDEA Nový projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Vyberte **další**.

6. V okně **Nový projekt** zadejte následující informace:  

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název projektu| Zadejte název.  Tento článek `myApp`používá .|  
    |Umístění&nbsp;projektu| Zadejte umístění pro uložení projektu.|
    |Sada SDK projektu| Toto pole může být při prvním použití idea prázdné.  Vyberte **Nový...** a přejděte do jdk.|
    |Verze Spark|Průvodce vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Výběr sady Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Vyberte **Finish** (Dokončit).  Může trvat několik minut, než bude projekt k dispozici.

8. Projekt Spark automaticky vytvoří artefakt pro vás. Chcete-li artefakt zobrazit, postupujte takto:

   a. Na řádku nabídek přejděte na **Strukturu projektu souborů...** > **Project Structure...**.

   b. V okně **Struktura projektu** vyberte **artefakty**.  

   c. Po zobrazení artefaktu vyberte **Zrušit.**

      ![Informace o artefaktu v dialogovém okně](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Přidejte zdrojový kód aplikace následujícím způsobem:

    a. Z projektu přejděte na **myApp** > **src** > **main** > **scala**.  

    b. Klikněte pravým tlačítkem myši na **scalu**a přejděte na **Novou** > **třídu Scala**.

   ![Příkazy pro vytvoření třídy Scala z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. V dialogovém okně **Vytvořit novou třídu Scaly** zadejte název, v rozevíracím seznamu **Druh** vyberte **Objekt** a pak vyberte **OK**.

     ![Dialogové okno Vytvořit novou třídu Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Soubor **myApp.scala** se pak otevře v hlavním zobrazení. Nahraďte výchozí kód níže uvedeným kódem:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    Kód přečte data z HVAC.csv (k dispozici ve všech clusterech HDInsight Spark), načte řádky, které mají pouze `/HVACOut` jednu číslici v sedmém sloupci v souboru CSV a zapíše výstup pod výchozí kontejner úložiště pro cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Připojení ke clusteru HDInsight

Uživatel se může [přihlásit k předplatnému Azure](#sign-in-to-your-azure-subscription)nebo [propojit cluster HDInsight](#link-a-cluster). Pro připojení k clusteru HDInsight použijte uživatelské jméno/heslo ambari nebo přihlašovací údaje spojené s doménou.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

1. Na řádku nabídek přejděte na **Zobrazit** > **nástroj Průzkumníka Windows.** > **Azure Explorer**

   ![IntelliJ IDEA zobrazuje Průzkumníka Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na uzel **Azure** a pak vyberte **Přihlásit se**.

   ![Průzkumník IntelliJ IDEA klikněte pravým tlačítkem na Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. V dialogovém okně **Přihlášení k Azure** zvolte Přihlášení k **zařízení**a pak **vyberte Přihlásit se**.

    !['IntelliJ IDEA přihlášení k přihlašovacímu zařízení azure'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. V dialogovém okně **Přihlášení zařízení Azure** klikněte na Kopírovat&**Otevřít**.

   !['IntelliJ IDEA azure device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. V rozhraní prohlížeče vložte kód a klepněte na tlačítko **Další**.

   !['Microsoft zadat kód dialog pro HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Zadejte svá pověření Azure a zavřete prohlížeč.

   !['Microsoft zadat e-mail ový dialog pro HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Po přihlášení zobrazí dialogové okno **Vybrat předplatná** všechna předplatná Azure, která jsou přidružená k přihlašovacím údajům. Vyberte předplatné a pak vyberte tlačítko **Vybrat.**

    ![Dialogové okno Select Subscriptions (Výběr předplatných)](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Z **Průzkumníka Azure**rozbalte **HDInsight** a zobrazte clustery HDInsight Spark, které jsou ve vašich předplatných.

    ![Hlavní zobrazení aplikace IntelliJ IDEA Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Chcete-li zobrazit prostředky (například účty úložiště), které jsou přidruženy ke clusteru, můžete dále rozbalit uzel názvu clusteru.

    ![Účty úložiště Azure Exploreru](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Propojení clusteru

Cluster HDInsight můžete propojit pomocí uživatelského jména spravovaného Apache Ambari. Podobně pro cluster HDInsight připojováno k doméně můžete propojit pomocí `user1@contoso.com`domény a uživatelského jména, například . Také můžete propojit cluster livy service.

1. Na řádku nabídek přejděte na **Zobrazit** > **nástroj Průzkumníka Windows.** > **Azure Explorer**

1. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na uzel **HDInsight** a pak vyberte **Propojit cluster**.

   ![Kontextová nabídka clusteru propojení Průzkumníka Azure](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Dostupné možnosti v okně **Propojit cluster** se budou lišit v závislosti na tom, jakou hodnotu vyberete z rozevíracího seznamu Typ **prostředku propojení.**  Zadejte hodnoty a pak vyberte **OK**.

    * **HDInsight Cluster**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku propojení|V rozevíracím seznamu vyberte **cluster HDInsight.**|
        |Název nebo adresa URL clusteru| Zadejte název clusteru.|
        |Typ ověřování| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        ![IntelliJ IDEA propojuje dialogové okno clusteru](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy servis**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku propojení|V rozevíracím seznamu vyberte **Livy Service.**|
        |Livy koncový bod| Zadat livy koncový bod|
        |Název clusteru| Zadejte název clusteru.|
        |Koncový bod příze|Nepovinný parametr.|
        |Typ ověřování| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        ![Dialogové okno skupiny Livy propojení IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Propojený cluster můžete zobrazit z uzlu **HDInsight.**

   ![Propojený cluster Azure Exploreru1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Můžete také odpojit cluster z **Průzkumníka Azure**.

   ![Azure Explorer odpojil cluster](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala v clusteru HDInsight Spark

Po vytvoření aplikace Scala ji můžete odeslat do clusteru.

1. Z projektu přejděte na **myApp** > **src** > **hlavní** > **scala** > **myApp**.  Klikněte pravým tlačítkem myši na **myApp**a vyberte **Odeslat aplikaci Spark** (pravděpodobně se bude nacházet v dolní části seznamu).

      ![Příkaz Odeslat aplikaci Spark do HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. V dialogovém okně **Odeslat aplikaci Spark** vyberte **1. Jiskra na HDInsight**.

3. V okně **Upravit konfiguraci** zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Skupovací clustery (pouze Linux)|Vyberte cluster HDInsight Spark, ve kterém chcete aplikaci spustit.|
    |Vyberte artefakt, který chcete odeslat.|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit výběrem tři tečky(**...**)  a výběr jiné třídy.|
    |Konfigurace úloh|Můžete změnit výchozí klíče a nebo hodnoty. Další informace naleznete v [tématu Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|V případě potřeby můžete zadat argumenty oddělené mezerou pro hlavní třídu.|
    |Odkazované sklenice a odkazované soubory|Můžete zadat cesty pro odkazované jars a soubory, pokud existuje. Můžete také procházet soubory ve virtuálním systému souborů Azure, který v současné době podporuje pouze cluster ADLS Gen 2. Další informace: [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Viz také [How to upload resources to cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Úložiště pro nahrávání úloh|Rozbalte a odhalte další možnosti.|
    |Typ úložiště|**Vrozenou možnost Použít objekt blob Azure k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte klíč úložiště.|
    |Kontejner úložiště|Po zadání **účtu úložiště** a **klíče úložiště** vyberte kontejner úložiště z rozevíracího seznamu.|

    ![Dialogové okno Odeslání jiskry](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Vyberte **SparkJobRun,** chcete-li odeslat projekt do vybraného clusteru. Karta **Vzdálená úloha Spark v clusteru** zobrazuje průběh provádění úloh y v dolní části. Aplikaci můžete zastavit kliknutím na červené tlačítko.

    ![Okno odeslání Apache Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Ladění aplikací Apache Spark místně nebo vzdáleně v clusteru HDInsight

Doporučujeme také jiný způsob odeslání aplikace Spark do clusteru. Můžete tak učinit nastavením parametrů v ide **konfigurace spustit/ladit.** Podívejte se [na debugovat aplikace Apache Spark místně nebo vzdáleně v clusteru HDInsight s Azure Toolkit pro IntelliJ až SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup ke clusterům HDInsight Spark a správa pomocí Azure Toolkit pro IntelliJ

Můžete provést různé operace pomocí Azure Toolkit pro IntelliJ.  Většina operací se schytá z **Průzkumníka Azure**.  Na řádku nabídek přejděte na **Zobrazit** > **nástroj Průzkumníka Windows.** > **Azure Explorer**

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh

1. Z Průzkumníka Azure přejděte na **HDInsight** > \<váš cluster> > **úlohy**.

    ![Uzel zobrazení úlohy Aplikace Azure Explorer IntelliJ](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. V pravém podokně karta **Zobrazení úloh Spark** zobrazuje všechny aplikace spuštěné v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    ![Podrobnosti aplikace Zobrazení úlohy Spark](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Chcete-li zobrazit základní informace o spuštěné úloze, najeďte na graf úloh. Chcete-li zobrazit graf fází a informace, které generuje každá úloha, vyberte uzel v grafu úlohy.

    ![Podrobnosti o fázi zobrazení úlohy Spark](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Chcete-li zobrazit často používané protokoly, například *Ovladač Stderr*, *Driver Stdout*a *Informace o adresáři*, vyberte kartu **Protokol.**

    ![Podrobnosti protokolu zobrazení úloh y Spark](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Můžete zobrazit uj. Vyberte odkaz v horní části okna.

### <a name="access-the-spark-history-server"></a>Přístup k serveru historie Spark

1. V Průzkumníkovi Azure rozbalte **HDInsight**, klikněte pravým tlačítkem myši na název clusteru Spark a pak vyberte **Otevřít uživatelské rozhraní Historie Sparku**.  
2. Po zobrazení výzvy zadejte přihlašovací údaje správce clusteru, které jste zadali při nastavování clusteru.

3. Na řídicím panelu serveru historie Spark můžete použít název aplikace k vyhledání aplikace, kterou jste právě dokončili. V předchozím kódu nastavíte název aplikace `val conf = new SparkConf().setAppName("myApp")`pomocí . Název aplikace Spark je **myApp**.

### <a name="start-the-ambari-portal"></a>Spuštění portálu Ambari

1. V Průzkumníkovi Azure rozbalte **HDInsight**, klikněte pravým tlačítkem myši na název clusteru Spark a pak vyberte **Otevřít portál pro správu clusteru (Ambari).**  

2. Po zobrazení výzvy zadejte přihlašovací údaje správce clusteru. Tato pověření jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure

Ve výchozím nastavení azure toolkit pro IntelliJ uvádí clustery Spark ze všech vašich předplatných Azure. V případě potřeby můžete zadat odběry, ke kterým chcete získat přístup.  

1. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na kořenový uzel **Azure** a pak vyberte **Vybrat předplatná**.  

2. V okně **Vybrat předplatná** zrušte zaškrtnutí políček vedle předplatných, ke kterým nechcete mít přístup, a pak vyberte **Zavřít**.

## <a name="spark-console"></a>Konzola Spark

Můžete spustit Spark Local Console (Scala) nebo Spark Livy Interactive Session Console (Scala).

### <a name="spark-local-consolescala"></a>Jiskra místní konzole (Scala)

Ujistěte se, že jste winutils splnili. EXE předpoklad.

1. Na řádku nabídek přejděte na **Spusťte** > **upravit konfigurace...**.

2. Z okna **Spustit/ladit konfigurace** v levém podokně přejděte na **Apache Spark na HDInsight** > **[Spark na HDInsight] myApp**.

3. V hlavním okně vyberte **`Locally Run`** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Hlavní třída práce|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit výběrem tři tečky(**...**)  a výběr jiné třídy.|
    |Proměnné prostředí|Ujistěte se, že hodnota HADOOP_HOME je správná.|
    |Umístění winutils.exe|Ujistěte se, že cesta je správná.|

    ![Konfigurace místní konzoly](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Z projektu přejděte na **myApp** > **src** > **hlavní** > **scala** > **myApp**.  

6. Na řádku nabídek přejděte na **Tools** > **Spark Console** > **Run Spark Local Console (Scala).**

7. Pak se mohou zobrazit dvě dialogová okna s dotazem, zda chcete automaticky opravit závislosti. Pokud ano, vyberte **možnost Automatická oprava**.

    ![Dialogové okno IntelliJ IDEA Spark Auto Fix1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![Dialogové okno IntelliJ IDEA Spark Auto Fix2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Konzole by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL+Enter.  Výsledek se zobrazí. Místní konzolu můžete ukončit kliknutím na červené tlačítko.

    ![Výsledek místní konzoly IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interaktivní reliéfní konzole (Scala)

1. Na řádku nabídek přejděte na **Spusťte** > **upravit konfigurace...**.

2. Z okna **Spustit/ladit konfigurace** v levém podokně přejděte na **Apache Spark na HDInsight** > **[Spark na HDInsight] myApp**.

3. V hlavním okně vyberte **`Remotely Run in Cluster`** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Skupovací clustery (pouze Linux)|Vyberte cluster HDInsight Spark, ve kterém chcete aplikaci spustit.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit výběrem tři tečky(**...**)  a výběr jiné třídy.|

    ![Konfigurace sady interaktivních konzol](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Z projektu přejděte na **myApp** > **src** > **hlavní** > **scala** > **myApp**.  

6. Na řádku nabídek přejděte na **Tools** > **Spark Console** > **Run Spark Livy Interactive Session Console (Scala).**

7. Konzole by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL+Enter.  Výsledek se zobrazí. Místní konzolu můžete ukončit kliknutím na červené tlačítko.

    ![Výsledek interaktivní konzoly IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark Console

Je vhodné předvídat výsledek skriptu odesláním nějakého kódu do místní konzole nebo Livy Interactive Session Console (Scala). Můžete zvýraznit nějaký kód v souboru Scala a pak kliknout pravým tlačítkem na **Odeslat výběr do spark console**. Vybraný kód bude odeslán do konzoly. Výsledek se zobrazí za kódem v konzole. Konzola zkontroluje chyby, pokud existují.  

   ![Odeslat výběr do konzoly Spark Console](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrace s HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Připojení ke clusteru HDInsight ESP pomocí zprostředkovatele ID (HIB)

Podle běžných kroků se můžete přihlásit k předplatnému Azure a připojit se k clusteru HDInsight ESP pomocí služby ID Broker (HIB). Po přihlášení se seznam clusterů zobrazí v Azure Exploreru. Další pokyny najdete v tématu [Připojení k clusteru HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spuštění aplikace Spark Scala v clusteru HDInsight ESP s id brokerem (HIB)

Můžete postupovat podle normálních kroků k odeslání úlohy do clusteru HDInsight ESP s ID Broker (HIB). Další pokyny [najdete v nabídce Spuštění aplikace Spark Scala v clusteru HDInsight Spark.](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)

Nahrajeme potřebné soubory do složky s názvem s vaším přihlašovacím účtem a cestu k nahrání uvidíte v konfiguračním souboru.

   ![nahrát cestu v konfiguraci](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Konzola Spark v clusteru HDInsight ESP s ID Brokerem (HIB)

Můžete spustit Spark Local Console (Scala) nebo Spark Livy Interactive Session Console (Scala) v clusteru HDInsight ESP s ID Broker (HIB). Další pokyny naleznete v [konzole Spark Console.](#spark-console)

   > [!NOTE]  
   > Pro cluster HDInsight ESP s Id Broker (HIB) není v současné době [podporováno propojení clusteru](#link-a-cluster) a [vzdálenéladění aplikací Apache Spark.](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster)

## <a name="reader-only-role"></a>Role pouze pro čtenáře

Když uživatelé odešlou úlohu do clusteru s oprávněním role pouze pro čtenáře, je vyžadována pověření Ambari.

### <a name="link-cluster-from-context-menu"></a>Propojení clusteru z místní nabídky

1. Přihlaste se pomocí účtu role pouze pro čtenáře.

2. Z **Průzkumníka Azure**rozbalte **HDInsight** a zobrazte clustery HDInsight, které jsou ve vašem předplatném. Clustery označené **"Role:Reader"** mají pouze oprávnění role pouze pro čtení.

    !['IntelliJ Azure Explorer Role:Reader'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Klikněte pravým tlačítkem myši na cluster s oprávněním role pouze pro čtení. Vyberte **Propojit tento cluster** z kontextové nabídky a propojit cluster. Zadejte uživatelské jméno a heslo Ambari.

    ![IntelliJ Azure Explorer propojuje tento cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Pokud je cluster úspěšně propojen, hdinsight se aktualizuje.
   Fáze clusteru se propojí.
  
    ![Propojený dialog IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Propojit cluster rozbalením uzlu Úlohy

1. Klikněte na uzel **Úlohy,** zobrazí se okno **Odepřený přístup k úlohám clusteru.**

2. Chcete-li propojit cluster, klepněte na odkaz **na tento cluster.**

    ![Dialogové okno Odepření přístupu úlohy clusteru](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Propojit cluster z okna Konfigurace spuštění/ladění

1. Vytvořte konfiguraci HDInsight. Pak vyberte **možnost Vzdálené spuštění v clusteru**.

2. Vyberte cluster, který má oprávnění role pouze pro čtení pro **clustery Spark (pouze Linux)**. Zobrazí se varovná zpráva. Chcete-li propojit cluster, můžete klepnout na odkaz na **tento cluster.**

   ![Vytvoření konfigurace spuštění/ladění IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Zobrazit účty úložiště

* U clusterů s oprávněním pouze pro čtení klikněte na uzel **Účty úložiště,** zobrazí se okno **Odepřeno přístupu k úložišti.** Kliknutím na **Otevřít Průzkumníka úložiště** otevřete Průzkumníka úložišť.

   !['IntelliJ IDEA Storage Access denied' 'IntelliJ IDEA Storage Access Denied' 'IntelliJ IDEA Storage Access Denied' 'In](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Tlačítko IntelliJ IDEA Storage Access Denied](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* U propojených clusterů klikněte na uzel **Účty úložiště,** objeví se okno **Odepřeno přístupu k úložišti.** Kliknutím na **Otevřít Azure Storage** otevřete Průzkumníka úložiště.

   !['IntelliJ IDEA Přístup k úložišti odepřen2'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Tlačítko IntelliJ IDEA Storage Access Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převod existujících aplikací IntelliJ IDEA na Azure Toolkit for IntelliJ

Existující aplikace Spark Scala, které jste vytvořili v Aplikaci IntelliJ IDEA, můžete převést tak, aby byly kompatibilní s Azure Toolkit pro IntelliJ. Potom můžete použít modul plug-in k odeslání aplikací do clusteru HDInsight Spark.

1. Pro existující aplikaci Spark Scala, která byla vytvořena pomocí `.iml` IntelliJ IDEA, otevřete přidružený soubor.

2. Na kořenové úrovni je prvek **modulu,** jako je následující text:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   Upravte prvek, `UniqueKey="HDInsightTool"` který chcete přidat, aby prvek **modulu** vypadal jako následující text:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Uložte změny. Vaše aplikace by teď měla být kompatibilní s Azure Toolkit pro IntelliJ. Můžete ji otestovat kliknutím pravým tlačítkem myši na název projektu v aplikaci Project. Rozbalovací nabídka má nyní možnost **Odeslat aplikaci Spark do HDInsight**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Do pole **Hledat** v horní části zadejte **HDInsight**.

1. V části **Služby** **vyberte clustery HDInsight** .

1. V seznamu clusterů HDInsight, který se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro tento článek.

1. Vyberte **Odstranit**. Vyberte **ano**.

![Portál Azure odstraní cluster HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili používat modul plug-in Azure Toolkit pro IntelliJ k vývoji aplikací Apache Spark napsaných v [Scala](https://www.scala-lang.org/). Pak je odeslal do clusteru HDInsight Spark přímo z integrovaného vývojového prostředí IntelliJ (IDE). Přejdete k dalšímu článku a zjistěte, jak lze data zaregistrovaná v Apache Spark vtáhnout do analytického nástroje BI, jako je Power BI.

> [!div class="nextstepaction"]
> [Analýza dat Apache Spark pomocí Power BI](apache-spark-use-bi-tools.md)
