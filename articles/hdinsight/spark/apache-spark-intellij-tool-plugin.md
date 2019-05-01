---
title: 'Azure Toolkit for IntelliJ: Vytvoření aplikací Spark pro HDInsight cluster '
description: Pomocí sady Azure Toolkit pro IntelliJ můžete vyvíjet aplikace Spark napsané v jazyce Scala a odesílat je na cluster HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 682b2266442f3aba6696385862844e1b4b93aa3d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702018"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Vytváření aplikací Apache Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro IntelliJ

Použití sady Azure Toolkit pro IntelliJ modulu plug-in k vývoji [Apache Spark](https://spark.apache.org/) aplikace napsané v [Scala](https://www.scala-lang.org/)a odešlete je do clusteru HDInsight Spark přímo z vývojového IntelliJ integrované prostředí (IDE). Modul plug-in můžete použít několika způsoby:

* Vývoj a odeslání aplikace Scala Spark na cluster HDInsight Spark.
* Přístup k vašim prostředkům clusteru Azure HDInsight Spark.
* Vyvíjejte a spouštějte aplikace Scala Spark místně.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Oracle Java Development kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Tento kurz používá verzi 8.0.202 Java.
* IntelliJ IDEA. Tento článek používá [verze IntelliJ IDEA Community  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Zobrazit [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS.EXE.  Zobrazit [problémy se spouštěním Hadoop na Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA
Proveďte následující postup instalace modulu plug-in Scala:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce, přejděte na **konfigurovat** > **moduly plug-in** otevřít **moduly plug-in** okna.
   
    ![Povolení modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Vyberte **nainstalovat** pro modul plug-in Scala, který je mezi vybranými položkami v novém okně.  

    ![Instalace modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Spark Scala pro cluster HDInsight Spark

1. Spusťte IntelliJ IDEA a vyberte **vytvořit nový projekt** otevřít **nový projekt** okna.

2. Vyberte **Azure Spark/HDInsight** v levém podokně.

3. Vyberte **Project Spark (Scala)** z hlavního okna.

4. Z **nástroj pro sestavení** rozevíracího seznamu vyberte jednu z následujících akcí:
   * **Maven** podpory jazyků Scala vytvoření projektu průvodce.
   * **SBT** ke správě závislostí a vývoj aplikací pro Scala projektu.

     ![Dialogové okno New Project (Nový projekt)](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Vyberte **Další**.

6. V **nový projekt** okno, zadejte následující informace:  

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název projektu| Zadejte název.  Tento kurz používá oblast `myApp`.|  
    |Projekt&nbsp;umístění| Zadejte požadované umístění pro uložení projektu.|
    |Sadu SDK projektu| To může být na první použití NÁPAD.  Vyberte **nové...**  a přejděte do vaší sady JDK.|
    |Verze Sparku|V Průvodci vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. Tento příklad používá **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Výběr sady Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Vyberte **Finish** (Dokončit).  Může trvat několik minut, než bude k dispozici projektu.

8. Artefakt Spark project automaticky vytvoří za vás. Chcete-li zobrazit artefakt, postupujte takto:

   a. V řádku nabídek, přejděte na **souboru** > **strukturu projektu...** .

   b. Z **strukturu projektu** okně **artefakty**.  

   c. Vyberte **zrušit** po zobrazují artefakt.

      ![Informace o artefaktu v dialogovém okně](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Přidejte zdrojový kód aplikace následujícím způsobem:

    a. V projektu, přejděte na **myApp** > **src** > **hlavní** > **scala**.  

    b. Klikněte pravým tlačítkem na **scala**a potom přejděte na **nový** > **Scala třídy**.

   ![Příkazů pro vytvoření třídy Scala z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. V **vytvořit novou třídu Scala** dialogové okno, zadejte název, vyberte **objekt** v **druh** rozevíracího seznamu a pak vyberte **OK**.

     ![Vytvoření dialogového okna Nová třída Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. **MyApp.scala** pak otevře v zobrazení pro hlavní soubor. Nahraďte výchozí kód kód, který najdete níže:  

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

    Kód čte data z HVAC.csv (k dispozici na všech clusterech HDInsight Spark), načte řádky, které mají pouze jednu číslici v sedmé sloupec v souboru CSV a zapíše výstup do `/HVACOut` pod výchozí kontejner úložiště pro cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Připojit ke clusteru HDInsight
Uživatel může buď [Přihlaste se k předplatnému Azure](#sign-in-to-your-azure-subscription), nebo [propojit HDInsight cluster](#link-a-cluster) pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené přihlašovací údaje pro připojení ke clusteru HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure.

1. V řádku nabídek, přejděte na **zobrazení** > **nástroj Windows** > **Průzkumníka služby Azure**.
       
   ![Propojení Průzkumníka služby Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Z Průzkumníka služby Azure, klikněte pravým tlačítkem myši **Azure** uzlu a pak vyberte **Sign In**.

3. V **Azure Sign In** dialogu **přihlášení**a pak zadejte přihlašovací údaje Azure.

    ![Dialogové okno Azure Sign In](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Jakmile jste přihlášení, **vyberte předplatná** dialogové okno obsahuje všechna předplatná Azure, které jsou spojeny s přihlašovacími údaji. Vyberte své předplatné a pak vyberte **vyberte** tlačítko.

    ![Dialogové okno Vybrat předplatná](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Z **Průzkumníka služby Azure**, rozbalte **HDInsight** zobrazíte clustery HDInsight Spark, které jsou v rámci vašeho předplatného.

    ![Clustery HDInsight Spark v Průzkumníku služby Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Chcete-li zobrazit prostředky (například účty úložiště), které jsou spojeny s clusterem, můžete dále rozšířit názvem clusteru uzel.

    ![Rozbalený uzel název clusteru](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Propojení clusteru
Cluster služby HDInsight můžete propojit s použitím Apache Ambari, spravovat uživatelské jméno. Podobně pro cluster HDInsight připojený k doméně můžete propojit pomocí doména a uživatelské jméno, například user1@contoso.com. Můžete také propojit Livy služby clusteru.

1. V řádku nabídek, přejděte na **zobrazení** > **nástroj Windows** > **Průzkumníka služby Azure**.

2. Z Průzkumníka služby Azure, klikněte pravým tlačítkem myši **HDInsight** uzlu a pak vyberte **clusteru A odkaz**.

   ![propojení clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Dostupné možnosti **clusteru A odkaz** okno se budou lišit v závislosti na hodnotu, která vyberete **Link Resource Type** rozevíracího seznamu.  Zadejte požadované hodnoty a pak vyberte **OK**.

    * **HDInsight Cluster**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku vazby|Vyberte **clusteru HDInsight** z rozevíracího seznamu.|
        |Název nebo adresu URL clusteru| Zadejte název clusteru.|
        |Typ ověření| Nechte **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí hodnota je správce.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|
    
        ![Dialogové okno clusteru hdinsight odkaz](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Služba Livy**  
  
        |Vlastnost |Hodnota |
        |----|----|
        |Typ prostředku vazby|Vyberte **Livy služby** z rozevíracího seznamu.|
        |Koncový bod Livy| Zadejte koncový bod Livy|
        |Název clusteru| Zadejte název clusteru.|
        |Koncový bod yarn|Volitelné.|
        |Typ ověření| Nechte **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí hodnota je správce.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        ![propojit dialogu clusteru livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Můžete zobrazit propojené clusteru z **HDInsight** uzlu.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.

   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala na cluster HDInsight Spark
Po vytvoření aplikace v jazyce Scala, můžete ho odeslat do clusteru.

1. V projektu, přejděte na **myApp** > **src** > **hlavní** > **scala**  >  **myApp**.  Klikněte pravým tlačítkem na **myApp**a vyberte **odeslání aplikace Spark** (pravděpodobně bude umístěn v dolní části seznamu).
    
      ![Odeslání aplikace Spark na HDInsight příkaz](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. V **odeslání aplikace Spark** dialogového okna, vyberte **1. Spark v HDInsight**.

3. V **upravit konfiguraci** okno, zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Clustery Spark (pouze Linux)|Vyberte, na kterém chcete spustit aplikaci v clusteru HDInsight Spark.|
    |Vyberte artefaktů k odeslání|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete symbol tří teček (**...** ) a výběrem jiné třídy.|
    |Konfigurace úlohy|Můžete změnit výchozí klíče nebo hodnoty. Další informace najdete v tématu [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|Můžete zadat argumenty oddělené místa pro hlavní třída v případě potřeby.|
    |Kromě souborů JAR odkazované a odkazované soubory|Cesty pro odkazované kromě souborů JAR a soubory můžete zadat případné. Další informace najdete tady: [Apache Spark konfigurace](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Viz také [jak nahrát prostředky do clusteru](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Úložiště odeslání úlohy|Rozbalte položku zobrazíte další možnosti.|
    |Typ úložiště|Vyberte **použití služby Azure Blob k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte klíč k úložišti.|
    |Kontejner úložiště|Vyberte kontejner úložiště z rozevíracího seznamu jednou **účtu úložiště** a **klíč úložiště** byl zadán.|

    ![Dialogové okno odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Vyberte **SparkJobRun** odeslání projektu pro vybraný cluster. **Vzdálené úlohy Spark v clusteru** karta zobrazuje průběh provádění úlohy v dolní části. Zastavte aplikaci klikněte na červené tlačítko. Zjistěte, jak získat přístup k výstupu úlohy, najdete v článku "přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ" dále v tomto článku.  
      
    ![V okně odeslání Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Ladění aplikací Apache Spark místně nebo vzdáleně v clusteru služby HDInsight 
Doporučujeme také jiný způsob odeslání aplikace Spark na clusteru. Můžete to provést nastavením parametrů **konfigurace spuštění/ladění** integrovaného vývojového prostředí. Další informace najdete v tématu [ladění aplikací Apache Spark místně nebo vzdáleně v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup a Správa clusterů HDInsight Spark pomocí sady Azure Toolkit pro IntelliJ
Pomocí sady Azure Toolkit pro IntelliJ můžete provádět různé operace.  Většinu operací inicializována z **Průzkumníka služby Azure**.  V řádku nabídek, přejděte na **zobrazení** > **nástroj Windows** > **Průzkumníka služby Azure**.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh

1. Z Průzkumníka služby Azure, přejděte na **HDInsight** > \<svůj Cluster >> **úlohy**.

    ![Úlohy zobrazení uzlu](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    ![Podrobnosti o aplikaci](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Zobrazit základní informace o úloze spuštěné, najeďte myší graf úlohy. Chcete-li zobrazit graf fází a informace, které generuje všechny úlohy, vyberte uzel v grafu úlohy.

    ![Podrobnosti o úloze fáze](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Chcete-li zobrazit často používají protokoly, *ovladač Stderr*, *ovladač Stdout*, a *informací o adresáři*, vyberte **protokolu** kartu.

    ![Podrobnosti protokolu](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Výběrem odkazu v horní části okna můžete také zobrazit historii Spark uživatelského rozhraní a Uživatelském rozhraní YARN (na úrovni aplikace).

### <a name="access-the-spark-history-server"></a>Přístup na server historie Sparku

1. Z Průzkumníka služby Azure, rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít uživatelské rozhraní historie Sparku**.  
2. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru, které jste zadali při nastavení clusteru.

3. V řídícím panelu server historie Sparku můžete název aplikace k vyhledání aplikace dokončení právě běží. V předchozím kódu, nastavte název aplikace s použitím `val conf = new SparkConf().setAppName("myApp")`. Proto je název vaší aplikace Spark **myApp**.

### <a name="start-the-ambari-portal"></a>Spusťte portál Ambari

1. Z Průzkumníka služby Azure, rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít Portal(Ambari) správy clusteru**.  

2. Jakmile budete vyzváni, zadejte přihlašovací údaje Správce clusteru. Tyto přihlašovací údaje jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure
Ve výchozím nastavení sady Azure Toolkit pro IntelliJ seznam clusterů Spark ze všech předplatných Azure. V případě potřeby můžete zadat předplatné, které chcete získat přístup.  

1. Z Průzkumníka služby Azure, klikněte pravým tlačítkem myši **Azure** kořenový uzel a potom vyberte **vyberte předplatná**.  

2. Z **vyberte předplatná** okna, zrušte zaškrtnutí políček u předplatných, které nechcete, aby pro přístup a pak vyberte **Zavřít**.

## <a name="spark-console"></a>Spark konzoly
Můžete spustit místní Console(Scala) Spark nebo spuštění Sparku Livy interaktivní relace Console(Scala).

### <a name="spark-local-consolescala"></a>Místní Console(Scala) Spark
Ujistěte se, že jste splnili WINUTILS. Předpokladem EXE.

1. V řádku nabídek, přejděte na **spustit** > **upravit konfiguraci...** .

2. Z **konfigurace spuštění/ladění** okno, v levém podokně přejděte do **Apache Spark v HDInsight** > **[Spark v HDInsight] myApp**.

3. V hlavním okně, vyberte **místně spustit** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Hlavní třída úlohy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete symbol tří teček (**...** ) a výběrem jiné třídy.|
    |Proměnné prostředí|Zajistěte, aby že hodnota HADOOP_HOME je správná.|
    |WINUTILS.exe umístění|Ujistěte se, že cesta je správná.|

    ![Konfigurace sady místní konzoly](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. V projektu, přejděte na **myApp** > **src** > **hlavní** > **scala**  >  **myApp**.  

6. V řádku nabídek, přejděte na **nástroje** > **Spark konzoly** > **spustit místní Console(Scala) Spark**.

7. Pak nemusí být zobrazeny dva dialogová okna klást, jestli chcete automaticky opravit závislosti. Pokud ano, vyberte **Automatická oprava**.

    ![Automatické Fix1 Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Automatické Fix2 Spark](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Konzole by měla vypadat podobně jako na obrázku níže. V typu okna konzoly `sc.appName`a potom stiskněte klávesu ctrl + Enter.  Zobrazí se výsledek. Místní konzoly můžete ukončit kliknutím na červené tlačítko.

    ![Výsledek místní konzoly](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Console(Scala) interaktivní relace Livy
Je podporován pouze na IntelliJ 2018.2 a 2018.3.

1. V řádku nabídek, přejděte na **spustit** > **upravit konfiguraci...** .

2. Z **konfigurace spuštění/ladění** okno, v levém podokně přejděte do **Apache Spark v HDInsight** > **[Spark v HDInsight] myApp**.

3. V hlavním okně, vyberte **vzdáleně spouštět v clusteru** kartu.

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Clustery Spark (pouze Linux)|Vyberte, na kterém chcete spustit aplikaci v clusteru HDInsight Spark.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete symbol tří teček (**...** ) a výběrem jiné třídy.|

    ![Konfigurace sady interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. V projektu, přejděte na **myApp** > **src** > **hlavní** > **scala**  >  **myApp**.  

6. V řádku nabídek, přejděte na **nástroje** > **Spark konzoly** > **Console(Scala) relaci spouštět Spark Livy interaktivní**.

7. Konzole by měla vypadat podobně jako na obrázku níže. V typu okna konzoly `sc.appName`a potom stiskněte klávesu ctrl + Enter.  Zobrazí se výsledek. Místní konzoly můžete ukončit kliknutím na červené tlačítko.

    ![Výsledek interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark

Je vhodné si můžete předvídat výsledek skriptu odesíláním nějaký kód do místní konzoly nebo interaktivní relace Console(Scala) Livy. Můžete zvýraznit nějaký kód v souboru Scala a pak klikněte pravým tlačítkem na **odeslat výběr do konzoly Spark**. Vybraný kód se odešlou do konzoly a provést. Výsledek se zobrazí po kódu v konzole. Konzole zjistí chyby, jestli existující.  

   ![Odeslat výběr do konzoly Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převést stávající aplikace IntelliJ IDEA pro použití sady Azure Toolkit pro IntelliJ

Můžete převést stávající Scala Spark aplikací, které jste vytvořili v IntelliJ IDEA, aby byl kompatibilní s Azure Toolkit pro IntelliJ. Potom můžete modul plug-in k odeslání aplikace do clusteru HDInsight Spark.

1. Pro existující aplikace Spark Scala, který byl vytvořen pomocí IntelliJ IDEA otevřete soubor přidružené .iml.

1. V kořenovém adresáři úroveň je **modulu** element následujícím postupem:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Proveďte editaci prvku přidat `UniqueKey="HDInsightTool"` tak, aby **modulu** element vypadá takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Uložte změny. Vaše aplikace by měla nyní být kompatibilní s Azure Toolkit pro IntelliJ. Můžete ho otestovat kliknutím pravým tlačítkem myši na název projektu v projektu. V rozbalovací nabídce má teď možnost **odeslání aplikace Spark na HDInsight**.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Chyba při místním spuštění: *Použít větší velikost haldy*
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
