---
title: 'Azure Toolkit for IntelliJ: aplikace Spark – HDInsight'
description: Azure Toolkit for IntelliJ můžete použít k vývoji aplikací Spark napsaných v Scala a jejich odeslání do clusteru HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 29fb96dc83ada329910844506838dee461321343
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866331"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Vytvoření Apache Spark aplikací pro cluster HDInsight pomocí Azure Toolkit for IntelliJ

Tento článek ukazuje, jak vyvíjet aplikace Apache Spark v Azure HDInsight pomocí modulu plug-in **Azure Toolkit** pro prostředí IDE pro IntelliJ. [Azure HDInsight](../hdinsight-overview.md) je spravovaná, open source analytická služba v cloudu. Služba umožňuje používat Open Source architektury, jako jsou Hadoop, Apache Spark, Apache Hive a Apache Kafka.

Modul plug-in **Azure Toolkit** můžete použít několika způsoby:

* Vývoj a odeslání aplikace Scala Spark do clusteru HDInsight Spark.
* Přístup k prostředkům Azure HDInsight Spark clusteru.
* Vývoj a spouštění aplikace Scala Spark lokálně

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Použití modulu plug-in Azure Toolkit for IntelliJ
> * Vývoj aplikací Apache Spark
> * Odeslání aplikace do clusteru Azure HDInsight

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Tento článek používá Java verze 8.0.202.

