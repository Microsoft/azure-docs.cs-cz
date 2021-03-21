---
title: 'Azure Toolkit for IntelliJ: ladění aplikací Spark pomocí SSH-HDInsight'
description: Podrobné pokyny k používání nástrojů HDInsight v Azure Toolkit for IntelliJ pro vzdálené ladění aplikací na clusterech HDInsight pomocí SSH
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: ccd642578c8c35ac6b5f23397788ad1e7f83a1f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942606"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Ladění aplikací Apache Spark v clusteru HDInsight pomocí Azure Toolkit for IntelliJ prostřednictvím SSH

Tento článek poskytuje podrobné pokyny, jak pomocí nástrojů služby HDInsight v [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) ladit aplikace vzdáleně v clusteru HDInsight. Chcete-li ladit projekt, můžete také zobrazit [ladění aplikací HDInsight Spark pomocí Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videa.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Pro uživatele Windows: když na počítači s Windows běží místní aplikace Spark Scala, může se zobrazit výjimka, jak je vysvětleno v [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože ve Windows chybí WinUtils.exe.

    Chcete-li tuto chybu vyřešit, stáhněte [Winutils.exe](https://github.com/steveloughran/winutils) do umístění, jako je například **C:\WinUtils\bin**. Pak přidejte proměnnou prostředí **HADOOP_HOME** a nastavte hodnotu proměnné na **C:\WinUtils**.

* [INTELLIJ nápad](https://www.jetbrains.com/idea/download/#section=windows) (edice Community je zdarma).

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Modul plug-in Scala pro IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Vytvoření aplikace Spark Scala

1. Spusťte IntelliJ nápad a výběrem **vytvořit nový projekt** otevřete okno **Nový projekt** .

1. V levém podokně vyberte **Apache Spark/HDInsight** .

1. V hlavním okně vyberte **projekt Spark s ukázkami (Scala)** .

1. V rozevíracím seznamu **Nástroj sestavení** vyberte jednu z následujících možností:

    * **Maven** for Scala – Průvodce vytvořením projektu – podpora.
    * **SBT** pro správu závislostí a sestavení projektu Scala.

     ![IntelliJ vytvořit nový projekt Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Vyberte **Další**.

1. V dalším **novém okně projektu** zadejte následující informace:

    |Vlastnost |Popis |
    |---|---|
    |Název projektu|Zadejte název. Tento názorný postup vás provede použitím `myApp` .|
    |Umístění projektu|Zadejte požadované umístění pro uložení projektu.|
    |Projektový SDK|Pokud je toto pole prázdné, vyberte **Nový...** a přejděte k JDK.|
    |Verze Sparku|Průvodce vytvořením integruje správnou verzi sady Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte možnost **Spark 2. x.** V tomto příkladu se používá **Spark 2.3.0 (Scala 2.11.8)**.|

   ![IntelliJ nový projekt vybrat verzi Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Vyberte **Dokončit**. Může to trvat několik minut, než bude projekt k dispozici. Podívejte se do pravého dolního rohu a sledujte jeho průběh.

1. Rozbalte svůj projekt a přejděte k ukázce **Src**  >  **Main**  >  **Scala**  >  **Sample**. Dvakrát klikněte na **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Provést místní spuštění

1. Ve skriptu **SparkCore_WasbIOTest** klikněte pravým tlačítkem myši na editor skriptů a potom vyberte možnost **Spustit SparkCore_WasbIOTest** a proveďte místní spuštění.

1. Až se místní spuštění dokončí, můžete si prohlédnout výstupní soubor uložit do aktuálního nastavení **dat** Průzkumníka projektu  >  ****.

    ![Výsledek místního běhu projektu IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Naše nástroje automaticky nastavily výchozí konfiguraci místního spuštění, když provedete místní spuštění a místní ladění. Otevřete konfiguraci **[Spark v HDInsight] XXX** v pravém horním rohu, kde se zobrazí **[Spark v HDInsight] XXX** , která už je vytvořená v **Apache Spark v HDInsight**. Přepněte na **místně spouštěnou** kartu.

    ![IntelliJ spustit místní běh konfigurací ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Proměnné prostředí](#prerequisites): Pokud jste již nastavili proměnnou prostředí systému **HADOOP_HOME** na **C:\WinUtils**, může automaticky rozpoznat, že není nutné ručně přidávat.
    - [WinUtils.exe umístění](#prerequisites): Pokud jste nestavili proměnnou prostředí systému, můžete umístění najít kliknutím na jeho tlačítko.
    - Stačí zvolit jednu ze dvou možností a, které nejsou potřeba v MacOS a Linux.

1. Konfiguraci můžete také nastavit ručně před provedením místního běhu a místního ladění. Na předchozím snímku obrazovky vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** . Zadejte informace pro **název**, **název hlavní třídy** , který chcete uložit, a potom klikněte na tlačítko místní spuštění.

## <a name="perform-local-debugging"></a>Provést místní ladění

1. Otevřete skript **SparkCore_wasbloTest** , nastavte zarážky.

1. Klikněte pravým tlačítkem na editor skriptů a potom vyberte možnost **ladit ' [Spark v HDInsight] xxx '** k provedení místního ladění.

## <a name="perform-remote-run"></a>Provést vzdálené spuštění

1. Přejít na **Spusťte**  >  **úpravu konfigurací...** Z této nabídky můžete vytvořit nebo upravit konfigurace pro vzdálené ladění.

1. V dialogovém okně **Spustit/ladit konfigurace** vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** .

   ![Přidat novou konfiguraci IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Přepněte na **vzdálené spuštění na kartě cluster** . Zadejte informace pro **název**, **cluster Spark** a **název hlavní třídy**. Pak klikněte na možnost **Pokročilá konfigurace (vzdálené ladění)**. Naše nástroje podporují ladění pomocí **prováděcích** modulů. Výchozí hodnota **numExectors** je 5. Lépe jste nestavili hodnotu vyšší než 3.

   ![IntelliJ spustit konfigurace ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. V části **Pokročilá konfigurace (vzdálené ladění)** vyberte možnost **Povolit vzdálené ladění Spark**. Zadejte uživatelské jméno SSH a pak zadejte heslo nebo použijte soubor privátního klíče. Chcete-li provést vzdálené ladění, je nutné jej nastavit. Není nutné ji nastavovat, pokud chcete pouze použít vzdálené spuštění.

   ![IntelliJ Pokročilá konfigurace povolení vzdáleného ladění Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **Upravit konfigurace**.

1. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru nebo provést vzdálené ladění.

   ![Tlačítko pro vzdálenou běh úlohy IntelliJ ladění vzdáleného Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klikněte na tlačítko **Odpojit** , které se v levém panelu nezobrazí protokoly odeslání. Pořád ale běží na back-endu.

   ![Výsledek vzdáleného spuštění úlohy IntelliJ ladění vzdáleného Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Provést vzdálené ladění

1. Nastavte body přerušení a pak klikněte na ikonu **vzdáleného ladění** . Rozdíl se vzdáleným odesláním spočívá v tom, že je nutné nakonfigurovat uživatelské jméno/heslo SSH.

   ![Ikona ladění vzdálené úlohy Sparku IntelliJ ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Když program dosáhne bodu přerušení, zobrazí se karta **ovladače** a dvě karty **prováděcího** modulu v podokně **ladicího programu** . Vyberte ikonu **pokračovat v programu** pro pokračování v běhu kódu, který pak dosáhne další zarážky. Pro vyhledání cílového prováděcího modulu pro ladění musíte přejít na správnou kartu **vykonavatele** . Protokoly spouštění můžete zobrazit na příslušné kartě **konzoly** .

   ![Karta ladění vzdálené úlohy Spark IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Provést vzdálené ladění a opravy chyb

1. Nastavte dva body přerušení a potom výběrem ikony **ladění** spusťte proces vzdáleného ladění.

1. Kód se zastaví v prvním bodu přerušení a informace o parametru a proměnné se zobrazí v podokně **proměnné** .

1. Pokračujte výběrem ikony **pokračovat v programu** . Kód se zastaví v druhém bodě. Výjimka je zachycena podle očekávání.

   ![Vzdálená úloha Spark IntelliJ ladění – chyba vyvolání](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Znovu vyberte ikonu znovu **Spustit program** . V okně pro **odeslání Sparku HDInsight** se zobrazí chyba "spuštění úlohy se nezdařilo.

   ![Odeslání chyby vzdálené úlohy Sparku IntelliJ ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Chcete-li dynamicky aktualizovat hodnotu proměnné pomocí možnosti ladění IntelliJ, vyberte znovu možnost **ladit** . Podokno **proměnné** se znovu zobrazí.

1. Klikněte pravým tlačítkem na cíl na kartě **ladění** a pak vyberte **nastavit hodnotu**. Potom zadejte novou hodnotu proměnné. Pak vyberte **zadat** a uložte hodnotu.

   ![Hodnota sady úloh IntelliJ ladění vzdálené Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Vyberte ikonu **pokračovat v programu** a pokračujte v spouštění programu. Tentokrát není zachycena žádná výjimka. Můžete vidět, že projekt se úspěšně spouští bez jakýchkoli výjimek.

   ![Vzdálená úloha Sparku pro IntelliJ ladění bez výjimky](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka

* Vytvořit projekt Scala (video): [vytváření Apache Sparkch Scala aplikací](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku ve službě HDInsight s nástroji BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](./apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](./apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)