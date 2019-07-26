---
title: 'Kurz – Azure Toolkit for IntelliJ: Vytváření aplikací Spark pro cluster HDInsight'
description: Kurz – použití Azure Toolkit for IntelliJ k vývoji aplikací Spark napsaných v Scala a jejich odesílání do clusteru HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: hrasheed
ms.openlocfilehash: 32f5ff2ebc9d938b1936d7f2929af83d552a543d
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489864"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Kurz: Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight

V tomto kurzu se dozvíte, jak používat modul plug-in Azure Toolkit for IntelliJ k vývoji Apache Spark aplikací napsaných v [Scala](https://www.scala-lang.org/)a pak je odeslat do clusteru HDInsight Spark přímo z integrovaného vývojového prostředí (IDE) IntelliJ. Modul plug-in můžete použít několika způsoby:

* Vývoj a odesílání aplikace Scala Spark v clusteru HDInsight Spark.
* Přístup k prostředkům Azure HDInsight Spark clusteru.
* Vývoj a spouštění aplikace Scala Spark lokálně

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Použití modulu plug-in Azure Toolkit for IntelliJ
> * Vývoj aplikací Apache Spark
> * Odeslat aplikaci do clusteru Azure HDInsight

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  V tomto kurzu se používá Java verze 8.0.202.

* IntelliJ IDEA. V tomto článku [se používá IntelliJ nápad Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Viz [instalace Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalace modulu plug-in Scala pro IntelliJ IDEA

K instalaci modulu plug-in Scala proveďte následující kroky:

1. Otevřete IntelliJ IDEA.

2. Na úvodní obrazovce přejděte na **Konfigurace** > **modulů plug-** in a otevřete okno **moduly plug-in** .
   
    ![Povolení modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Vyberte **nainstalovat** pro modul plug-in Scala, který je vybraný v novém okně.  

    ![Instalace modulu plug-in Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po úspěšné instalaci modulu plug-in je potřeba restartovat integrované vývojové prostředí (IDE).

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Spark Scala pro cluster HDInsight Spark

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .

2. V levém podokně vyberte **Azure Spark/HDInsight** .

3. V hlavním okně vyberte **projekt Spark (Scala)** .

4. V rozevíracím seznamu **Nástroj sestavení** vyberte jednu z následujících možností:
   * **Maven** for Scala – Průvodce vytvořením projektu – podpora.
   * **SBT** pro správu závislostí a sestavení projektu Scala.

     ![Dialogové okno New Project (Nový projekt)](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Vyberte **Další**.

6. V okně **Nový projekt** zadejte následující informace:  

    |  Vlastnost   | Popis   |  
    | ----- | ----- |  
    |Název projektu| Zadejte název.  Tento kurz používá oblast `myApp`.|  
    |Umístění&nbsp;projektu| Zadejte požadované umístění pro uložení projektu.|
    |Projektový SDK| Tato situace může být prázdná při prvním použití NÁPADu.  Vyberte **Nový...** a přejděte k JDK.|
    |Verze Sparku|Průvodce vytvořením integruje správnou verzi sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Výběr sady Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Vyberte **Finish** (Dokončit).  Může to trvat několik minut, než bude projekt k dispozici.

8. Projekt Spark automaticky vytvoří artefakt za vás. Chcete-li zobrazit artefakt, postupujte následovně:

   a. Z řádku nabídek přejděte na **soubor** > **struktura projektu...** .

   b. V okně **struktura projektu** vyberte artefakty .  

   c. Po zobrazení artefaktu vyberte **Zrušit** .

      ![Informace o artefaktu v dialogovém okně](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Svůj zdrojový kód aplikace přidejte následujícím způsobem:

    a. Z projektu přejděte na **MyApp** > **Src** > **Main** > **Scala**.  

    b. Klikněte pravým tlačítkem na **Scala**a potom přejděte k **nové** > **třídě Scala**.

   ![Příkazy pro vytvoření třídy Scala z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. V dialogovém okně **vytvořit novou třídu Scala** zadejte název, vyberte **objekt** v rozevíracím seznamu **typ** a pak vyberte **OK**.

     ![Dialogové okno vytvořit novou třídu Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Soubor **MyApp. Scala** se pak otevře v hlavním zobrazení. Nahraďte výchozí kód následujícím kódem, který najdete níže:  

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

    Kód přečte data z TVK. CSV (k dispozici ve všech clusterech HDInsight Spark), načte řádky obsahující pouze jednu číslici v sedmé sloupci v souboru CSV a zapíše výstup do `/HVACOut` výchozího kontejneru úložiště pro cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Připojení ke clusteru HDInsight
Uživatel se může [přihlásit k předplatnému Azure](#sign-in-to-your-azure-subscription)nebo [propojit cluster HDInsight](#link-a-cluster) s použitím Ambari uživatelského jména/hesla nebo přihlašovacích údajů připojených k doméně pro připojení ke clusteru HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Přihlásit k předplatnému Azure

1. V řádku nabídek přejděte k **zobrazení** > **nástrojů Windows** > **Azure Explorer**.
       
   ![Odkaz na Průzkumníka Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. V Průzkumníku Azure klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **Přihlásit**se.
   
   ![Odkaz na Průzkumníka Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. V dialogovém okně **přihlášení do Azure** zvolte přihlášení k **zařízení**a pak vyberte **Přihlásit**se.

    ![Dialogové okno přihlášení do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. V dialogovém okně **přihlášení k zařízení Azure** klikněte na **Kopírovat & otevřít**.
   
   ![Dialogové okno přihlášení do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-5.png)

5. V rozhraní prohlížeče vložte kód a potom klikněte na tlačítko **Další**.
   
   ![Dialogové okno přihlášení do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-6.png)

6. Zadejte svoje přihlašovací údaje Azure a pak zavřete prohlížeč.
   
   ![Dialogové okno přihlášení do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-7.png)

7. Po přihlášení se v dialogovém okně **Vybrat odběry** zobrazí všechna předplatná Azure, která jsou přidružená k přihlašovacím údajům. Vyberte své předplatné a pak vyberte tlačítko **Vybrat** .

    ![Dialogové okno vybrat odběry](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. V **Azure Exploreru**rozbalte **HDInsight** , abyste viděli clustery HDInsight Spark, které jsou ve vašich předplatných.

    ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

9.  Chcete-li zobrazit prostředky (například účty úložiště), které jsou přidruženy ke clusteru, můžete dále rozšířit uzel název clusteru.

    ![Rozšířený uzel s názvem clusteru](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Propojení clusteru

Cluster HDInsight můžete propojit pomocí spravovaného uživatelského jména Apache Ambari. Podobně v případě clusteru HDInsight připojeného k doméně můžete propojit s doménou a uživatelským jménem, jako `user1@contoso.com`je například. Také můžete propojit cluster služby Livy Service.

1. V řádku nabídek přejděte k **zobrazení** > **nástrojů Windows** > **Azure Explorer**.

2. V Průzkumníku Azure klikněte pravým tlačítkem na uzel **HDInsight** a pak vyberte **propojit cluster**.

   ![místní nabídka propojit cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. Dostupné možnosti v okně **propojit cluster** se budou lišit v závislosti na vybrané hodnotě v rozevíracím seznamu **typ prostředku propojení** .  Zadejte hodnoty a pak vyberte **OK**.

    * **Cluster HDInsight**  
  
        |Vlastnost |Value |
        |----|----|
        |Typ prostředku propojení|Z rozevíracího seznamu vyberte **cluster HDInsight** .|
        |Název nebo adresa URL clusteru| Zadejte název clusteru.|
        |Typ ověření| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí nastavení je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|
    
        ![Dialogové okno propojit cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Služba Livy**  
  
        |Vlastnost |Value |
        |----|----|
        |Typ prostředku propojení|V rozevíracím seznamu vyberte **Služba Livy** .|
        |Koncový bod Livy| Zadejte koncový bod Livy.|
        |Název clusteru| Zadejte název clusteru.|
        |Koncový bod příze|Volitelné.|
        |Typ ověření| Ponechat jako **základní ověřování**|
        |Uživatelské jméno| Zadejte uživatelské jméno clusteru, výchozí nastavení je admin.|
        |Heslo| Zadejte heslo pro uživatelské jméno.|

        ![Dialogové okno propojit cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Propojený cluster můžete zobrazit z uzlu **HDInsight** .

   ![propojený cluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

2. Cluster taky můžete odpojit od **Azure Exploreru**.

   ![nepropojený cluster](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Spuštění aplikace Spark Scala v clusteru HDInsight Spark

Po vytvoření aplikace Scala ji můžete odeslat do clusteru.

1. Z projektu přejděte do **MyApp** > **Src** > **Main** > ScalaMyApp > .  Klikněte pravým tlačítkem myši na položku **MyApp**a vyberte možnost **Odeslat aplikaci Spark** (ta bude pravděpodobně umístěna na konci seznamu).
    
      ![Příkaz Odeslat aplikaci Spark do HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. V dialogovém okně **Odeslat aplikaci Spark** vyberte **1. Spark ve službě**HDInsight.

3. V okně **Upravit konfiguraci** zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Hodnota |
    |----|----|
    |Clustery Spark (jenom Linux)|Vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.|
    |Vyberte artefakt, který se má odeslat.|Ponechte výchozí nastavení.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky ( **...** ).  a volba jiné třídy.|
    |Konfigurace úloh|Můžete změnit výchozí klíče nebo hodnoty. Další informace najdete v článku [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty příkazového řádku|V případě potřeby můžete zadat argumenty oddělené mezerou pro hlavní třídu.|
    |Odkazované jar a odkazované soubory|Můžete zadat cesty pro odkazované jar a soubory, pokud existují. Můžete také procházet soubory ve virtuálním systému souborů Azure, který aktuálně podporuje jenom cluster ADLS Gen 2. Další informace: [Konfigurace Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Viz také [Postup nahrání prostředků do clusteru](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Úložiště nahrávání úlohy|Rozbalením zobrazíte další možnosti.|
    |Typ úložiště|Vyberte **použít Azure Blob k nahrání** z rozevíracího seznamu.|
    |Účet úložiště|Zadejte svůj účet úložiště.|
    |Klíč úložiště|Zadejte svůj klíč úložiště.|
    |Kontejner úložiště|Po zadání **účtu úložiště** a **klíče úložiště** vyberte z rozevíracího seznamu svůj kontejner úložiště.|

    ![Dialogové okno pro odeslání Sparku](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Vyberte **SparkJobRun** a odešlete projekt do vybraného clusteru. **Vzdálená úloha Spark na kartě cluster** zobrazuje průběh provádění úlohy v dolní části. Aplikaci můžete zastavit kliknutím na tlačítko červené. Informace o přístupu k výstupu úlohy najdete v části "přístup a Správa clusterů HDInsight Spark pomocí Azure Toolkit for IntelliJ" dále v tomto článku.  
      
    ![Okno pro odeslání Sparku](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Místní nebo vzdálené ladění aplikací Apache Spark v clusteru HDInsight 

Doporučujeme také další způsob, jak odeslat aplikaci Spark do clusteru. To lze provést nastavením parametrů v integrovaném vývojovém prostředí (IDE) pro **spuštění nebo ladění** . Další informace najdete v tématu [ladění Apache Sparkch aplikací místně nebo vzdáleně v clusteru HDInsight s Azure Toolkit for IntelliJ prostřednictvím SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Přístup k clusterům HDInsight Spark a jejich správa pomocí Azure Toolkit for IntelliJ

Pomocí Azure Toolkit for IntelliJ můžete provádět různé operace.  Většina operací se zahajuje z **Průzkumníka Azure**.  V řádku nabídek přejděte k **zobrazení** > **nástrojů Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Přístup k zobrazení úlohy

1. V Azure Exploreru přejděte do služby **HDInsight** > \<, kterou cluster > > **úlohy**.

    ![Uzel zobrazení úlohy](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. V pravém podokně karta **zobrazení úlohy Spark** zobrazí všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

    ![Podrobnosti aplikace](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Pokud chcete zobrazit základní informace o spuštěné úloze, najeďte myší na graf úlohy. Chcete-li zobrazit graf fází a informace, které Každá úloha generuje, vyberte uzel v grafu úlohy.

    ![Podrobnosti fáze úlohy](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Pokud chcete zobrazit často používané protokoly, jako je například *ovladač stderr*, *ovladač stdout*a *informace o adresáři*, vyberte kartu **protokol** .

    ![Podrobnosti protokolu](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Pomocí odkazu v horní části okna můžete také zobrazit uživatelské rozhraní historie Spark a uživatelské rozhraní PŘÍZe (na úrovni aplikace).

### <a name="access-the-spark-history-server"></a>Přístup k serveru historie Sparku

1. V Azure Exploreru rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít historii Spark – uživatelské rozhraní**.  
2. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru, které jste zadali při nastavování clusteru.

3. Na řídicím panelu serveru historie Spark můžete použít název aplikace k vyhledání aplikace, kterou jste právě dokončili. V předchozím kódu nastavíte název aplikace pomocí `val conf = new SparkConf().setAppName("myApp")`. Proto má název aplikace Spark hodnotu **MyApp**.

### <a name="start-the-ambari-portal"></a>Spustit portál Ambari

1. V Azure Exploreru rozbalte **HDInsight**, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít cluster portál pro správu (Ambari)** .  

2. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. Tyto přihlašovací údaje jste zadali během procesu instalace clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure

Ve výchozím nastavení Azure Toolkit for IntelliJ vypíše Clustery Spark ze všech vašich předplatných Azure. V případě potřeby můžete zadat odběry, ke kterým chcete získat přístup.  

1. V Průzkumníku Azure klikněte pravým tlačítkem na kořenový uzel **Azure** a vyberte **Vybrat odběry**.  

2. V okně **Vybrat odběry** zrušte zaškrtnutí políček u předplatných, ke kterým nechcete získat přístup, a pak vyberte **Zavřít**.

## <a name="spark-console"></a>Konzola Spark

Můžete spustit místní konzolu Spark (Scala) nebo spustit konzolu Spark Livy Interactive Session (Scala).

### <a name="spark-local-consolescala"></a>Místní konzola Spark (Scala)

Ujistěte se, že jste splnili WINUTILS. Požadovaná součást EXE.

1. Z řádku nabídek přejděte na **Spustit** > **úpravy konfigurace...** .

2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v HDInsight** >  **[Spark v HDInsight] MyApp**.

3. V hlavním okně vyberte kartu **spustit místně** .

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Value |
    |----|----|
    |Hlavní třída úlohy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky ( **...** ).  a volba jiné třídy.|
    |Proměnné prostředí|Zajistěte, aby byla hodnota pro HADOOP_HOME správná.|
    |Umístění WINUTILS. exe|Zkontrolujte, zda je cesta správná.|

    ![Konfigurace sady místních konzol](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Z projektu přejděte do **MyApp** > **Src** > **Main** > ScalaMyApp > .  

6. V řádku nabídek přejděte do**konzoly** >  **nástroje** > Spark**spustit prostředí Spark místní konzola (Scala)** .

7. Pak se můžou zobrazit dvě dialogová okna, která vás požádají, pokud chcete automaticky opravovat závislosti. Pokud ano, vyberte možnost **automaticky opravit**.

    ![Spark auto Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark auto Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL + ENTER.  Zobrazí se výsledek. Místní konzolu můžete ukončit kliknutím na tlačítko červené.

    ![Výsledek místní konzoly](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Konzola pro interaktivní relace Spark Livy (Scala)

Podporuje se jenom v IntelliJ 2018,2 a 2018,3.

1. Z řádku nabídek přejděte na **Spustit** > **úpravy konfigurace...** .

2. V okně **Konfigurace spuštění/ladění** v levém podokně přejděte na **Apache Spark v HDInsight** >  **[Spark v HDInsight] MyApp**.

3. V hlavním okně vyberte kartu **vzdáleně spustit na clusteru** .

4. Zadejte následující hodnoty a pak vyberte **OK**:

    |Vlastnost |Value |
    |----|----|
    |Clustery Spark (jenom Linux)|Vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.|
    |Název hlavní třídy|Výchozí hodnota je hlavní třída z vybraného souboru. Třídu můžete změnit tak, že vyberete tři tečky ( **...** ).  a volba jiné třídy.|

    ![Konfigurace sady interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Z projektu přejděte do **MyApp** > **Src** > **Main** > ScalaMyApp > .  

6. V řádku nabídek přejděte do**konzoly** >  **nástroje** > Spark**Spustit Livy interaktivní relace (Scala) Sparku**.

7. Konzola by měla vypadat podobně jako na obrázku níže. V okně konzoly zadejte `sc.appName`a stiskněte kombinaci kláves CTRL + ENTER.  Zobrazí se výsledek. Místní konzolu můžete ukončit kliknutím na tlačítko červené.

    ![Výsledek interaktivní konzoly](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Odeslat výběr do konzoly Spark

Je vhodné předvídat výsledek skriptu odesláním kódu do místní konzole nebo konzoly Livy Interactive Session Console (Scala). Můžete zvýraznit nějaký kód v souboru Scala a pak klikněte pravým tlačítkem myši na **Odeslat výběr do konzoly Spark**. Vybraný kód se odešle do konzoly a provede se. Výsledek se zobrazí za kódem v konzole nástroje. Konzola zkontroluje chyby, pokud již existují.  

   ![Odeslat výběr do konzoly Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>Role jen pro čtení

Když uživatelé odešlou úlohu do clusteru s oprávněním role jen pro čtení, vyžadují se přihlašovací údaje Ambari.

### <a name="link-cluster-from-context-menu"></a>Propojit cluster s místní nabídkou

1. Přihlaste se pomocí účtu role jen pro čtení.
       
2. V **Azure Exploreru**rozbalte **HDInsight** pro zobrazení clusterů HDInsight, které jsou ve vašem předplatném. Clustery označené jako **role: čtenář** mají pouze oprávnění role jen pro čtení.

    ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-15.png)

3. Klikněte pravým tlačítkem na cluster s oprávněním role jen pro čtení. Pokud chcete propojit cluster, vyberte **propojit tento cluster** z kontextové nabídky. Zadejte uživatelské jméno a heslo pro Ambari.

  
    ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-11.png)

4. Pokud je cluster úspěšně propojený, služba HDInsight se aktualizuje.
   Fáze clusteru se stane propojená.
  
    ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Propojení clusteru rozbalením uzlu úlohy

1. Klikněte na uzel **úlohy** , okno **přístup k úloze clusteru odepřeno** .
   
2. Kliknutím na **propojit tento cluster** propojíte cluster.
   
    ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Propojit cluster s oknem konfigurace spuštění/ladění

1. Vytvořte konfiguraci HDInsight. Pak vyberte **vzdálené spuštění v clusteru**.
   
2. Vyberte cluster, který má oprávnění jen pro čtení pro clustery **Spark (pouze Linux)** . Zpráva upozornění se zobrazí. Kliknutím na **propojit tento cluster** můžete propojit cluster.
   
   ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/create-config-1.png)
   
### <a name="view-storage-accounts"></a>Zobrazit účty úložiště

* U clusterů s oprávněním role jen pro čtení klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová zařízení. Pro otevření Průzkumník služby Storage můžete kliknout na **otevřít Průzkumník služby Azure Storage** .
     
   ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-14.png)

   ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-10.png)

* U propojených clusterů klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová aplikace. Pro otevření Průzkumník služby Storage můžete kliknout na **otevřít Azure Storage** .
     
   ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-13.png)

   ![Clustery HDInsight Spark v Azure Exploreru](./media/apache-spark-intellij-tool-plugin/view-explorer-12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Převod stávajících IntelliJ NÁPADových aplikací na použití Azure Toolkit for IntelliJ

Stávající aplikace Spark Scala, které jste vytvořili v IntelliJ, můžete převést tak, aby byly kompatibilní s Azure Toolkit for IntelliJ. Pak můžete použít modul plug-in k odeslání aplikací do clusteru HDInsight Spark.

1. V případě existující aplikace Spark Scala vytvořené prostřednictvím IntelliJ NÁPADu otevřete přidružený soubor. IML.

2. Na kořenové úrovni je prvek **modulu** , například následující:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Upravte element tak, aby `UniqueKey="HDInsightTool"` bylo možné přidat prvek **modulu** , aby vypadal takto:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Uložte změny. Vaše aplikace by měla být teď kompatibilní s Azure Toolkit for IntelliJ. Můžete ho otestovat kliknutím pravým tlačítkem myši na název projektu v projektu. Místní nabídka má nyní možnost **Odeslat aplikaci Spark do HDInsight**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster, který jste vytvořili, pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.

1. V části **služby**vyberte **clustery HDInsight** .

1. V seznamu clusterů HDInsight, které se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro účely tohoto kurzu.

1. Vyberte **Odstranit**. Vyberte **Ano**.

![Odstranění clusteru HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat modul plug-in Azure Toolkit for IntelliJ k vývoji Apache Spark aplikací napsaných v [Scala](https://www.scala-lang.org/)a pak je odeslat do clusteru HDInsight Spark přímo z integrovaného vývojového prostředí IntelliJ ( INTEGROVANÉ VÝVOJOVÉ PROSTŘEDÍ (IDE). V dalším článku zjistíte, jak se data, která jste zaregistrovali v Apache Spark můžete načíst do nástroje BI Analytics, jako je například Power BI.

> [!div class="nextstepaction"]
> [Analýza dat pomocí nástrojů BI](apache-spark-use-bi-tools.md)