* IntelliJ nápad. V tomto článku se používá [INTELLIJ nápad Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Viz [instalace Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA

Postup instalace modulu plug-in Scala:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce přejděte na **Konfigurace**  >  **modulů plug-** in a otevřete okno **moduly plug-in** .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png" alt-text="IntelliJ nápad povoluje modul plug-in Scala" border="true":::

3. Vyberte **nainstalovat** pro modul plug-in Scala, který je vybraný v novém okně.  

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png" alt-text="IntelliJ nápad nainstaluje modul plug-in Scala" border="true":::

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Spark Scala pro cluster HDInsight Spark

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .

2. V levém podokně vyberte **Azure Spark/HDInsight** .

3. V hlavním okně vyberte **projekt Spark (Scala)** .

4. V rozevíracím seznamu **Nástroj sestavení** vyberte jednu z následujících možností:
   * **Maven** for Scala – Průvodce vytvořením projektu – podpora.
   * **SBT** pro správu závislostí a sestavení projektu Scala.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png" alt-text="IntelliJ nápad – nový projekt – dialogové okno" border="true":::

5. Vyberte **Další**.

6. V okně **Nový projekt** zadejte následující informace:  

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název projektu| Zadejte název.  Tento článek používá `myApp` .|  
    |&nbsp;Umístění projektu| Zadejte umístění pro uložení projektu.|
    |Projektový SDK| Toto pole může být prázdné při prvním použití NÁPADu.  Vyberte **Nový...** a přejděte k JDK.|
    |Verze Sparku|Průvodce vytvořením integruje správnou verzi sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.3.0 (Scala 2.11.8)**.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-new-project.png" alt-text="Výběr sady Apache Spark SDK" border="true":::

7. Vyberte **Dokončit**.  Může to trvat několik minut, než bude projekt k dispozici.

8. Projekt Spark automaticky vytvoří artefakt za vás. Chcete-li zobrazit artefakt, proveďte následující kroky:

   a. Z řádku nabídek přejděte na **soubor**  >  **struktura projektu...**.

   b. V okně **struktura projektu** vyberte **artefakty**.  

   c. Po zobrazení artefaktu vyberte **Zrušit**  .

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png" alt-text="Informace o artefaktu v dialogovém okně" border="true":::

9. Přidejte zdrojový kód aplikace pomocí následujících kroků:

    a. Z projektu přejděte na **MyApp**  >  **Src**  >  **Main**  >  **Scala**.  

    b. Klikněte pravým tlačítkem na **Scala** a potom přejděte k **nové**  >  **třídě Scala**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png" alt-text="Příkazy pro vytvoření třídy Scala z projektu" border="true":::

   c. V dialogovém okně **vytvořit novou třídu Scala** zadejte název, vyberte **objekt** v rozevíracím seznamu **typ** a pak vyberte **OK**.

     :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png" alt-text="Dialogové okno vytvořit novou třídu Scala" border="true":::

   d. Soubor **MyApp. Scala** se pak otevře v hlavním zobrazení. Nahraďte výchozí kód následujícím kódem, který najdete níže:  

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

    Kód načte data z HVAC.csv (k dispozici ve všech clusterech HDInsight Spark), načte řádky, které mají v souboru CSV jenom jednu číslici, a zapíše výstup do `/HVACOut` výchozího kontejneru úložiště pro cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Připojení ke clusteru HDInsight

Uživatel se může [přihlásit k předplatnému Azure](#sign-in-to-your-azure-subscription)nebo [propojit cluster HDInsight](#link-a-cluster). K připojení ke clusteru HDInsight použijte přihlašovací údaje Ambari nebo uživatelské jméno nebo heslo připojené k doméně.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

1. V řádku nabídek přejděte k **zobrazení**  >  **nástrojů Windows**  >  **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png" alt-text="IntelliJ nápad ukazuje Azure Explorer" border="true":::

2. V Průzkumníku Azure klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **Přihlásit** se.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png" alt-text="Průzkumník nápadů pro IntelliJ klikněte pravým tlačítkem myši na Azure." border="true":::

3. V dialogovém okně **přihlášení do Azure** zvolte přihlášení k **zařízení** a pak vyberte **Přihlásit** se.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png" alt-text="IntelliJ Login zařízení pro přihlášení k Azure" border="true":::

4. V dialogovém okně **přihlášení k zařízení Azure** klikněte na **Kopírovat&otevřít**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png" alt-text="IntelliJ nápad Login pro zařízení Azure" border="true":::

5. V rozhraní prohlížeče vložte kód a potom klikněte na tlačítko **Další**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png" alt-text="Dialog Microsoft zadejte kód pro HDI." border="true":::

6. Zadejte svoje přihlašovací údaje Azure a pak zavřete prohlížeč.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png" alt-text="' Dialog zadat e-mailovou zprávu Microsoftu pro HDI '" border="true":::

7. Po přihlášení se v dialogovém okně **Vybrat odběry** zobrazí všechna předplatná Azure, která jsou přidružená k přihlašovacím údajům. Vyberte své předplatné a pak vyberte tlačítko **Vybrat** .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png" alt-text="Dialogové okno Select Subscriptions (Výběr předplatných)" border="true":::

8. V **Azure Exploreru** rozbalte **HDInsight** , abyste viděli clustery HDInsight Spark, které jsou ve vašich předplatných.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png" alt-text="IntelliJ nápad v hlavním zobrazení Průzkumníka Azure" border="true":::

9. Chcete-li zobrazit prostředky (například účty úložiště), které jsou přidruženy ke clusteru, můžete dále rozšířit uzel název clusteru.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png" alt-text="Účty úložiště Azure Explorer" border="true":::

### <a name="link-a-cluster"></a>Propojení clusteru

Cluster HDInsight můžete propojit pomocí spravovaného uživatelského jména Apache Ambari. Podobně v případě clusteru HDInsight připojeného k doméně můžete propojit s doménou a uživatelským jménem, jako je například `user1@contoso.com` . Také můžete propojit cluster služby Livy Service.

1. V řádku nabídek přejděte k **zobrazení**  >  **nástrojů Windows**  >  **Azure Explorer**.

1. V Průzkumníku Azure klikněte pravým tlačítkem na uzel **HDInsight** a pak vyberte **propojit cluster**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png" alt-text="Místní nabídka propojených clusterů v Průzkumníkovi Azure" border="true":::

1. Dostupné možnosti v okně **propojit cluster** se budou lišit v závislosti na vybrané hodnotě v rozevíracím seznamu **typ prostředku propojení** .  Zadejte hodnoty a pak vyberte **OK**.

    * **Cluster HDInsight**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku propojení|Z rozevíracího seznamu vyberte **cluster HDInsight** .|
        |Název nebo adresa URL clusteru| Zadejte název clusteru.|
        |Typ ověřování| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí nastavení je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png" alt-text="Odkaz na IntelliJ nápad – dialogové okno clusteru" border="true":::

    * **Služba Livy**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku propojení|V rozevíracím seznamu vyberte **Služba Livy** .|
        |Koncový bod Livy| Zadejte koncový bod Livy.|
        |Název clusteru| Zadejte název clusteru.|
        |Koncový bod příze|Nepovinný parametr.|
        |Typ ověřování| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí nastavení je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        :::image type="content" source="./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png" alt-text="Dialog Livy clusteru IntelliJ nápad Link" border="true":::

1. Propojený cluster můžete zobrazit z uzlu **HDInsight** .

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png" alt-text="Propojené Cluster1 Průzkumníka Azure" border="true":::

1. Cluster taky můžete odpojit od **Azure Exploreru**.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png" alt-text="Cluster s nepropojeným průzkumníkem Azure Explorer" border="true":::

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala v clusteru HDInsight Spark

Po vytvoření aplikace Scala ji můžete odeslat do clusteru.

1. Z projektu přejděte do **MyApp**  >  **Src**  >  **Main**  >  **Scala**  >  **MyApp**.  Klikněte pravým tlačítkem myši na položku **MyApp** a vyberte možnost **Odeslat aplikaci Spark** (ta bude pravděpodobně umístěna na konci seznamu).

      :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png" alt-text="Příkaz Odeslat aplikaci Spark do HDInsight" border="true":::

2. V dialogovém okně **Odeslat aplikaci Spark** vyberte **1. Spark ve službě HDInsight**.

3. V okně **Upravit konfiguraci** zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Clustery Spark (jenom Linux)|Vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.|
    |Vyberte artefakt, který se má odeslat.|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky (**...**).  a volba jiné třídy.|
    |Konfigurace úloh|Můžete změnit výchozí klíče a hodnoty. Další informace najdete v článku [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|V případě potřeby můžete zadat argumenty oddělené mezerou pro hlavní třídu.|
    |Odkazované jar a odkazované soubory|Můžete zadat cesty pro odkazované jar a soubory, pokud existují. Můžete také procházet soubory ve virtuálním systému souborů Azure, který aktuálně podporuje jenom cluster ADLS Gen 2. Další informace: [Apache Spark konfiguraci](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Viz také [Postup nahrání prostředků do clusteru](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Úložiště nahrávání úlohy|Rozbalením zobrazíte další možnosti.|
    |Typ úložiště|Vyberte **použít Azure Blob k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte svůj klíč úložiště.|
    |Kontejner úložiště|Po zadání **účtu úložiště** a **klíče úložiště** vyberte z rozevíracího seznamu svůj kontejner úložiště.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Dialogové okno pro odeslání Sparku" border="true":::

4. Vyberte **SparkJobRun** a odešlete projekt do vybraného clusteru. **Vzdálená úloha Spark na kartě cluster** zobrazuje průběh provádění úlohy v dolní části. Aplikaci můžete zastavit kliknutím na tlačítko červené.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png" alt-text="Okno pro odeslání Apache Spark" border="true":::

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Místní nebo vzdálené ladění aplikací Apache Spark v clusteru HDInsight

Doporučujeme také další způsob, jak odeslat aplikaci Spark do clusteru. To lze provést nastavením parametrů v integrovaném vývojovém prostředí (IDE) pro **spuštění nebo ladění** . Viz téma [ladění Apache Spark aplikací místně nebo vzdáleně v clusteru HDInsight s Azure Toolkit for IntelliJ prostřednictvím SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup k clusterům HDInsight Spark a jejich správa pomocí Azure Toolkit for IntelliJ

Pomocí Azure Toolkit for IntelliJ můžete provádět různé operace.  Většina operací se spouští z **Průzkumníka Azure**.  V řádku nabídek přejděte k **zobrazení**  >  **nástrojů Windows**  >  **Azure Explorer**.

### <a name="access-the-job-view"></a>Přístup k zobrazení úlohy

1. V Azure Exploreru přejděte na úlohy **HDInsight**  >  \<Your Cluster>  >  .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png" alt-text="Uzel zobrazení úloh v Průzkumníkovi Azure IntelliJ" border="true":::

2. V pravém podokně karta **zobrazení úlohy Spark** zobrazí všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png" alt-text="Podrobnosti o aplikaci pro zobrazení úlohy Spark" border="true":::

3. Pokud chcete zobrazit základní informace o spuštěné úloze, najeďte myší na graf úlohy. Chcete-li zobrazit graf fází a informace, které Každá úloha generuje, vyberte uzel v grafu úlohy.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png" alt-text="Podrobnosti fáze úlohy Spark zobrazení úloh" border="true":::

4. Pokud chcete zobrazit často používané protokoly, jako je například *ovladač stderr*, *ovladač stdout* a *informace o adresáři*, vyberte kartu **protokol** .

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png" alt-text="Podrobnosti protokolu zobrazení úlohy Sparku" border="true":::

5. Můžete zobrazit uživatelské rozhraní historie Spark a uživatelské rozhraní PŘÍZe (na úrovni aplikace). Vyberte odkaz v horní části okna.

### <a name="access-the-spark-history-server"></a>Přístup k serveru historie Sparku

1. V Azure Exploreru rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít historii Spark – uživatelské rozhraní**.  
2. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru, které jste zadali při nastavování clusteru.

3. Na řídicím panelu serveru historie Spark můžete použít název aplikace k vyhledání aplikace, kterou jste právě dokončili. V předchozím kódu nastavíte název aplikace pomocí `val conf = new SparkConf().setAppName("myApp")` . Název vaší aplikace Spark je **MyApp**.

### <a name="start-the-ambari-portal"></a>Spustit portál Ambari

1. V Azure Exploreru rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít cluster portál pro správu (Ambari)**.  

2. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. Tyto přihlašovací údaje jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure

Ve výchozím nastavení Azure Toolkit for IntelliJ vypíše Clustery Spark ze všech vašich předplatných Azure. V případě potřeby můžete zadat odběry, ke kterým chcete získat přístup.  

1. V Průzkumníku Azure klikněte pravým tlačítkem na kořenový uzel **Azure** a vyberte **Vybrat odběry**.  

2. V okně **Vybrat odběry** zrušte zaškrtnutí políček u předplatných, ke kterým nechcete získat přístup, a pak vyberte **Zavřít**.

## <a name="spark-console"></a>Konzola Spark

Můžete spustit místní konzolu Spark (Scala) nebo spustit konzolu Spark Livy Interactive Session (Scala).

### <a name="spark-local-consolescala"></a>Místní konzola Spark (Scala)

Ujistěte se, že jste splnili požadavky na WINUTILS.EXE.

1. Z řádku nabídek přejděte na **Spustit**  >  **úpravy konfigurace...**.

2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v HDInsight**  >  **[Spark v HDInsight] MyApp**.

3. V hlavním okně vyberte **`Locally Run`** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Hlavní třída úlohy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky (**...**).  a volba jiné třídy.|
    |Proměnné prostředí|Zajistěte, aby byla hodnota HADOOP_HOME správná.|
    |Umístění WINUTILS.exe|Zkontrolujte, zda je cesta správná.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/console-set-configuration.png" alt-text="Konfigurace sady místních konzol" border="true":::

5. Z projektu přejděte do **MyApp**  >  **Src**  >  **Main**  >  **Scala**  >  **MyApp**.  

6. V řádku nabídek přejděte do konzoly **nástroje**  >  **Spark**  >  **spustit prostředí Spark místní konzola (Scala)**.

7. Pak se můžou zobrazit dvě dialogová okna, která vás požádají, pokud chcete automaticky opravovat závislosti. Pokud ano, vyberte možnost **automaticky opravit**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png" alt-text="IntelliJ nápad Spark auto Fix Dialog1" border="true":::

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png" alt-text="IntelliJ nápad Spark auto Fix dialog2" border="true":::

8. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName` a stiskněte kombinaci kláves CTRL + ENTER.  Zobrazí se výsledek. Místní konzolu můžete ukončit kliknutím na tlačítko červené.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/local-console-result.png" alt-text="Výsledek místní konzoly IntelliJ" border="true":::

### <a name="spark-livy-interactive-session-consolescala"></a>Konzola pro interaktivní relace Spark Livy (Scala)

1. Z řádku nabídek přejděte na **Spustit**  >  **úpravy konfigurace...**.

2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v HDInsight**  >  **[Spark v HDInsight] MyApp**.

3. V hlavním okně vyberte **`Remotely Run in Cluster`** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Clustery Spark (jenom Linux)|Vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky (**...**).  a volba jiné třídy.|

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png" alt-text="Konfigurace sady interaktivní konzoly" border="true":::

5. Z projektu přejděte do **MyApp**  >  **Src**  >  **Main**  >  **Scala**  >  **MyApp**.  

6. V řádku nabídek přejděte do konzoly **nástroje**  >  **Spark**  >  **Spustit Livy interaktivní relace (Scala) Sparku**.

7. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName` a stiskněte kombinaci kláves CTRL + ENTER.  Zobrazí se výsledek. Místní konzolu můžete ukončit kliknutím na tlačítko červené.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/interactive-console-result.png" alt-text="Výsledek IntelliJ NÁPADu pro interaktivní konzolu" border="true":::

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark

Je vhodné, abyste mohli výsledek skriptu předvídat odesláním kódu do místní konzoly nebo konzoly Livy Interactive Session (Scala). Můžete zvýraznit nějaký kód v souboru Scala a pak klikněte pravým tlačítkem myši na **Odeslat výběr do konzoly Spark**. Vybraný kód bude odeslán do konzoly. Výsledek se zobrazí za kódem v konzole nástroje. Konzola zkontroluje chyby, pokud již existují.  

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png" alt-text="Odeslat výběr do konzoly Spark" border="true":::

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrace se službou HDInsight identity broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Připojení ke clusteru HDInsight ESP pomocí zprostředkovatele ID (HIB)

Pomocí běžných kroků se přihlaste k předplatnému Azure, abyste se připojili ke clusteru HDInsight ESP pomocí zprostředkovatele ID (HIB). Po přihlášení se v Azure Exploreru zobrazí seznam clusterů. Další pokyny najdete v tématu [připojení ke clusteru HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spuštění aplikace Spark Scala v clusteru HDInsight ESP s zprostředkovatelem ID (HIB)

Pomocí běžných kroků můžete odeslat úlohu do clusteru HDInsight ESP se zprostředkovatelem ID (HIB). Další pokyny najdete [v tématu spuštění aplikace Spark Scala v clusteru HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) .

Potřebné soubory nahrajeme do složky s názvem přihlašovacího účtu a cestu pro nahrávání můžete zobrazit v konfiguračním souboru.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png" alt-text="nahrát cestu v konfiguraci" border="true":::

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Konzola Spark v clusteru HDInsight ESP s zprostředkovatelem ID (HIB)

Můžete spustit místní konzolu Spark (Scala) nebo spustit konzolu Spark Livy Interactive Session (Scala) v clusteru HDInsight ESP s zprostředkovatelem ID (HIB). Další pokyny najdete v [konzole Spark](#spark-console) .

   > [!NOTE]  
   > Pro cluster HDInsight ESP s zprostředkovatelem ID (HIB) [propojte cluster](#link-a-cluster) a [aplikace pro ladění Apache Spark vzdáleně](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) není aktuálně podporována.

## <a name="reader-only-role"></a>Role jen pro čtení

Když uživatelé odešlou úlohu do clusteru s oprávněním role jen pro čtení, vyžadují se přihlašovací údaje Ambari.

### <a name="link-cluster-from-context-menu"></a>Propojit cluster s místní nabídkou

1. Přihlaste se pomocí účtu role jen pro čtení.

2. V **Azure Exploreru** rozbalte **HDInsight** pro zobrazení clusterů HDInsight, které jsou ve vašem předplatném. Clustery označené jako **role: čtenář** mají pouze oprávnění role jen pro čtení.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png" alt-text="' IntelliJ Azure Explorer role: čtenář '" border="true":::

3. Klikněte pravým tlačítkem na cluster s oprávněním role jen pro čtení. Pokud chcete propojit cluster, vyberte **propojit tento cluster** z kontextové nabídky. Zadejte uživatelské jméno a heslo pro Ambari.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png" alt-text="IntelliJ tento cluster propojit s Průzkumníkem Azure" border="true":::

4. Pokud je cluster úspěšně propojený, služba HDInsight se aktualizuje.
   Fáze clusteru se stane propojená.
  
    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png" alt-text="Propojený dialog v Průzkumníkovi Azure IntelliJ" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Propojení clusteru rozbalením uzlu úlohy

1. Klikněte na uzel **úlohy** , okno **přístup k úloze clusteru odepřeno** .

2. Kliknutím na **propojit tento cluster** propojíte cluster.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png" alt-text="dialog s odepřeným přístupem k úloze clusteru" border="true":::

### <a name="link-cluster-from-rundebug-configurations-window"></a>Propojit cluster s oknem konfigurace spuštění/ladění

1. Vytvořte konfiguraci HDInsight. Pak vyberte **vzdálené spuštění v clusteru**.

2. Vyberte cluster, který má oprávnění jen pro čtení pro **Clustery Spark (pouze Linux)**. Zpráva upozornění se zobrazí. Kliknutím na **propojit tento cluster** můžete propojit cluster.

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/create-configuration.png" alt-text="Spuštění nebo ladění IntelliJ NÁPADu – vytvořit konfiguraci" border="true":::

### <a name="view-storage-accounts"></a>Zobrazit účty úložiště

* U clusterů s oprávněním role jen pro čtení klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová zařízení. Pro otevření Průzkumník služby Storage můžete kliknout na **otevřít Průzkumník služby Azure Storage** .

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png" alt-text="Přístup k úložišti NÁPADu IntelliJ byl odepřen." border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png" alt-text="Tlačítko IntelliJ pro přístup k úložišti nápadů zakázáno" border="true":::

* U propojených clusterů klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová aplikace. Pro otevření Průzkumník služby Storage můžete kliknout na **otevřít Azure Storage** .

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png" alt-text="' IntelliJ nápad pro přístup k úložišti Denied2 '" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png" alt-text="IntelliJ nápad – přístup k Denied2mu úložišti" border="true":::

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převod stávajících IntelliJ NÁPADových aplikací na použití Azure Toolkit for IntelliJ

Stávající aplikace Spark Scala, které jste vytvořili v IntelliJ, můžete převést tak, aby byly kompatibilní s Azure Toolkit for IntelliJ. Pak můžete použít modul plug-in k odeslání aplikací do clusteru HDInsight Spark.

1. V případě existující aplikace Spark Scala vytvořené prostřednictvím IntelliJ NÁPADu otevřete přidružený `.iml` soubor.

2. Na kořenové úrovni je prvek **modulu** , jako je následující text:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Upravte prvek tak, aby byl přidán `UniqueKey="HDInsightTool"` , aby element **Module** vypadal jako následující text:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Uložte změny. Vaše aplikace by měla být teď kompatibilní s Azure Toolkit for IntelliJ. Můžete ho otestovat kliknutím pravým tlačítkem myši na název projektu v projektu. Místní nabídka má nyní možnost **Odeslat aplikaci Spark do HDInsight**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster, který jste vytvořili, pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.

1. V části **služby** vyberte **clustery HDInsight** .

1. V seznamu clusterů HDInsight, které se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro tento článek.

1. Vyberte **Odstranit**. Vyberte **Ano**.

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal odstraní cluster HDInsight." border="true":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat modul plug-in Azure Toolkit for IntelliJ k vývoji Apache Spark aplikací napsaných v [Scala](https://www.scala-lang.org/). Pak je odešlete do clusteru HDInsight Spark přímo z integrovaného vývojového prostředí (IDE) IntelliJ. V dalším článku zjistíte, jak se data, která jste zaregistrovali v Apache Spark můžete načíst do nástroje BI Analytics, jako je například Power BI.

> [!div class="nextstepaction"]
> [Analýza dat Apache Spark pomocí Power BI](apache-spark-use-bi-tools.md)
