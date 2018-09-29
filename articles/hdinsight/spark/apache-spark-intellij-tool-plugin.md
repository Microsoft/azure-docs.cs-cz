---
title: 'Sada Azure Toolkit pro IntelliJ: vytvoření aplikací Spark pro HDInsight cluster '
description: Pomocí sady Azure Toolkit pro IntelliJ můžete vyvíjet aplikace Spark napsané v jazyce Scala a odesílat je na cluster HDInsight Spark.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/25/2017
ms.author: maxluk
ms.openlocfilehash: 07c2b506007daccd53a8b06a43064e6e274ac43b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433354"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Vytvoření aplikací Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro IntelliJ

Použití sady Azure Toolkit pro IntelliJ modulu plug-in k vývoji aplikací Spark napsané v jazyce Scala a odesílat je na cluster HDInsight Spark přímo z integrovaného vývojového prostředí (IDE) IntelliJ. Modul plug-in můžete použít několika způsoby:

* Vývoj a odeslání aplikace Scala Spark na cluster HDInsight Spark.
* Přístup k vašim prostředkům clusteru Azure HDInsight Spark.
* Vyvíjejte a spouštějte aplikace Scala Spark místně.

K vytvoření projektu, zobrazení [vytvoření aplikací Spark pomocí sady Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) videa.

> [!IMPORTANT]
> Tento modul plug-in můžete použít k vytvoření a odeslání aplikace pouze pro cluster HDInsight Spark na systému Linux.
> 

## <a name="prerequisites"></a>Požadavky

- Cluster Apache Spark v HDInsight Linux. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Můžete ho nainstalovat [webu Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
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
Normální clusteru HDInsight můžete propojit pomocí Ambari, spravovat uživatelské jméno. Podobně pro cluster HDInsight připojený k doméně můžete propojit pomocí doména a uživatelské jméno, například user1@contoso.com.

1. Vyberte **propojení clusteru** z **Průzkumníka služby Azure**.

   ![propojení clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)


1. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**. Je potřeba zkontrolovat uživatelské jméno a heslo, pokud máte ověřování se nezdařilo. Volitelně můžete přidat účet úložiště, klíč úložiště, pak vyberte kontejner z kontejneru úložiště. Informace o úložiště je pro Průzkumníka služby storage v levém stromu
   
   ![Dialogové okno clusteru odkaz](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník služby Storage v IntelliJ](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
1. Můžete zobrazit propojené clusteru v **HDInsight** uzlu, pokud jsou vstupní informace správné. Nyní můžete odeslat aplikace do této propojené clusteru.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.
   
   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala na cluster HDInsight Spark

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky: 

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

1. Spusťte aplikaci v clusteru HDInsight Spark následujícím způsobem:

   a. V prohlížeči projektu klikněte pravým tlačítkem myši na název projektu a pak vyberte **odeslání aplikace Spark na HDInsight**.
      
      ![Odeslání aplikace Spark na HDInsight příkaz](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Zobrazí se výzva k zadání přihlašovacích údajů předplatného Azure. V **Spark odeslání** dialogové okno, zadejte následující hodnoty a pak vyberte **odeslat**.
      
      * Pro **(pouze Linux) clustery Spark**, vyberte na kterém chcete spustit aplikaci v clusteru HDInsight Spark.

      * Vyberte artefakt z projektu IntelliJ nebo vyberte některou z pevného disku.

      * V **hlavní název třídy** , vyberte symbol tří teček (**...** ), vyberte hlavní třídy ve zdrojovém kódu aplikace a pak vyberte **OK**.

        ![Vyberte hlavní třídy dialogových oken](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Můžete zadat požadované informace. O **úlohy konfigurace**, budeme mít výchozí hodnotu. A můžete se podívat do [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) Další informace o klíčích. **Argumenty příkazového řádku**, **odkazuje JARs** a **odkazované soubory** by měl vypadat jako obrázku níže. Můžete se podívat do [konfigurace Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) Další informace o **odkazuje JARs** a **odkazované soubory**. Chcete-li **odkazuje JARs** a **odkazované soubory** fungovat dobře, byste měli nahrávat prostředky do clusteru nejdřív odešlete. Odkazovat na [jak nahrát prostředky do clusteru](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer). Dialogové okno Odeslat by měla vypadat podobně jako na následujícím obrázku.
        
        ![Význam Spark odeslání dialogové okno pole úlohy konfigurace](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

        ![To znamená soubory jar pole Spark odeslání dialogového okna](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

        ![Dialogové okno odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. **Spark odeslání** karta v dolní části okna by se měl spustit zobrazení průběhu. Můžete také zastavit aplikaci tak, že vyberete červené tlačítko v **Spark odeslání** okna.
      
     ![V okně odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Zjistěte, jak získat přístup k výstupu úlohy, najdete v článku "přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ" dále v tomto článku.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Místní nebo vzdálené ladění aplikací Spark na clusteru služby HDInsight 
Doporučujeme také jiný způsob odeslání aplikace Spark na clusteru. Můžete to provést nastavením parametrů **konfigurace spuštění/ladění** integrovaného vývojového prostředí. Další informace najdete v tématu [ladění aplikací Spark místně nebo vzdáleně v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ
Pomocí sady Azure Toolkit pro IntelliJ můžete provádět různé operace.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh
1. V Průzkumníku služby Azure, rozbalte **HDInsight**, rozbalte název clusteru Spark a pak vyberte **úlohy**.  

    ![Úlohy zobrazení uzlu](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    ![Podrobnosti aplikace](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Poznámka
    >

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

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převést stávající aplikace IntelliJ IDEA pro použití sady Azure Toolkit pro IntelliJ
Můžete převést stávající Scala Spark aplikací, které jste vytvořili v IntelliJ IDEA, aby byl kompatibilní s Azure Toolkit pro IntelliJ. Potom můžete modul plug-in k odeslání aplikace do clusteru HDInsight Spark.

1. Pro existující aplikace Spark Scala, který byl vytvořen pomocí IntelliJ IDEA otevřete soubor přidružené .iml.

1. V kořenovém adresáři úroveň je **modulu** element následujícím postupem:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Proveďte editaci prvku přidat `UniqueKey="HDInsightTool"` tak, aby **modulu** element vypadá takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Uložte změny. Vaše aplikace by měla nyní být kompatibilní s Azure Toolkit pro IntelliJ. Kliknutím pravým tlačítkem myši na název projektu v Průzkumníku projektů ji můžete otestovat. V rozbalovací nabídce má teď možnost **odeslání aplikace Spark na HDInsight**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Chyba při místním spuštění: *použijte prosím větší velikost haldy*
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
Při propojení clusteru, můžu Navrhněte poskytnutí přihlašovacích údajů úložiště.

![Propojení clusteru, zadejte přihlašovací údaje úložiště](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Existují dva režimy pro odeslání úlohy. Pokud přihlašovací údaje úložiště v dávkovém režimu se použije k odeslání úlohy. V opačném případě se použije interaktivním režimu. Pokud cluster je zaneprázdněný, může se zobrazit následující chyba.

![Intellij zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy
V současné době se nepodporuje zobrazení Spark výstupy přímo.

Pokud máte nějaké návrhy nebo zpětnou vazbu, nebo pokud se vyskytnou potíže při použití tohoto modulu plug-in, pošlete nám e-mail na hdivstool@microsoft.com.

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvořit projekt Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes síť VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
