---
title: 'Sada Azure Toolkit pro IntelliJ: Vytvoření aplikací Spark pro HDInsight cluster '
description: Pomocí sady Azure Toolkit pro IntelliJ můžete vyvíjet aplikace Spark napsané v jazyce Scala a odesílat je na cluster HDInsight Spark.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: 621d41c5c9558b5cb17d2a1e5a03d68f8af0df19
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53600637"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Vytváření aplikací Apache Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro IntelliJ

Použití sady Azure Toolkit pro IntelliJ modulu plug-in k vývoji [Apache Spark](https://spark.apache.org/) aplikace napsané v [Scala](https://www.scala-lang.org/)a odešlete je do clusteru HDInsight Spark přímo z vývojového IntelliJ integrované prostředí (IDE). Modul plug-in můžete použít několika způsoby:

* Vývoj a odeslání aplikace Scala Spark na cluster HDInsight Spark.
* Přístup k vašim prostředkům clusteru Azure HDInsight Spark.
* Vyvíjejte a spouštějte aplikace Scala Spark místně.

K vytvoření projektu, zobrazení [vytváření aplikací Apache Spark pomocí sady Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) videa.

> [!IMPORTANT]  
> Tento modul plug-in můžete použít k vytvoření a odeslání aplikace pouze pro cluster HDInsight Spark na systému Linux.
> 

## <a name="prerequisites"></a>Požadavky

- Cluster Apache Spark v HDInsight Linux. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Můžete ho nainstalovat [webu Oracle](https://aka.ms/azure-jdks).
- IntelliJ IDEA. Tento článek používá verzi 2017.1. Můžete ho nainstalovat [JetBrains webu](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Nainstalujte sadu Azure Toolkit pro IntelliJ
Pokyny k instalaci, naleznete v tématu [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Začínáme
Uživatel může buď [Přihlaste se k předplatnému Azure](#sign-in-to-your-azure-subscription), nebo [propojit HDInsight cluster](#link-a-cluster) pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené přihlašovací údaje ke spuštění.


## <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure.

1. Spuštění rozhraní IDE IntelliJ a otevřete Průzkumníka služby Azure. Na **zobrazení** nabídce vyberte možnost **nástroj Windows**a pak vyberte **Průzkumníka služby Azure**.
       
   ![Propojení Průzkumníka služby Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. Klikněte pravým tlačítkem myši **Azure** uzlu a pak vyberte **Sign In**.

1. V **Azure Sign In** dialogu **přihlášení**a pak zadejte přihlašovací údaje Azure.

    ![Dialogové okno Azure Sign In](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. Jakmile jste přihlášení, **vyberte předplatná** dialogové okno obsahuje všechna předplatná Azure, které jsou spojeny s přihlašovacími údaji. Vyberte **vyberte** tlačítko.

    ![Dialogové okno Vybrat předplatná](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. Na **Průzkumníka služby Azure** kartu, rozbalte **HDInsight** zobrazíte clustery HDInsight Spark, které jsou v rámci vašeho předplatného.
   
    ![Clustery HDInsight Spark v Průzkumníku služby Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. Chcete-li zobrazit prostředky (například účty úložiště), které jsou spojeny s clusterem, můžete dále rozšířit názvem clusteru uzel.
   
    ![Rozbalený uzel název clusteru](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Propojení clusteru
Normální clusteru HDInsight můžete propojit s použitím Apache Ambari, spravovat uživatelské jméno. Podobně pro cluster HDInsight připojený k doméně můžete propojit pomocí doména a uživatelské jméno, například user1@contoso.com. Můžete také propojit Livy služby clusteru.

1. Vyberte **propojení clusteru** z **Průzkumníka služby Azure**.

   ![propojení clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Máte dvě možnosti, jak propojit clustery. 

   * Pro propojení HDInsight clusteru, zvolte **clusteru HDInsight** v poli **informace o clusteru**, zadejte **název nebo adresu URL clusteru**, **uživatelské jméno**, a **Heslo**.

      ![Dialogové okno clusteru hdinsight odkaz](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * Propojení služby Livy clusteru, zvolte **Livy služby** v poli **informace o clusteru**, zadejte **koncový bod Livy**, **název clusteru**. **Yarn koncový bod** je volitelný. V poli **ověřování**, jsou k dispozici dvě možnosti. Jsou **základní ověřování** a **bez ověřování**. Když vyberete **základní ověřování**, **uživatelské jméno** a **heslo** by měla být k dispozici. Musí zkontrolovat uživatelské jméno a heslo, pokud máte ověřování se nezdařilo.
      
      ![propojit dialogu clusteru livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. Můžete zobrazit propojené clusteru v **HDInsight** uzlu, pokud jsou vstupní informace správné. Nyní můžete odeslat aplikace do této propojené clusteru.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.
   
   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Spark Scala na cluster HDInsight Spark

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V **nový projekt** dialogové okno, postupujte podle následujících kroků: 

   a. Vyberte **HDInsight** > **Spark on HDInsight (Scala)** (Spark v HDInsight (Scala)).

   b. V seznamu **Build tool** (Nástroj sestavení) vyberte podle svých potřeb některý z následujících:

      * **Maven** – pro podporu průvodce vytvořením projektu Scala
      * **SBT** – pro správu závislostí a vytváření pro projekt Scala

    ![Dialogové okno New Project (Nový projekt)](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. Vyberte **Další**.

1. Průvodce vytvořením projektu Scala automaticky zjistí, jestli jste nainstalovali modul plug-in Scala. Vyberte **Install** (Nainstalovat).

   ![Kontrola modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. Pokud si chcete stáhnout modul plug-in Scala, vyberte **OK**. Postupujte podle pokynů pro restartování IntelliJ. 

   ![Dialogové okno instalace modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. V okně **New Project** (Nový projekt) proveďte následující kroky:  

    ![Výběr sady Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Zadejte název a umístění projektu.

   b. V rozevíracím seznamu **Project SDK** (SDK projektu) vyberte **Java 1.8** pro cluster Spark 2.x, nebo vyberte **Java 1.7** pro cluster Spark 1.x.

   c. Pomocí rozevíracího seznamu **Spark version** (Verze clusteru Spark) průvodce vytvořením projektu Scala integruje správnou verzi pro sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.

1. Vyberte **Finish** (Dokončit).

1. Artefakt Spark project automaticky vytvoří za vás. Chcete-li zobrazit artefakt, postupujte takto:

   a. Na **souboru** nabídce vyberte možnost **strukturu projektu**.

   b. V **strukturu projektu** dialogu **artefakty** Chcete-li zobrazit výchozí artefakt, který je vytvořen. Můžete také vytvořit vlastní artefakt vyberte znaménko plus (**+**).

      ![Informace o artefaktu v dialogovém okně](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. Přidejte zdrojový kód aplikace následujícím způsobem:

   a. V prohlížeči projektu klikněte pravým tlačítkem myši na **src**, přejděte na **nový**a pak vyberte **Scala třídy**.
      
      ![Příkazů pro vytvoření třídy Scala v Project Exploreru](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. V **vytvořit novou třídu Scala** dialogové okno, zadejte název, vyberte **objekt** v **druh** a potom vyberte **OK**.
      
      ![Vytvoření dialogového okna Nová třída Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. V **MyClusterApp.scala** soubor, vložte následující kód. Kód čte data z HVAC.csv (k dispozici na všech clusterech HDInsight Spark), načte řádky, které mají pouze jednu číslici v sedmé sloupec v souboru CSV a zapíše výstup do **/HVACOut** pod výchozí kontejner úložiště pro cluster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala na cluster HDInsight Spark
Po vytvoření aplikace v jazyce Scala, můžete ho odeslat do clusteru.

1. V prohlížeči projektu nalezení souboru Java nebo Scala a pak vyberte **odeslání aplikace Spark na HDInsight** v místní nabídce.
    
      ![Odeslání aplikace Spark na HDInsight příkaz](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. V dialogovém okně konfigurace, zadejte následující hodnoty a pak klikněte na **SparkJobRun**.

      ![Dialogové okno odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * Pro **(pouze Linux) clustery Spark**, vyberte na kterém chcete spustit aplikaci v clusteru HDInsight Spark.

    * Vyberte artefakt z projektu IntelliJ nebo vyberte některou z pevného disku.

    * **Název hlavní třídy** pole: Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete symbol tří teček (**...** ) a výběrem jiné třídy.   

    * **Konfigurace úlohy** pole:  Ve výše uvedeném obrázku jsou nastaveny výchozí hodnoty. Můžete změnit hodnotu nebo přidejte nový klíč/hodnota pro odeslání úlohy. Další informace najdete tady: [Apache Livy rozhraní REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![Význam Spark odeslání dialogové okno pole úlohy konfigurace](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **Argumenty příkazového řádku** pole: Můžete zadat hodnoty argumentů místo pro hlavní třída v případě potřeby rozdělit.

    * **Kromě souborů JAR odkazovat** a **odkazované soubory** pole: Cesty pro odkazované kromě souborů JAR a soubory můžete zadat případné. Další informace najdete tady: [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) 

      ![To znamená soubory jar pole Spark odeslání dialogového okna](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]  
       > Nahrát odkazuje JARs a odkazované soubory, najdete v tématu: [Jak nahrát prostředky do clusteru](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **Nahrát cesta**: Můžete určit umístění úložiště pro soubor Jar nebo Scala odeslání prostředků projektu. Existují tři podporované typy úložiště: **Azure Blob**, **interaktivní relace použití Sparku k nahrání artefakty**, a **použít výchozí účet úložiště clusteru**, a **ADLS Gen1**. Následující snímek obrazovky je příklad pro objektů Blob v Azure.

        ![Dialogové okno odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![Dialogové okno odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. Klikněte na tlačítko **SparkJobRun** odeslání projektu pro vybraný cluster. **Vzdálené úlohy Spark v clusteru** karta zobrazuje průběh provádění úlohy v dolní části. Zastavte aplikaci klikněte na červené tlačítko. Zjistěte, jak získat přístup k výstupu úlohy, najdete v článku "přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ" dále v tomto článku.
      
     ![V okně odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Ladění aplikací Apache Spark místně nebo vzdáleně v clusteru služby HDInsight 
Doporučujeme také jiný způsob odeslání aplikace Spark na clusteru. Můžete to provést nastavením parametrů **konfigurace spuštění/ladění** integrovaného vývojového prostředí. Další informace najdete v tématu [ladění aplikací Apache Spark místně nebo vzdáleně v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ
Pomocí sady Azure Toolkit pro IntelliJ můžete provádět různé operace.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh
1. V Průzkumníku služby Azure, rozbalte **HDInsight**, rozbalte název clusteru Spark a pak vyberte **úlohy**.  

    ![Úlohy zobrazení uzlu](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    ![Podrobnosti aplikace](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

1. Zobrazit základní informace o úloze spuštěné, najeďte myší graf úlohy. Chcete-li zobrazit graf fází a informace, které generuje všechny úlohy, vyberte uzel v grafu úlohy.

    ![Podrobnosti o úloze fáze](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. Chcete-li zobrazit často používají protokoly, *ovladač Stderr*, *ovladač Stdout*, a *informací o adresáři*, vyberte **protokolu** kartu.

    ![Podrobnosti protokolu](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. Výběrem odkazu v horní části okna můžete také zobrazit historii Spark uživatelského rozhraní a Uživatelském rozhraní YARN (na úrovni aplikace).

### <a name="access-the-spark-history-server"></a>Přístup na server historie Sparku
1. V Průzkumníku služby Azure, rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít uživatelské rozhraní historie Sparku**. 

1. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru, které jste zadali při nastavení clusteru.

1. V řídícím panelu server historie Sparku můžete název aplikace k vyhledání aplikace dokončení právě běží. V předchozím kódu, nastavte název aplikace s použitím `val conf = new SparkConf().setAppName("MyClusterApp")`. Proto je název vaší aplikace Spark **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Spusťte portál Ambari
1. V Průzkumníku služby Azure, rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít portál pro správu clusteru (Ambari)**. 

1. Jakmile budete vyzváni, zadejte přihlašovací údaje Správce clusteru. Tyto přihlašovací údaje jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure
Ve výchozím nastavení sady Azure Toolkit pro IntelliJ seznam clusterů Spark ze všech předplatných Azure. V případě potřeby můžete zadat předplatné, které chcete získat přístup. 

1. V Průzkumníku služby Azure, klikněte pravým tlačítkem myši **Azure** kořenový uzel a potom vyberte **spravovat předplatná**. 

1. V dialogovém okně, zrušte zaškrtnutí políček u předplatných, které nechcete, aby pro přístup a pak vyberte **Zavřít**. Můžete také vybrat **Odhlásit** potřeby odhlaste se z vašeho předplatného Azure.

## <a name="spark-console"></a>Spark konzoly
Můžete spustit místní Console(Scala) Spark nebo spuštění Sparku Livy interaktivní relace Console(Scala).

### <a name="spark-local-consolescala"></a>Místní Console(Scala) Spark
1. Pokud nemáte žádné před nastavení konfigurace. V **konfigurace spuštění/ladění** okna, klikněte na tlačítko **+** -> **Azure HDInsight Spark**, vyberte kartu **místně spusťte**a **vzdáleně spouštět v clusteru**, zvolte hlavní třída a potom klikněte na tlačítko **OK**.

    ![Konfigurace sady místní konzoly](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. Otevřete soubor odpovídající hlavní třídy a klikněte pravým tlačítkem myši **Spark konzoly**, pak klikněte na tlačítko **spustit místní Console(Scala) Spark**. Nebo přejděte do nabídky **nástroje**->**Spark konzoly**->**spustit místní Console(Scala) Spark** ke spuštění konzoly. Pak dvě dialogová okna se zobrazí zadat, jestli chcete automaticky opravit závislosti. Stačí kliknout na tlačítko **Automatická oprava**.

    ![Automatické Fix1 Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Automatické Fix2 Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Spark místní vstupní bod](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. Po úspěšném spuštění místní konzoly. Vypadá jako níže. Můžete udělat něco, co chcete. Zadejte například **sc.appName**, stiskněte klávesy ctrl + Enter, pak se zobrazí výsledek. Místní konzoly můžete ukončit kliknutím na červené tlačítko.

    ![Výsledek místní konzoly](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Console(Scala) interaktivní relace Livy
Je podporován pouze na IntelliJ 2018.2 a 2018.3.

1. Pokud nemáte žádné před nastavení konfigurace. V **konfigurace spuštění/ladění** okna, klikněte na tlačítko **+** -> **Azure HDInsight Spark**, vyberte kartu **vzdáleně spouštět v clusteru** , zvolte název clusteru a hlavní třída a potom klikněte na tlačítko **OK**.

    ![Interaktivní konzolu, přidejte položku konfigurace](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Konfigurace sady interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. Otevřete soubor odpovídající hlavní třídy a klikněte pravým tlačítkem myši **Spark konzoly**, pak klikněte na tlačítko **Console(Scala) relaci spouštět Spark Livy interaktivní**. Nebo přejděte do nabídky **nástroje**, pak klikněte na tlačítko **Spark konzoly**, pak **Console(Scala) relaci spouštět Spark Livy interaktivní** ke spuštění konzoly.

3. Po spuštění konzoly nástroje úspěšně, můžete udělat něco, co chcete. Zadejte například **sc.appName**, stiskněte klávesy ctrl + Enter, pak se zobrazí výsledek.

    ![Výsledek interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark
Je vhodné pro vás forsee výsledek skriptu odesláním některých kódů místní konzoly nebo Livy Console(Scala) interaktivní relace. Můžete zvýraznit některých kódů v souboru Scala a pak klikněte pravým tlačítkem myši **odeslat výběr do konzoly Spark**. Vybrané kódy se odešlou do konzoly a provést. Výsledek se zobrazí po kódy v konzole. Konzole zjistí chyby, jestli existující. 

   ![Odeslat výběr do konzoly Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převést stávající aplikace IntelliJ IDEA pro použití sady Azure Toolkit pro IntelliJ
Můžete převést stávající Scala Spark aplikací, které jste vytvořili v IntelliJ IDEA, aby byl kompatibilní s Azure Toolkit pro IntelliJ. Potom můžete modul plug-in k odeslání aplikace do clusteru HDInsight Spark.

1. Pro existující aplikace Spark Scala, který byl vytvořen pomocí IntelliJ IDEA otevřete soubor přidružené .iml.

1. V kořenovém adresáři úroveň je **modulu** element následujícím postupem:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Proveďte editaci prvku přidat `UniqueKey="HDInsightTool"` tak, aby **modulu** element vypadá takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Uložte změny. Vaše aplikace by měla nyní být kompatibilní s Azure Toolkit pro IntelliJ. Kliknutím pravým tlačítkem myši na název projektu v Průzkumníku projektů ji můžete otestovat. V rozbalovací nabídce má teď možnost **odeslání aplikace Spark na HDInsight**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Chyba při místním spuštění: *Použijte prosím větší velikost haldy*
Ve Spark 1.6 při místním spuštění, při použití sady Java SDK pro 32-bit může dojít k následujícím chybám:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Tyto chyby dojít, protože velikost haldy není dostatečně velký pro Spark ke spuštění. Spark vyžaduje nejméně 471 MB. (Další informace najdete v tématu [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) Jeden jednoduchý řešením je použití sady Java SDK 64bitovým kompilátorem. Můžete také změnit nastavení JVM v IntelliJ přidáním následujících možností:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Přidání možností do pole "Možnosti virtuálního počítače" v IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Nejčastější dotazy
Pokud cluster je zaneprázdněný, může se zobrazit následující chyba.

![Intellij zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy
V současné době se nepodporuje zobrazení Spark výstupy přímo.

Pokud máte nějaké návrhy nebo zpětnou vazbu, nebo pokud se vyskytnou potíže při použití tohoto modulu plug-in, pošlete nám e-mail na hdivstool@microsoft.com.

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvoření projektu Scala (video): [Vytváření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Ladění aplikací Apache Spark vzdáleně v clusteru HDInsight pomocí sady Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Ladění aplikací Apache Spark vzdáleně přes síť VPN pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ladění aplikací Apache Spark vzdáleně přes SSH pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse](apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
