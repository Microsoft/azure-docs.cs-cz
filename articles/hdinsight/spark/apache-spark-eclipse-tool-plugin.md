---
title: 'Azure Toolkit for Eclipse: Vytvoření aplikací Scala pro HDInsight Spark'
description: Pomocí nástrojů HDInsight v sadě nástrojů Azure pro Eclipse vyvíjejte aplikace Spark napsané v Scale a odesílejte je do clusteru HDInsight Spark přímo z IDE Eclipse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272107"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Použití sady Nástrojů Azure pro Eclipse k vytváření aplikací Apache Spark pro cluster HDInsight

Pomocí nástrojů HDInsight v sadě nástrojů Azure pro [Eclipse](https://www.eclipse.org/) můžete vyvíjet aplikace [Apache Spark](https://spark.apache.org/) napsané v [Scale](https://www.scala-lang.org/) a odesílat je do clusteru Azure HDInsight Spark přímo z IDE Eclipse. Modul plug-in Nástroje HDInsight můžete používat několika různými způsoby:

* Vývoj a odeslání aplikace Scala Spark v clusteru HDInsight Spark.
* Přístup k prostředkům clusteru Azure HDInsight Spark.
* Vývoj a spuštění aplikace Scala Spark místně.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark na HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). Tento článek používá Eclipse IDE pro vývojáře jazyka Java.

## <a name="install-required-plug-ins"></a>Instalace požadovaných modulů plug-in

### <a name="install-azure-toolkit-for-eclipse"></a>Instalace sady Azure Toolkit for Eclipse

