---
title: 'Azure Toolkit pro IntelliJ: Ladění aplikací Spark pomocí SSH - HDInsight'
description: Podrobné pokyny, jak používat nástroje HDInsight v sadě Nástrojů Azure pro IntelliJ k vzdálenému ladění aplikací v clusterech HDInsight prostřednictvím SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: a012c3ce8f7c9e105a42d8383a502f3608c84070
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732910"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Ladění aplikací Apache Spark v clusteru HDInsight pomocí Azure Toolkit pro IntelliJ až SSH

Tento článek obsahuje podrobné pokyny, jak používat nástroje HDInsight v [sadě nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) k vzdálenému ladění aplikací v clusteru HDInsight. Chcete-li ladit svůj projekt, můžete také zobrazit [ladicí HDInsight Spark aplikace s Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Pro uživatele Windows: Když používáte místní aplikaci Spark Scala v počítači se systémem Windows, může se vám výjimka, jak je vysvětleno v [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože v systému Windows chybí program WinUtils.exe.

    Chcete-li tuto chybu vyřešit, stáhněte soubor [Winutils.exe](https://github.com/steveloughran/winutils) do umístění, například **C:\WinUtils\bin**. Potom přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné na **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Vydání Společenství je zdarma.).

* [Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Scala plugin pro IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Vytvoření aplikace Spark Scala

1. Spusťte IntelliJ IDEA a vyberte **Vytvořit nový projekt,** chcete-li otevřít okno **Nový projekt.**

1. V levém podokně vyberte **Apache Spark/HDInsight.**

1. V hlavním okně vyberte **Spark Project se vzorky (Scala).**

1. V rozevíracím seznamu **Nástrojem sestavení** vyberte jednu z následujících možností:

    * **Podpora** průvodce vytvořením projektu Scala.
    * **SBT** pro správu závislostí a vytváření pro projekt Scala.

     ![Intellij Vytvořit novou jiskru projektu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Vyberte **Další**.

1. V dalším okně **Nový projekt** zadejte následující informace:

    |Vlastnost |Popis |
    |---|---|
    |Název projektu|Zadejte název. Tato procházka `myApp`používá .|
    |Umístění projektu|Zadejte požadované umístění pro uložení projektu.|
    |Sada SDK projektu|Pokud je prázdné, vyberte **Nový...** a přejděte do jdk.|
    |Verze Spark|Průvodce vytvořením integruje správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Možnost Jiskra 2.x.**. Tento příklad používá **Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij Nový projekt vybrat verzi Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Vyberte **Finish** (Dokončit). Může trvat několik minut, než bude projekt k dispozici. Podívejte se na pravý dolní roh pro pokrok.

1. Rozšiřte svůj projekt a přejděte na**ukázku** **src** > **main** > **scala** > . Poklepejte na **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Provést místní spuštění

1. Ve **SparkCore_WasbIOTest** skriptu klepněte pravým tlačítkem myši na editor skriptů a vyberte možnost **Spustit SparkCore_WasbIOTest** pro místní spuštění.

1. Po dokončení místního spuštění se zobrazí výstupní soubor uložený do**__aktuálního výchozího nastavení__** **dat** > průzkumníka projektu .

    ![Výsledek místního spuštění projektu Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Naše nástroje nastavily výchozí konfiguraci místního spuštění automaticky při provádění místního spuštění a místního ladění. Otevřete konfiguraci **[Spark na HDInsight] XXX** v pravém horním rohu, můžete vidět **[Spark na HDInsight]XXX** již vytvořené pod **Apache Spark na HDInsight**. Přepněte na kartu **Místní spuštění.**

    ![Intellij Spustit konfigurace ladění místní spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Proměnné prostředí](#prerequisites): Pokud již nastavíte proměnnou systémového prostředí **HADOOP_HOME** na **C:\WinUtils**, může automaticky zjistit, že není nutné ručně přidávat.
    - [WinUtils.exe Umístění](#prerequisites): Pokud jste nenastavili proměnnou prostředí systému, můžete najít umístění klepnutím na jeho tlačítko.
    - Stačí si vybrat některou ze dvou možností a nejsou potřeba na MacOS a Linux.

1. Můžete také nastavit konfiguraci ručně před provedením místní ho spustit a místní ladění. Na předchozím snímku obrazovky vyberte**+** znaménko plus ( ). Pak vyberte **možnost Apache Spark na HDInsight.** Zadejte informace pro **název**, **Hlavní název třídy,** který chcete uložit, a klikněte na tlačítko místní spuštění.

## <a name="perform-local-debugging"></a>Provést místní ladění

1. Otevřete **skript SparkCore_wasbloTest,** nastavte zarážky.

1. Klikněte pravým tlačítkem myši na editor skriptů a vyberte možnost **Ladění '[Spark na HDInsight]XXX'** k provedení místního ladění.

## <a name="perform-remote-run"></a>Provedení vzdáleného spuštění

1. Přejděte na **Spouštět** > **konfigurace úprav...**. Z této nabídky můžete vytvořit nebo upravit konfigurace pro vzdálené ladění.

1. V dialogovém okně **Spustit/ladit konfigurace** vyberte**+** znaménko plus ( ). Pak vyberte **možnost Apache Spark na HDInsight.**

   ![Intellij Přidat novou konfiguraci](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Přepněte na kartu Vzdálené spuštění na kartě **Cluster.** Zadejte informace pro **název**, **cluster Spark**a název **hlavní třídy**. Potom klepněte na tlačítko **Upřesnit konfiguraci (vzdálené ladění).** Naše nástroje podporují ladění s **executory**. **NumExectors**, výchozí hodnota je 5. Raději nenastavíte vyšší než 3.

   ![Intellij Spustit konfigurace ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. V části **Rozšířená konfigurace (Vzdálené ladění)** vyberte **Povolit vzdálené ladění Spark**. Zadejte uživatelské jméno SSH a zadejte heslo nebo použijte soubor soukromého klíče. Pokud chcete provést vzdálené ladění, musíte jej nastavit. Není třeba ji nastavovat, pokud chcete používat pouze vzdálené spuštění.

   ![Pokročilá konfigurace Intellij umožňuje vzdálené ladění jiskry](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti konfigurace, vyberte název konfigurace. Chcete-li provést změny, vyberte **možnost Upravit konfigurace**.

1. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru nebo provést vzdálené ladění.

   ![Tlačítko spuštění vzdálené úlohy intellij Remote Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klepněte na tlačítko **Odpojit,** aby se protokoly odeslání nezobrazovaly v levém panelu. Však je stále běží na back-endu.

   ![Výsledek vzdáleného spuštění úlohy Intellij Pro vzdálené spuštění úlohy](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Provádět vzdálené ladění

1. Nastavte body přerušení a potom klikněte na ikonu **Vzdálené ladění.** Rozdíl s vzdálené podání je, že SSH uživatelské jméno / heslo je třeba nakonfigurovat.

   ![Ikona ladění úlohy vzdáleného sparku Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Když spuštění programu dosáhne bodu zlomu, zobrazí se karta **Ovladač** a dvě karty **executoru** v podokně **ladicí program.** Chcete-li pokračovat ve spuštění kódu, který pak dosáhne další zarážky, vyberte ikonu Pokračovat v **programu.** Chcete-li najít cílového prováděcího modulu k ladění, je třeba přepnout na správnou kartu **Executor.** Protokoly spuštění můžete zobrazit na odpovídající kartě **konzoly.**

   ![Karta Ladění vzdálené houšti Intellij Remote Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Proveďte vzdálené ladění a opravu chyb

1. Nastavte dva body přerušení a pak vyberte ikonu **ladění** a spusťte proces vzdáleného ladění.

1. Kód se zastaví v prvním bodě zlomu a informace o parametrech a proměnných se zobrazí v podokně **Proměnné.**

1. Chcete-li pokračovat, vyberte ikonu Pokračovat v **programu.** Kód se zastaví v druhém bodě. Výjimka je zachycena podle očekávání.

   ![Chyba vyvolání úlohy vzdálené úlohy ladění intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Znovu vyberte ikonu **Pokračovat v programu.** V okně **odeslání hdinsight spark** se zobrazí chyba "spuštění úlohy se nezdařilo".

   ![Odeslání chyby úlohy vzdálené jiskry Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Chcete-li dynamicky aktualizovat hodnotu proměnné pomocí funkce ladění IntelliJ, vyberte **znovu ladění.** Podokno **Proměnné** se znovu zobrazí.

1. Klikněte pravým tlačítkem myši na cíl na kartě **Ladění** a potom vyberte **Nastavit hodnotu**. Dále zadejte novou hodnotu proměnné. Pak vyberte **Enter,** chcete-li hodnotu uložit.

   ![Hodnota sady úlohy vzdálené jiskry Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Chcete-li pokračovat v jeho spuštění, vyberte ikonu Pokračovat v **programu.** Tentokrát není zachycena žádná výjimka. Můžete vidět, že projekt běží úspěšně bez jakýchkoli výjimek.

   ![Úloha vzdálené jiskry intellij bez výjimky](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka

* Vytvoření projektu Scala (video): [Vytvoření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Pomocí sady Azure Toolkit pro IntelliJ můžete vzdáleně ladit aplikace Apache Spark v clusteru HDInsight.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu s nástroji BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Pomocí Spark v HDInsightu můžete analyzovat teplotu budov pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití sady Nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě nástrojů Azure pro Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
