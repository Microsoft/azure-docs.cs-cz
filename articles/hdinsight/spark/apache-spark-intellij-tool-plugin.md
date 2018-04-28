---
title: 'Azure nástrojů pro IntelliJ: vytvoření aplikací Spark pro cluster služby HDInsight | Microsoft Docs'
description: Použití sady nástrojů Azure pro IntelliJ k vývoji aplikací Spark napsané v jazyce Scala a odesílat je na clusteru HDInsight Spark.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/25/2017
ms.author: maxluk,jejiang
ms.openlocfilehash: 726b062aecc11db738926586cdede045eae3a61e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Vytvoření aplikací Spark pro cluster služby HDInsight pomocí nástrojů Azure pro IntelliJ

Použití nástrojů Azure pro IntelliJ modulu plug-in k vývoji aplikací Spark napsané v jazyce Scala a odesílat je na clusteru HDInsight Spark přímo z IntelliJ integrované vývojové prostředí (IDE). Modul plug-in můžete použít několika způsoby:

* Vývoj a odesílání aplikací Scala Spark na clusteru HDInsight Spark.
* Přístup k prostředkům clusteru Azure HDInsight Spark.
* Vytvořte a spusťte aplikací Scala Spark místně.

Chcete-li vytvořit projekt, podívejte se [vytvoření aplikací Spark pomocí sady nástrojů Azure pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) videa.

> [!IMPORTANT]
> Tento modul plug-in můžete použít k vytvoření a odeslání aplikací pouze pro cluster služby HDInsight Spark na systému Linux.
> 

## <a name="prerequisites"></a>Požadavky