Pokyny k instalaci najdete [v tématu Instalace sady Azure Toolkit for Eclipse](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalace modulu plug-in Scala

Když otevřete Eclipse, nástroje HDInsight automaticky zjistí, zda jste nainstalovali modul plug-in Scala. Chcete-li pokračovat, vyberte **možnost OK** a podle pokynů nainstalujte modul plug-in z webu Eclipse Marketplace. Po dokončení instalace restartujte ide.

![Automatická instalace zásuvného modulu Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Potvrzení modulů plug-in

1. Přejděte na **nápovědu** > **Eclipse Marketplace...**.

1. Vyberte kartu **Nainstalováno**.

1. Měli byste vidět alespoň:
    * Azure Toolkit \<pro verzi Eclipse>.
    * Scala IDE \<verze>.

## <a name="sign-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

1. Spuštění ide Eclipse.

1. Přejděte do **zobrazení okna** >  **Zobrazit** > **jiné...**  >  **Přihlaste se..**.

1. V dialogovém okně **Zobrazit zobrazení** přejděte do **Azure** > **Exploreru**a vyberte **Otevřít**.

   ![Zobrazení apache spark eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. V **Průzkumníkovi Azure**klikněte pravým tlačítkem myši na uzel **Azure** a pak vyberte **Přihlásit se**.

1. V dialogovém okně **Přihlášení do Azure** zvolte metodu ověřování, **vyberte Přihlásit**se a dokončete proces přihlášení.

   ![Apache Spark Eclipse Azure Sign](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Po přihlášení zobrazí dialogové okno **Vaše předplatná** všechna předplatná Azure přidružená k přihlašovacím údajům. Stisknutím **klávesy Select** zavřete dialogové okno.

   ![Dialogové okno Vybrat předplatná](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Z **Průzkumníka Azure**přejděte na **Azure** >  **HDInsight** a podívejte se na clustery HDInsight Spark v rámci vašeho předplatného.

   ![Clustery HDInsight Spark v Azure Exploreru3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Dále můžete rozbalit uzel názvu clusteru a zobrazit prostředky (například účty úložiště) přidružené ke clusteru.

   ![Rozšíření názvu clusteru pro zobrazení prostředků](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Propojení clusteru

Normální cluster můžete propojit pomocí uživatelského jména spravovaného společností Ambari. Podobně pro cluster HDInsight připojováno k doméně můžete propojit pomocí `user1@contoso.com`domény a uživatelského jména, například .

1. Z **Průzkumníka Azure**klikněte pravým tlačítkem myši na **HDInsight**a vyberte **Propojit cluster**.

   ![Nabídka clusteru propojení Průzkumníka Azure](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Zadejte **název clusteru**, **uživatelské jméno**a **heslo**a vyberte **ok**. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vybrat Kontejner úložiště pro průzkumníka úložiště, aby fungoval v levém stromovém zobrazení.

   ![Propojení dialogového okna nového clusteru HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Používáme propojený klíč úložiště, uživatelské jméno a heslo, pokud cluster přihlášen v předplatném Azure a propojen clusteru.
   > ![Účty úložiště Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Pro uživatele pouze klávesnice, když je aktuální fokus na **klíč úložiště**, musíte použít **Ctrl+TAB** k zaostření na další pole v dialogovém okně.

1. Propojený cluster uvidíte v části **HDInsight**. Nyní můžete odeslat žádost do tohoto propojeného clusteru.

   ![Propojený cluster Hdi Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Můžete také odpojit cluster z **Průzkumníka Azure**.

   ![Azure Explorer odpojil cluster](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Nastavení projektu Spark Scala pro cluster HDInsight Spark

1. V pracovním prostoru Eclipse IDE vyberte **Soubor** > **nového** > **projektu...**.

1. V průvodci **Nový projekt** vyberte **HDInsight Project** > **Spark na HDInsight (Scala).** Pak vyberte **Další**.

   ![Výběr projektu Spark na HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. V dialogovém okně Nový projekt **SCALA Nové HDInsight** zadejte následující hodnoty a pak vyberte **Další**:
   * Zadejte název projektu.
   * V oblasti **JRE,** ujistěte se, že **použití prostředí spuštění JRE** je nastavena na **JavaSE-1.7** nebo novější.
   * V oblasti **Knihovna Spark** můžete zvolit **Použít Maven ke konfiguraci možnosti Spark SDK.**  Náš nástroj integruje správnou verzi pro Spark SDK a Scala SDK. Můžete také zvolit **možnost Přidat spark SDK ručně,** stáhnout a přidat Spark SDK ručně.

   ![Dialogové okno Nový projekt HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. V dalším dialogovém okně zkontrolujte podrobnosti a pak vyberte **Dokončit**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace Scala pro cluster HDInsight Spark

1. V **Průzkumníku balíčků**rozbalte projekt, který jste vytvořili dříve. Klepněte pravým tlačítkem myši na **položku src**, vyberte **možnost Nové** > **další...**.

1. V **dialogovém** okně Vybrat průvodce vyberte **položku Scala Wizards** > **Scala Object**. Pak vyberte **Další**.

   ![Výběr průvodce Vytvořením objektu Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. V dialogovém okně **Vytvořit nový soubor** zadejte název objektu a pak vyberte **Dokončit**. Otevře se textový editor.

   ![Průvodce vytvořením nového souboru](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. V textovém editoru nahraďte aktuální obsah níže uvedenou kódem:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Spuštění aplikace v clusteru HDInsight Spark:

   a. V Průzkumníku balíčků klikněte pravým tlačítkem myši na název projektu a potom vyberte **Odeslat aplikaci Spark do HDInsightu**.

   b. V dialogovém okně **Odeslání jiskry** zadejte následující hodnoty a pak vyberte **Odeslat**:

   * V **části Název clusteru**vyberte cluster HDInsight Spark, ve kterém chcete aplikaci spustit.
   * Vyberte artefakt z projektu Eclipse nebo jeden z pevného disku. Výchozí hodnota závisí na položce, na kterou klepnete pravým tlačítkem myši z Průzkumníka balíčků.
   * V rozevíracím seznamu **Název hlavní třídy** průvodce odesláním zobrazí všechny názvy objektů z projektu. Vyberte nebo zadejte ten, který chcete spustit. Pokud jste vybrali artefakt z pevného disku, musíte zadat název hlavní třídy ručně. 
   * Vzhledem k tomu, že kód aplikace v tomto příkladu nevyžaduje žádné argumenty příkazového řádku nebo odkazovat na jars nebo soubory, můžete ponechat zbývající textová pole prázdná.

     ![Dialogové okno Odeslání Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Karta **Odeslání spark** by měla začít zobrazovat průběh. Aplikaci můžete zastavit výběrem červeného tlačítka v okně **Odeslání Jiskry.** Protokoly pro tuto konkrétní aplikaci můžete také zobrazit tak, že vyberete ikonu zeměkoule (označenou modrým polem na obrázku).

   ![Okno odeslání Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Přístup ke clusterům HDInsight Spark a správa pomocí nástrojů HDInsight v sadě Nástrojů Azure pro Eclipse

Můžete provádět různé operace pomocí nástroje HDInsight, včetně přístupu k výstupu úlohy.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh

1. V **Průzkumníkovi Azure**rozbalte **HDInsight**, pak název clusteru Spark a pak vyberte **Úlohy**.

   ![Uzel zobrazení úlohy Azure Explorer Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Vyberte uzel **Úlohy.** Pokud je verze Java nižší než **1,8**, nástroje HDInsight tools automaticky připomene instalaci modulu plug-in **E(fx)clipse.** Chcete-li pokračovat, vyberte **možnost OK** a potom podle pokynů průvodce nainstalujte jej z webu Eclipse Marketplace a restartujte eclipse.

   ![Nainstalujte chybějící plugin E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Otevřete zobrazení úloh z uzlu **Úlohy.** V pravém podokně karta **Zobrazení úloh Spark** zobrazuje všechny aplikace spuštěné v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

   ![Podrobnosti protokolů úloh zobrazení Apache Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Poté můžete provést některou z těchto akcí:

   * Najeďte na graf úlohy. Zobrazuje základní informace o spuštěné úloze. Vyberte graf úlohy a uvidíte fáze a informace, které každá úloha generuje.

     ![Apache Spark informace o fázi fáze grafu úlohy](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Výběrem karty **Protokol** zobrazíte často používané protokoly, včetně **ovladačů Stderr**, **Stdout a** **informací o adresáři**.

     ![Apache Spark Eclipse informace o protokolu úloh](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Otevřete ui historie Spark a Apache Hadoop YARN UI (na úrovni aplikace) výběrem hypertextových odkazů v horní části okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Přístup ke kontejneru úložiště pro cluster

1. V Průzkumníkovi Azure rozbalte kořenový uzel **HDInsight** a zotřete seznam clusterů HDInsight Spark, které jsou k dispozici.

1. Rozbalením názvu clusteru zobrazíte účet úložiště a výchozí kontejner úložiště pro cluster.

   ![Účet úložiště a výchozí kontejner úložiště](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Vyberte název kontejneru úložiště přidružený ke clusteru. V pravém podokně poklepejte na složku **HVACOut.** Otevřete jeden z **part-** soubory pro zobrazení výstupu aplikace.

### <a name="access-the-spark-history-server"></a>Přístup k serveru historie Spark

1. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na název clusteru Spark a pak vyberte **Otevřít uživatelské rozhraní historie Sparku**. Po zobrazení výzvy zadejte přihlašovací údaje správce clusteru. Zadali jste je při zřizování clusteru.

1. Na řídicím panelu serveru historie Spark můžete pomocí názvu aplikace vyhledat aplikaci, kterou jste právě dokončili. V předchozím kódu nastavíte název aplikace `val conf = new SparkConf().setAppName("MyClusterApp")`pomocí . Takže název aplikace Spark byl **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Spuštění portálu Apache Ambari

1. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na název clusteru Spark a pak vyberte **Otevřít portál pro správu clusteru (Ambari).**

1. Po zobrazení výzvy zadejte přihlašovací údaje správce clusteru. Zadali jste je při zřizování clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure

Ve výchozím nastavení nástroj HDInsight v sadě nástrojů Azure toolkit for Eclipse uvádí clustery Spark ze všech vašich předplatných Azure. V případě potřeby můžete určit odběry, pro které chcete získat přístup ke clusteru.

1. V Průzkumníkovi Azure klikněte pravým tlačítkem myši na kořenový uzel **Azure** a potom vyberte **Spravovat předplatná**.

1. V dialogovém okně zrušte zaškrtnutí políček u předplatného, ke kterému nechcete mít přístup, a pak vyberte **Zavřít**. Pokud se chcete odhlásit z předplatného Azure, můžete taky vybrat **Odhlásit** se.

## <a name="run-a-spark-scala-application-locally"></a>Místní spuštění aplikace Spark Scala

Pomocí nástrojů HDInsight v sadě nástrojů Azure toolkit pro Eclipse můžete spustit aplikace Spark Scala místně na vaší pracovní stanici. Tyto aplikace obvykle nepotřebují přístup k prostředkům clusteru, jako je například kontejner úložiště, a můžete je spustit a otestovat místně.

### <a name="prerequisite"></a>Požadavek

Při spuštění místní aplikace Spark Scala v počítači se systémem Windows se může stát výjimka, jak je vysvětleno v [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). K této výjimce dochází, protože program **WinUtils.exe** v systému Windows chybí.

Chcete-li tuto chybu vyřešit, potřebujete [soubor Winutils.exe](https://github.com/steveloughran/winutils) do umístění, jako **je C:\WinUtils\bin**, a potom přidejte proměnnou prostředí **HADOOP_HOME** a nastavte hodnotu proměnné na **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Spuštění místní aplikace Spark Scala

1. Spusťte Eclipse a vytvořte projekt. V dialogovém okně **Nový projekt** proveďte následující volby a pak vyberte **Další**.

1. V průvodci **Nový projekt** vyberte **HDInsight Project** > **Spark na ukázce místního spuštění HDInsight (Scala).** Pak vyberte **Další**.

   ![Nový projekt vybere dialogové okno průvodce.](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Chcete-li poskytnout podrobnosti o projektu, postupujte podle kroků 3 až 6 z předchozí části [Nastavení projektu Spark Scala pro cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Šablona přidá ukázkový kód (**LogQuery**) do složky **src,** kterou můžete spustit místně v počítači.

   ![Umístění místní aplikace Scala LogQuery](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Klepněte pravým tlačítkem myši na **soubor LogQuery.scala** a vyberte příkaz **Spustit jako** > **1 aplikaci Scala**. Takový výstup se zobrazí na kartě **Konzola:**

   ![Výsledek místního spuštění aplikace Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Role pouze pro čtenáře

Když uživatelé odešlou úlohu do clusteru s oprávněním role pouze pro čtenáře, je vyžadována pověření Ambari.

### <a name="link-cluster-from-context-menu"></a>Propojení clusteru z místní nabídky

1. Přihlaste se pomocí účtu role pouze pro čtenáře.

2. Z **Průzkumníka Azure**rozbalte **HDInsight** a zobrazte clustery HDInsight, které jsou ve vašem předplatném. Clustery označené **"Role:Reader"** mají pouze oprávnění role pouze pro čtení.

    ![Clustery HDInsight Spark ve čtečce rolí Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Klikněte pravým tlačítkem myši na cluster s oprávněním role pouze pro čtení. Vyberte **Propojit tento cluster** z kontextové nabídky a propojit cluster. Zadejte uživatelské jméno a heslo Ambari.

    ![Clustery HDInsight Spark v odkazu na Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Pokud je cluster úspěšně propojen, hdinsight se aktualizuje.
   Fáze clusteru se propojí.
  
    ![Clustery HDInsight Spark v Azure Exploreru propojené](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Propojit cluster rozbalením uzlu Úlohy

1. Klikněte na uzel **Úlohy,** zobrazí se okno **Odepřený přístup k úlohám clusteru.**

2. Chcete-li propojit cluster, klepněte na odkaz **na tento cluster.**

    ![Clustery HDInsight Spark v Azure Exploreru9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Propojit cluster z okna Odeslání Spark

1. Vytvořte projekt HDInsight.

2. Klikněte pravým tlačítkem myši na balíček. Pak vyberte **Odeslat aplikaci Spark do HDInsight**.

   ![Clustery HDInsight Spark v Azure Exploreru odeslat](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Vyberte cluster, který má oprávnění role pouze pro čtení pro **název clusteru**. Zobrazí se varovná zpráva. Chcete-li propojit cluster, můžete klepnout na odkaz **na tento cluster.**

   ![Clustery HDInsight Spark v Azure Exploreru propojují](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Zobrazit účty úložiště

* U clusterů s oprávněním pouze pro čtení klikněte na uzel **Účty úložiště,** zobrazí se okno **Odepřeno přístupu k úložišti.**

   ![Clustery HDInsight Spark v úložišti Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Clustery HDInsight Spark v Azure Exploreru byly odepřeny](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* U propojených clusterů klikněte na uzel **Účty úložiště,** objeví se okno **Odepřeno přístupu k úložišti.**

   ![Clustery HDInsight Spark v Azure Exploreru byly zamítnuty2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Známé problémy

Při použití **link clusteru**, navrhoval bych vám poskytnout pověření úložiště.

![propojení clusteru s zatměním pověření úložiště](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Existují dva režimy, které mohou být odesílány. Pokud je k dispozici pověření úložiště, dávkový režim se použije k odeslání úlohy. V opačném případě bude použit interaktivní režim. Pokud je cluster zaneprázdněn, může se zobrazí níže uvedená chyba.

![zatmění dostat chybu, když cluster ujet](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "zatmění dostat chybu, když cluster ujet")

![zatmění dostat chybu, když cluster ujeté příze](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "zatmění dostat chybu, když cluster ujeté příze")

## <a name="see-also"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Vytvoření a odeslání aplikací Spark Scala pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím sítě VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití sady Azure Toolkit for IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa zdrojů

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
