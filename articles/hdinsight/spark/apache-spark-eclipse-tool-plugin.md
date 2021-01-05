---
title: 'Azure Toolkit for Eclipse: vytváření aplikací Scala pro HDInsight Spark'
description: Nástroje HDInsight v Azure Toolkit for Eclipse slouží k vývoji aplikací Spark napsaných v Scala a jejich odeslání do clusteru HDInsight Spark přímo z integrovaného vývojového prostředí (IDE).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 33cbb9b5ac754969a6a9038db227123bae3a0ea7
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822399"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Použití Azure Toolkit for Eclipse k vytvoření Apache Spark aplikací pro cluster HDInsight

Pomocí nástrojů HDInsight v sadě Azure Toolkit for [zatmění](https://www.eclipse.org/) můžete vyvíjet aplikace [Apache Spark](https://spark.apache.org/) napsané v [Scala](https://www.scala-lang.org/) a odeslat je do clusteru Azure HDInsight Spark přímo z integrovaného vývojového prostředí (IDE). Modul plug-in nástroje HDInsight můžete použít několika různými způsoby:

* K vývoji a odeslání aplikace Scala Spark v clusteru HDInsight Spark.
* Pro přístup k prostředkům clusteru Azure HDInsight Spark.
* Pro vývoj a spouštění aplikace Scala Spark lokálně.

## <a name="prerequisites"></a>Požadavky

* Apache Spark clusteru v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Rozhraní IDE pro zatmění](https://www.eclipse.org/downloads/). Tento článek používá pro vývojáře v jazyce Java integrované vývojové prostředí (zatmění).

## <a name="install-required-plug-ins"></a>Nainstalovat požadované moduly plug-in

### <a name="install-azure-toolkit-for-eclipse"></a>Instalace sady Azure Toolkit for Eclipse

Pokyny k instalaci najdete v tématu [instalace Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Instalace modulu plug-in Scala

Když otevřete položku zatmění, nástroje HDInsight automaticky zjistí, jestli jste nainstalovali modul plug-in Scala. Pokračujte výběrem **OK** a pak postupujte podle pokynů k instalaci modulu plug-in z webu zatmění. Po dokončení instalace restartujte prostředí IDE.

![Automatická instalace modulu plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Potvrdit moduly plug-in

1. Přejít na   >  **Web Help zatmění na webu...**

1. Vyberte kartu **Nainstalováno**.

1. Měli byste vidět aspoň tyto informace:
    * Azure Toolkit for Eclipse \<version> .
    * Scala IDE \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

1. Spusťte prostředí IDE s zatmění.

1. Přejít do **okna**  >   **Zobrazit**  >  **Další zobrazení...**  >  **Přihlásit se..**.

1. V dialogovém okně **Zobrazit zobrazení** přejděte na **Azure**  >  **Azure Explorer** a pak vyberte **otevřít**.

   ![Zobrazit zobrazení Apache Spark zatmění](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. V **Průzkumníku Azure** klikněte pravým tlačítkem na uzel **Azure** a pak vyberte **Přihlásit** se.

1. V dialogovém okně **přihlášení do Azure** zvolte metodu ověřování, vyberte **Přihlásit** se a dokončete proces přihlašování.

   ![Apache Spark – přihlašování k Azure v zatmění](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Po přihlášení se v dialogovém okně **Vaše předplatná** zobrazí všechna předplatná Azure přidružená k těmto přihlašovacím údajům. Stisknutím tlačítka **Vybrat** zavřete dialogové okno.

   ![Dialogové okno vybrat odběry](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. V **Azure Exploreru** přejděte do **Azure**  >   **HDInsight** , abyste viděli clustery HDInsight Spark v rámci vašeho předplatného.

   ![Clustery HDInsight Spark v Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Uzel názvu clusteru můžete dále rozšířit, aby se zobrazily prostředky (například účty úložiště) přidružené ke clusteru.

   ![Rozšíření názvu clusteru pro zobrazení prostředků](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Propojení clusteru

Běžný cluster můžete propojit pomocí spravovaného uživatelského jména Ambari. Podobně v případě clusteru HDInsight připojeného k doméně můžete propojit s doménou a uživatelským jménem, jako je například `user1@contoso.com` .

1. V **Průzkumníku Azure** klikněte pravým tlačítkem na **HDInsight** a vyberte **propojit cluster**.

   ![Nabídka cluster odkazů Azure Explorer](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Zadejte **název clusteru**, **uživatelské jméno** a **heslo** a pak vyberte **OK**. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vybrat kontejner úložiště pro Průzkumníka služby Storage, aby fungoval v levém stromovém zobrazení.

   ![Dialog připojit nový cluster HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > V případě, že se ke clusteru přihlásilo v předplatném Azure i v souvislosti s clusterem, používáme klíč propojeného úložiště, uživatelské jméno a heslo
   > ![Účty úložiště Azure Explorer](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Pouze pro uživatele s klávesnicí, pokud je aktuální fokus na **klíč úložiště**, je nutné použít **klávesovou zkratku CTRL + TAB** pro zaměření na další pole v dialogovém okně.

1. Propojený cluster můžete zobrazit v **HDInsight**. Nyní můžete odeslat aplikaci do tohoto odkazovaného clusteru.

   ![Propojený cluster Azure Explorer HDI](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Cluster taky můžete odpojit od **Azure Exploreru**.

   ![Cluster s nepropojeným průzkumníkem Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Nastavení projektu Spark Scala pro cluster HDInsight Spark

1. Z pracovního prostoru prostředí IDE pro zatmění vyberte **soubor**  >  **Nový**  >  **projekt...**.

1. V průvodci **vytvořením nového projektu** vyberte **HDInsight Project**  >  **Spark v HDInsight (Scala)**. Pak vyberte **Další**.

   ![Výběr projektu Spark na HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. V dialogovém okně **Nový projekt HDInsight Scala** zadejte následující hodnoty a potom vyberte **Další**:
   * Zadejte název projektu.
   * V oblasti **JRE** se ujistěte, že **použití spouštěcího prostředí JRE** je nastavené na **Java-1,7** nebo novější.
   * V oblasti **knihovny Spark** můžete zvolit možnost **použít Maven ke konfiguraci sady Spark SDK** .  Náš nástroj integruje správnou verzi pro sadu Spark SDK a sadu Scala SDK. Můžete také zvolit možnost **Přidat sadu Spark SDK ručně** , stáhnout a přidat sadu Spark SDK ručně.

   ![Nový projekt HDInsight Scala – dialogové okno](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. V dalším dialogovém okně Zkontrolujte podrobnosti a pak vyberte **Dokončit**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace v Scala pro cluster HDInsight Spark

1. V **Průzkumníku balíčků** rozbalte projekt, který jste vytvořili dříve. Klikněte pravým tlačítkem na **Src**, vyberte **Nový**  >  **...**.

1. V dialogovém okně **Vybrat Průvodce** vyberte **Scala průvodci**  >  **Scala objekt**. Pak vyberte **Další**.

   ![Výběr Průvodce vytvořením objektu Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. V dialogovém okně **vytvořit nový soubor** zadejte název objektu a pak vyberte **Dokončit**. Otevře se textový editor.

   ![Průvodce novým souborem – vytvořit nový soubor](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. V textovém editoru nahraďte aktuální obsah následujícím kódem:

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

1. Spusťte aplikaci v clusteru HDInsight Spark:

   a. V Průzkumníku balíčků klikněte pravým tlačítkem myši na název projektu a pak vyberte **Odeslat aplikaci Spark do HDInsight**.

   b. V dialogovém okně pro **odeslání Sparku** zadejte následující hodnoty a pak vyberte **Odeslat**:

   * V poli **název clusteru** vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.
   * Vyberte artefakt z projektu zatmění nebo ho vyberte z pevného disku. Výchozí hodnota závisí na položce, kterou kliknete pravým tlačítkem z Průzkumníka balíčků.
   * V rozevíracím seznamu **název hlavní třídy** se v Průvodci odesláním zobrazí všechny názvy objektů z vašeho projektu. Vyberte nebo zadejte jednu z nich, kterou chcete spustit. Pokud jste vybrali artefakt z pevného disku, je nutné zadat název hlavní třídy ručně. 
   * Vzhledem k tomu, že kód aplikace v tomto příkladu nevyžaduje žádné argumenty příkazového řádku nebo referenční jar nebo soubory, můžete zbývající textová pole nechat prázdná.

     ![Dialogové okno pro odeslání Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Na kartě pro **odeslání Spark** by se mělo začít zobrazovat průběh. Aplikaci můžete zastavit tak, že v okně pro **odeslání Sparku** vyberete červené tlačítko. Můžete také zobrazit protokoly pro tuto konkrétní aplikaci, a to tak, že vyberete ikonu zeměkoule (označeno modrým polem v obrázku).

   ![Okno pro odeslání Apache Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Přístup k clusterům HDInsight Spark a jejich správa pomocí nástrojů HDInsight v Azure Toolkit for Eclipse

Pomocí nástrojů HDInsight můžete provádět různé operace, včetně přístupu k výstupu úlohy.

### <a name="access-the-job-view"></a>Přístup k zobrazení úlohy

1. V **Azure Exploreru** rozbalte **HDInsight**, potom název clusteru Spark a pak vyberte **úlohy**.

   ![Uzel zobrazení úlohy zatmění v Průzkumníkovi Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Vyberte uzel **úlohy** . Pokud je verze Java nižší než **1,8**, nástroje HDInsight automaticky připomenutí vám umožní nainstalovat modul plug-in pro **kliparty E (FX)** . Pokračujte výběrem **OK** a pak postupujte podle pokynů průvodce a nainstalujte ho z webu zatmění a restartujte.

   ![Nainstalovat výstřižek chybějícího modulu plug-in E (FX)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Otevřete zobrazení úlohy z uzlu **úlohy** . V pravém podokně karta **zobrazení úlohy Spark** zobrazí všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

   ![Podrobnosti o protokolech úloh zobrazení prozatmění v Apache](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Pak můžete provést kteroukoli z těchto akcí:

   * Najeďte myší na graf úlohy. Zobrazuje základní informace o spuštěné úloze. Vyberte graf úlohy a můžete zobrazit fáze a informace, které Každá úloha generuje.

     ![Informace o fázi grafu Apache Spark úlohy](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Vyberte kartu **protokol** pro zobrazení často používaných protokolů, včetně protokolu **stderr**, **ovladače stdout** a **informací o adresáři**.

     ![Informace o protokolu úlohy Apache Spark zatmění](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Kliknutím na hypertextový odkaz v horní části okna otevřete ovládací panel Historie Sparku a uživatelské rozhraní Apache Hadoop nitě (na úrovni aplikace).

### <a name="access-the-storage-container-for-the-cluster"></a>Přístup k kontejneru úložiště pro cluster

1. V Azure Exploreru rozbalte kořenový uzel **HDInsight** a zobrazte seznam dostupných clusterů HDInsight Spark.

1. Rozbalte název clusteru, abyste viděli účet úložiště a výchozí kontejner úložiště pro cluster.

   ![Účet úložiště a výchozí kontejner úložiště](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Vyberte název kontejneru úložiště, který je přidružený ke clusteru. V pravém podokně klikněte dvakrát na složku pro **TVK** . Otevřete jeden z **částí** souborů, abyste viděli výstup aplikace.

### <a name="access-the-spark-history-server"></a>Přístup k serveru historie Sparku

1. V Průzkumníku Azure klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít historii Spark – uživatelské rozhraní**. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. Při zřizování clusteru jste tyto služby zadali.

1. Na řídicím panelu serveru historie Sparku použijte název aplikace a vyhledejte aplikaci, kterou jste právě dokončili. V předchozím kódu nastavíte název aplikace pomocí `val conf = new SparkConf().setAppName("MyClusterApp")` . Takže název vaší aplikace Spark byl **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Spustit portál Apache Ambari

1. V Průzkumníku Azure klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít cluster portál pro správu (Ambari)**.

1. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru. Při zřizování clusteru jste tyto služby zadali.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure

Ve výchozím nastavení nástroj HDInsight v Azure Toolkit for Eclipse uvádí Clustery Spark ze všech vašich předplatných Azure. V případě potřeby můžete zadat odběry, pro které chcete získat přístup ke clusteru.

1. V Průzkumníku Azure klikněte pravým tlačítkem na kořenový uzel **Azure** a pak vyberte **spravovat předplatná**.

1. V dialogovém okně zrušte zaškrtnutí políček u předplatného, ke kterému nechcete získat přístup, a pak vyberte **Zavřít**. Můžete také vybrat možnost **Odhlásit** se, pokud se chcete odhlásit z předplatného Azure.

## <a name="run-a-spark-scala-application-locally"></a>Místní spuštění aplikace Spark Scala

Pomocí nástrojů služby HDInsight v Azure Toolkit for Eclipse můžete spouštět aplikace Spark Scala místně na pracovní stanici. Obvykle tyto aplikace nepotřebují přístup k prostředkům clusteru, jako je například kontejner úložiště, a můžete je spustit a otestovat místně.

### <a name="prerequisite"></a>Požadavek

Když spouštíte místní aplikaci Spark Scala na počítači se systémem Windows, může se vám zobrazit výjimka, jak je vysvětleno v [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). K této výjimce dochází, protože ve Windows chybí **WinUtils.exe** .

Chcete-li vyřešit tuto chybu, potřebujete [Winutils.exe](https://github.com/steveloughran/winutils) do umístění, jako je **C:\WinUtils\bin**, a pak přidat proměnnou prostředí **HADOOP_HOME** a nastavit hodnotu proměnné na **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Spuštění místní aplikace Spark Scala

1. Spusťte zatmění a vytvořte projekt. V dialogovém okně **Nový projekt** proveďte následující volby a pak vyberte **Další**.

1. V průvodci **vytvořením nového projektu** vyberte v   >  **ukázce pro místní spuštění HDInsight možnost HDInsight Project Spark (Scala)**. Pak vyberte **Další**.

   ![Nový projekt vybere dialog průvodce.](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Chcete-li poskytnout podrobné informace o projektu, postupujte podle kroků 3 až 6 v předchozí části [nastavení projektu Spark Scala pro cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Šablona přidá vzorový kód (**LogQuery**) do složky **Src** , kterou můžete spustit místně na vašem počítači.

   ![Umístění místní aplikace Scala pro LogQuery](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Klikněte pravým tlačítkem na **LogQuery. Scala** a vyberte **Spustit jako**  >  **1 Scala aplikaci**. Výstup podobný tomuto se zobrazí na kartě **Konzola** :

   ![Výsledek místního spuštění aplikace Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Role jen pro čtení

Když uživatelé odešlou úlohu do clusteru s oprávněním role jen pro čtení, vyžadují se přihlašovací údaje Ambari.

### <a name="link-cluster-from-context-menu"></a>Propojit cluster s místní nabídkou

1. Přihlaste se pomocí účtu role jen pro čtení.

2. V **Azure Exploreru** rozbalte **HDInsight** pro zobrazení clusterů HDInsight, které jsou ve vašem předplatném. Clustery označené jako **role: čtenář** mají pouze oprávnění role jen pro čtení.

    ![Clustery HDInsight Spark v čtečce rolí Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Klikněte pravým tlačítkem na cluster s oprávněním role jen pro čtení. Pokud chcete propojit cluster, vyberte **propojit tento cluster** z kontextové nabídky. Zadejte uživatelské jméno a heslo pro Ambari.

    ![Odkazy na clustery HDInsight Spark v Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Pokud je cluster úspěšně propojený, služba HDInsight se aktualizuje.
   Fáze clusteru se stane propojená.
  
    ![Clustery HDInsight Spark v aplikaci Azure Explorer propojeny](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Propojení clusteru rozbalením uzlu úlohy

1. Klikněte na uzel **úlohy** , okno **přístup k úloze clusteru odepřeno** .

2. Kliknutím na **propojit tento cluster** propojíte cluster.

    ![Clustery HDInsight Spark v Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Propojit cluster s oknem odeslání Spark

1. Vytvořte projekt HDInsight.

2. Klikněte pravým tlačítkem na balíček. Pak vyberte **Odeslat aplikaci Spark do HDInsight**.

   ![Odeslání clusterů HDInsight Spark v Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Vyberte cluster, který má oprávnění role jen pro čtení pro **název clusteru**. Zpráva upozornění se zobrazí. Kliknutím na **propojit tento cluster** můžete propojit cluster.

   ![Propojování clusterů HDInsight Spark v Azure Exploreru](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Zobrazit účty úložiště

* U clusterů s oprávněním role jen pro čtení klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová zařízení.

   ![Clustery HDInsight Spark v Azure Exploreru Storage](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Clustery HDInsight Spark v Azure Exploreru se zamítly.](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* U propojených clusterů klikněte na uzel **účty úložiště** , na okno **přístup k úložišti** se zobrazí stavová aplikace.

   ![Clustery HDInsight Spark v Azure Exploreru denied2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Známé problémy

Při použití **propojení clusteru** doporučujeme zadat přihlašovací údaje úložiště.

![propojit cluster s přihlašovacími údaji služby Storage – zatmění](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Existují dva režimy, jak odeslat úlohy. Pokud je zadané přihlašovací údaje úložiště, použije se k odeslání úlohy dávkový režim. V opačném případě bude použit interaktivní režim. Pokud je cluster zaneprázdněný, může se zobrazit chyba níže.

![Při zaneprázdněném clusteru se zobrazí chyba funkce zatmění](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Při zaneprázdněném clusteru se zobrazí chyba funkce zatmění")

![Při zaneprázdněném přízi clusteru se zobrazí chyba funkce zatmění](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "Při zaneprázdněném přízi clusteru se zobrazí chyba funkce zatmění")

## <a name="see-also"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití Azure Toolkit for IntelliJ k vytváření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím sítě VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)