- Cluster Apache Spark v HDInsight Linux. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Můžete nainstalovat z [Oracle webu](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Tento článek používá verzi 2017.1. Můžete nainstalovat z [JetBrains webu](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalace Azure Toolkit pro IntelliJ
Pokyny k instalaci naleznete v tématu [instalaci nástrojů Azure pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Začínáme
Uživatel může buď [Přihlaste se k předplatnému Azure](#sign-in-to-your-azure-subscription), nebo [odkaz HDInsight cluster](#link-a-cluster) pomocí nástroje Ambari uživatelského jména a hesla nebo domény připojeno přihlašovací údaje pro spuštění.


## <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure.

1. Spuštění rozhraní IDE IntelliJ a otevřete Průzkumník Azure. Na **zobrazení** nabídce vyberte možnost **nástroj Windows**a potom vyberte **Azure Explorer**.
       
   ![Průzkumník Azure odkaz](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Klikněte pravým tlačítkem myši **Azure** uzel a potom vyberte **přihlásit**.

3. V **přihlásit k Azure** dialogové okno, vyberte **přihlášení**a pak zadejte přihlašovací údaje Azure.

    ![Dialogové okno přihlášení k Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Poté, co jste přihlášení, **vyberte odběry** dialogové okno zobrazí všechna předplatná Azure, které jsou spojeny s přihlašovacími údaji. Vyberte **vyberte** tlačítko.

    ![Dialogové okno Vybrat odběrů](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Na **Průzkumník Azure** rozbalte **HDInsight** zobrazíte clustery HDInsight Spark, které jsou v rámci vašeho předplatného.
   
    ![Clustery HDInsight Spark v Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Chcete-li zobrazit prostředky (například účty úložiště), které jsou přidružené ke clusteru, můžete dále rozšířit uzlem název clusteru.
   
    ![Rozbalené uzly název clusteru](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">Odkaz clusteru</h2>
Můžete propojit normální clusteru pomocí Ambari spravované uživatelské jméno, také propojení clusteru hadoop zabezpečení pomocí uživatelského jména domény (například: user1@contoso.com). 
1. Klikněte na tlačítko **odkaz cluster** z **Azure Explorer**.

   ![odkaz clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**. Je třeba zkontrolovat uživatelské jméno a heslo, pokud se tu selhání ověření. Volitelně můžete přidat účet úložiště, klíč k úložišti, pak vyberte kontejner z kontejneru úložiště. Informace o úložiště je pro Průzkumníka úložiště v levém stromu
   
   ![Dialogové okno odkaz clusteru](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené cluster používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník úložišť v IntelliJ](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
3. Můžete zobrazit v clusteru s podporou propojené **HDInsight** uzlu, pokud vstupní informace jsou správná. Teď můžete odeslat žádost této propojené clusteru.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Také můžete zrušit propojení clusteru z **Azure Explorer**.
   
   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace na Spark Scala clusteru HDInsight Spark

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V **nový projekt** dialogové okno pole, postupujte takto: 

   a. Vyberte **HDInsight** > **Spark v HDInsight (Scala)**.

   b. V **nástroj pro sestavení** vyberte některý z následujících akcí podle potřeby vašeho:

      * **Maven**, podpora Průvodce vytvoření projektu Scala
      * **SBT**, ke správě závislosti a vytváření projektu Scala

    ![Dialogové okno Nový projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Vyberte **Next** (Další).

3. Průvodce vytvoření projektu Scala automaticky zjišťuje, zda jste nainstalovali Scala modulu plug-in. Vyberte **nainstalovat**.

   ![Kontrola modul plug-in Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Chcete-li stáhnout modul plug-in Scala, vyberte **OK**. Postupujte podle pokynů k restartování IntelliJ. 

   ![Dialogové okno instalace modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. V **nový projekt** okno, postupujte takto:  

    ![Výběr Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Zadejte název projektu a umístění.

   b. V **SDK projektu** rozevíracího seznamu vyberte **Java 1.8** pro Spark 2.x cluster nebo vyberte **Java 1.7** 1.x clusteru Spark.

   c. V **Spark verze** rozevíracího seznamu, Průvodce vytvořením projektu Scala integruje správnou verzi sady SDK Spark a Scala SDK. Pokud verze clusteru Spark je starší než 2.0, vyberte **Spark 1.x**. Jinak vyberte možnost **Spark2.x**. Tento příklad používá **Spark bodu 2.0.2 (Scala 2.11.8)**.

6. Vyberte **Finish** (Dokončit).

7. Projekt Spark pro vás automaticky vytvoří artefakt. Chcete-li zobrazit artefaktu, postupujte takto:

   a. Na **soubor** nabídce vyberte možnost **strukturu projektu**.

   b. V **strukturu projektu** dialogové okno, vyberte **artefakty** zobrazíte výchozí artefaktu, který je vytvořen. Můžete také vytvořit vlastní artefaktů výběrem na symbol plus (**+**).

      ![Informace o artefaktů v dialogovém okně](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Přidejte zdrojový kód aplikace následujícím způsobem:

   a. V prohlížeči projektu klikněte pravým tlačítkem na **src**, přejděte na příkaz **nový**a potom vyberte **Scala třída**.
      
      ![Příkazy pro vytvoření třídy Scala z Project Exploreru](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. V **vytvořte novou třídu Scala** dialogové okno, zadejte název, vyberte **objekt** v **druhu** a pak vyberte **OK**.
      
      ![Nová třída Scala dialogové okno vytvořit](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. V **MyClusterApp.scala** souboru, vložte následující kód. Kód čte data z HVAC.csv (k dispozici na všech clusterech HDInsight Spark), načte řádky, které mají jenom jednu číslici ve sloupci sedmého v souboru CSV a zapíše výstup do **/HVACOut** v kontejneru výchozí úložiště pro cluster.

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

9. Spuštění aplikace na clusteru HDInsight Spark následujícím způsobem:

   a. V prohlížeči projektu klikněte pravým tlačítkem na název projektu a pak vyberte **odesílání aplikací Spark na HDInsight**.
      
      ![Odeslání aplikací Spark na HDInsight příkaz](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Zobrazí se výzva k zadání přihlašovacích údajů předplatného Azure. V **Spark odeslání** dialogové okno, zadejte následující hodnoty a potom vyberte **odeslání**.
      
      * Pro **Spark clustery (pouze Linux)**, vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.

      * Vyberte artefakt z projektu IntelliJ nebo vyberte jednu z pevného disku.

      * V **hlavní název třídy** vyberte se třemi tečkami (**...** ), vyberte hlavní třídy ve zdrojovém kódu aplikace a pak vyberte **OK**.

        ![Dialogové okno Vybrat hlavní – třída](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Protože kódu aplikace v tomto příkladu nevyžaduje argumenty příkazového řádku ani odkazovat JAR nebo soubory, můžete zbývající pole ponechat prázdné. Po zadání všech informací, dialogové okno by měla vypadat přibližně na následujícím obrázku.
        
        ![Dialogové okno Spark odeslání](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. **Spark odeslání** karta v dolní části okna by se měl spustit zobrazení průběhu. Můžete také zastavit aplikaci tak, že vyberete červené tlačítko v **Spark odeslání** okno.
      
     ![Okno Spark odeslání](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Další informace o přístupu k výstup úlohy najdete v tématu "přístup a spravovat clustery HDInsight Spark pomocí nástrojů Azure pro IntelliJ" později v tomto článku.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Ladění aplikací Spark v clusteru HDInsight místně nebo vzdáleně 
Doporučujeme také jiný způsob odesílání aplikací Spark pro cluster. Můžete tak učinit pomocí nastavení v parametrech **konfigurace spustit/Debug** IDE. Další informace najdete v tématu [ladění aplikací Spark místně nebo vzdáleně v clusteru s Azure nástrojů HDInsight pro IntelliJ prostřednictvím SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup a spravovat clustery HDInsight Spark pomocí nástrojů Azure pro IntelliJ
Pomocí nástrojů Azure pro IntelliJ můžete provádět různé operace.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh
1. V Průzkumníku Azure rozbalte **HDInsight**, rozbalte název clusteru Spark a pak vyberte **úlohy**.  

    ![Úlohy zobrazení uzlu](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro který chcete zobrazit další podrobnosti.

    ![Podrobnosti aplikace](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Poznámka
    >

3. Chcete-li zobrazit základní informace o spuštěné úlohy, pozastavte ukazatel myši nad graf úlohy. Chcete-li zobrazit graf fázích a informace, které generuje každých úlohy, vyberte uzel na graf úlohy.

    ![Fáze podrobnosti úlohy](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Zobrazení často používat protokoly, jako např *ovladač Stderr*, *ovladač Stdout*, a *informací o adresáři*, vyberte **protokolu** karta.

    ![Podrobnosti protokolu](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Výběrem odkaz v horní části okna můžete také zobrazit historii Spark uživatelského rozhraní a uživatelské rozhraní YARN (na úrovni aplikace).

### <a name="access-the-spark-history-server"></a>Přístup k serveru Spark historie
1. V Průzkumníku Azure rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřete uživatelské rozhraní historie Spark**. 

2. Když se zobrazí výzva, zadejte přihlašovací údaje Správce clusteru, které jste zadali při nastavování clusteru.

3. Na řídicím panelu serveru historie Spark můžete použít název aplikace a Hledat aplikace právě dokončila spuštění. V předchozím kódu nastavit název aplikace pomocí `val conf = new SparkConf().setAppName("MyClusterApp")`. Proto je název aplikace Spark **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Spuštění portálu Ambari
1. V Průzkumníku Azure rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřete portál pro správu clusteru (Ambari)**. 

2. Když se zobrazí výzva, zadejte přihlašovací údaje Správce clusteru. Tyto přihlašovací údaje jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Spravovat předplatná Azure
Ve výchozím nastavení seznam nástrojů Azure pro IntelliJ clustery Spark ze všech předplatných Azure. V případě potřeby můžete zadat odběry, které chcete získat přístup. 

1. V Průzkumníku Azure, klikněte pravým tlačítkem myši **Azure** kořenový uzel a potom vyberte **Spravovat odběry**. 

2. V dialogovém okně, zrušte zaškrtnutí políček vedle odběry, které nechcete použít pro přístup a potom vyberte **Zavřít**. Můžete také vybrat **Odhlásit** Pokud se chcete odhlásit z vašeho předplatného Azure.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převést stávající aplikace IntelliJ IDEA používat sadu nástrojů Azure pro IntelliJ
Můžete převést stávající Spark Scala aplikace, který jste vytvořili v IntelliJ IDEA, aby byl kompatibilní s Azure nástrojů pro IntelliJ. Potom můžete modul plug-in odeslání aplikací do clusteru HDInsight Spark.

1. Pro existující Spark Scala aplikaci, která byla vytvořena prostřednictvím IntelliJ IDEA otevřete soubor přidružené .iml.

2. V kořenovém adresáři úroveň je **modulu** element takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Upravit elementu, který chcete přidat `UniqueKey="HDInsightTool"` tak, aby **modulu** element vypadá takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Uložte změny. Aplikace by teď měly být kompatibilní s Azure nástrojů pro IntelliJ. Můžete otestovat ji kliknutím pravým tlačítkem na název projektu v prohlížeči projektu. V rozbalovacím má teď možnost **odesílání aplikací Spark na HDInsight**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Chyba při místním spuštění: *použijte prosím větší velikost haldy*
V 1.6 Spark Pokud používáte Java SDK 32-bit při místním spuštění, může dojít k následujícím chybám:

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

Tyto chyby dojít, protože velikost haldy není dostatečně velký pro Spark ke spuštění. Spark vyžaduje alespoň 471 MB. (Další informace najdete v tématu [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) Jeden jednoduchým řešením je použití sady Java SDK 64-bit. Můžete také změnit nastavení JVM v IntelliJ přidáním následujících možností:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Přidání možnosti do pole "Možnosti virtuálního počítače" v IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Nejčastější dotazy
Chcete-li odeslat aplikace do Azure Data Lake Store, zvolte **interaktivní** režimu během procesu Azure přihlášení. Pokud vyberete **automatizovaná** režimu, můžete dojde k chybě.

![interaktivní přihlášení](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

Nyní jsme ho vyřešil. Můžete použít Cluster služby Azure Data Lake odeslat vaší aplikace s libovolnou metodu přihlášení.

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy
V současné době Spark výstupů zobrazení přímo není podporováno.

Pokud máte jakékoli návrhy či názory, nebo pokud se vyskytnou potíže při použití tento modul plug-in, e-mailu nás na adrese hdivstool@microsoft.com.

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvoření projektu Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [pomocí nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Spark v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Používat nástroje HDInsight pro vytvoření aplikací Spark v Azure nástrojů pro Eclipse](apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

