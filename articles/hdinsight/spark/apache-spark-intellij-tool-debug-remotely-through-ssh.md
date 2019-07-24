---
title: 'Azure Toolkit for IntelliJ: Vzdálené ladění aplikací Spark přes SSH '
description: Podrobné pokyny k používání nástrojů HDInsight v Azure Toolkit for IntelliJ pro vzdálené ladění aplikací na clusterech HDInsight pomocí SSH
keywords: dálková ladění IntelliJ, vzdálené ladění IntelliJ, SSH, IntelliJ, HDInsight, ladění IntelliJ, ladění
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 0bca6c16124f886d9df9e88e651f3f7450f51a1a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876355"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Ladění Apache Spark aplikací místně nebo vzdáleně v clusteru HDInsight s Azure Toolkit for IntelliJ prostřednictvím SSH

Tento článek poskytuje podrobné pokyny, jak pomocí nástrojů služby HDInsight v [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) ladit aplikace vzdáleně v clusteru HDInsight. Chcete-li ladit projekt, můžete také zobrazit [ladění aplikací HDInsight Spark pomocí Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videa.

**Požadavky**
* **Nástroje HDInsight v Azure Toolkit for IntelliJ**. Tento nástroj je součástí Azure Toolkit for IntelliJ. Další informace najdete v tématu [instalace Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). A **Azure Toolkit for IntelliJ**. Pomocí této sady nástrojů můžete vytvářet aplikace Apache Spark pro cluster HDInsight. Pokud chcete získat další informace, postupujte podle pokynů v tématu [použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Služba HDInsight SSH se správou uživatelského jména a hesla**. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [používání tunelového propojení SSH pro přístup k webovému uživatelskému rozhraní Ambari, JobHistory, NameNode, Apache Oozie a dalším webovým uživatelská rozhraní](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Naučte se provádět místní spouštění a ladění.
### <a name="scenario-1-create-a-spark-scala-application"></a>Scénář 1: Vytvoření aplikace Spark Scala 

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky:

   a. Vyberte **Azure Spark/HDInsight**. 

   b. Vyberte šablonu Java nebo Scala podle vaší předvolby. Vyberte mezi následujícími možnostmi:

   - **Projekt Spark (Java)**

   - **Projekt Spark (Scala)**

   - **Projekt Spark s ukázkami (Scala)**

   - **Projekt Spark s chybovou úlohou ladění ukázek (Preview) (Scala)**

     V tomto příkladu se používá šablona **projektu Spark s ukázkami (Scala)** .

   c. V seznamu **Build tool** (Nástroj sestavení) vyberte podle svých potřeb některý z následujících:

   - **Maven** – pro podporu průvodce vytvořením projektu Scala

   - **SBT** – pro správu závislostí a vytváření pro projekt Scala 

     ![Vytvořit ladicí projekt](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Vyberte **Další**.     
 
1. V dalším **novém okně projektu** proveďte následující:

   ![Výběr sady Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V rozevíracím seznamu **sada SDK projektu** vyberte **Java 1,8** pro cluster **Spark 2. x** nebo vyberte **Java 1,7** pro cluster **Spark 1. x** .

   c. V rozevíracím seznamu **verze Sparku** se v Průvodci vytvořením projektu Scala integruje správná verze sady Spark SDK a sady Scala SDK. Pokud je verze clusteru Spark starší než 2,0, vyberte **Spark 1. x**. V opačném případě vyberte možnost **Spark 2. x.** V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)** .

   d. Vyberte **Finish** (Dokončit).

1. Vyberte **Src** > MainScala > a otevřete svůj kód v projektu. V tomto příkladu se používá skript **SparkCore_wasbloTest** .

### <a name="prerequisite-for-windows"></a>Předpoklad pro Windows
Když spouštíte místní aplikaci Spark Scala na počítači se systémem Windows, může se zobrazit výjimka, jak je vysvětleno v [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože ve Windows chybí WinUtils. exe. 

Chcete-li tuto chybu vyřešit, [Stáhněte spustitelný soubor](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako je například **C:\WinUtils\bin**. Pak přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné na **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scénář 2: Provést místní spuštění
1. Otevřete skript **SparkCore_wasbloTest** , klikněte pravým tlačítkem myši na editor skriptů a potom vyberte možnost **Spustit [Spark Job] XXX** a proveďte místní spuštění.
1. Až se místní spuštění dokončí, můžete si prohlédnout výstupní soubor uložit do aktuálního **__Nastavení__** **dat** > Průzkumníka projektu.

    ![Výsledek místního spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Naše nástroje automaticky nastavily výchozí konfiguraci místního spuštění, když provedete místní spuštění a místní ladění. Otevřete konfiguraci **[Spark v HDInsight] XXX** v pravém horním rohu, kde se zobrazí **[Spark v HDInsight] XXX** , která už je vytvořená v **Apache Spark v HDInsight**. Přepněte na **místně spouštěnou** kartu.

    ![Konfigurace místního spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Proměnné prostředí](#prerequisite-for-windows): Pokud jste již nastavili proměnnou prostředí systému **HADOOP_HOME** na **C:\WinUtils**, může automaticky rozpoznat, že není nutné ručně přidávat.
    - [Umístění WinUtils. exe](#prerequisite-for-windows): Pokud jste nestavili proměnnou prostředí systému, můžete umístění najít kliknutím na jeho tlačítko.
    - Stačí zvolit jednu ze dvou možností a, které nejsou potřeba v MacOS a Linux.
1. Konfiguraci můžete také nastavit ručně před provedením místního běhu a místního ladění. Na předchozím snímku obrazovky vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** . Zadejte informace pro **název**, **název hlavní třídy** , který chcete uložit, a potom klikněte na tlačítko místní spuštění.

### <a name="scenario-3-perform-local-debugging"></a>Scénář 3: Provést místní ladění
1. Otevřete skript **SparkCore_wasbloTest** , nastavte zarážky.
1. Klikněte pravým tlačítkem na editor skriptů a potom vyberte možnost **ladit ' [Spark v HDInsight] xxx '** k provedení místního ladění.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Naučte se provádět vzdálené spuštění a ladění.
### <a name="scenario-1-perform-remote-run"></a>Scénář 1: Provést vzdálené spuštění

1. Pokud chcete získat přístup k nabídce **Upravit konfigurace** , vyberte ikonu v pravém horním rohu. Z této nabídky můžete vytvořit nebo upravit konfigurace pro vzdálené ladění.

   ![Upravit konfigurace](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. V dialogovém okně **Spustit/ladit konfigurace** vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** .

   ![Přidat novou konfiguraci](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Přepněte na **vzdálené spuštění na kartě cluster** . Zadejte informace pro **název**, **cluster Spark**a **název hlavní třídy**. Pak klikněte na možnost **Pokročilá konfigurace (vzdálené ladění)** . Naše nástroje podporují ladění pomocí **prováděcích**modulů. Výchozí hodnota **numExectors**je 5. Lépe jste nestavili hodnotu vyšší než 3.

   ![Spustit konfigurace ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. V části **Pokročilá konfigurace (vzdálené ladění)** vyberte možnost **Povolit vzdálené ladění Spark**. Zadejte uživatelské jméno SSH a pak zadejte heslo nebo použijte soubor privátního klíče. Chcete-li provést vzdálené ladění, je nutné jej nastavit. Není nutné ji nastavovat, pokud chcete pouze použít vzdálené spuštění.

   ![Povolit vzdálené ladění Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **Upravit konfigurace**. 

1. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru nebo provést vzdálené ladění.
   
   ![Tlačítko pro vzdálené spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Klikněte na tlačítko **Odpojit** , které se v levém panelu nezobrazí protokoly odeslání. Pořád ale běží na back-endu.

   ![Tlačítko pro vzdálené spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scénář 2: Provést vzdálené ladění
1. Nastavte body přerušení a pak klikněte na ikonu **vzdáleného ladění** . Rozdíl se vzdáleným odesláním spočívá v tom, že je nutné nakonfigurovat uživatelské jméno/heslo SSH.

   ![Výběr ikony ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Když program dosáhne bodu přerušení, zobrazí se karta **ovladače** a dvě karty prováděcího modulu  v podokně ladicího **programu** . Vyberte ikonu **pokračovat v programu** pro pokračování v běhu kódu, který pak dosáhne další zarážky. Pro vyhledání cílového prováděcího modulu pro  ladění musíte přejít na správnou kartu vykonavatele. Protokoly spouštění můžete zobrazit na příslušné kartě **konzoly** .

   ![Karta ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénář 3: Provést vzdálené ladění a opravy chyb
1. Nastavte dva body přerušení a potom výběrem ikony **ladění** spusťte proces vzdáleného ladění.

1. Kód se zastaví v prvním bodu přerušení a informace o parametru a proměnné se zobrazí v podokně **proměnné** . 

1. Pokračujte výběrem ikony **pokračovat v programu** . Kód se zastaví v druhém bodě. Výjimka je zachycena podle očekávání.

   ![Chyba vyvolání](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Znovu vyberte ikonu znovu **Spustit program** . V okně pro **odeslání Sparku HDInsight** se zobrazí chyba "spuštění úlohy se nezdařilo.

   ![Odeslání chyby](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Chcete-li dynamicky aktualizovat hodnotu proměnné pomocí možnosti ladění IntelliJ, vyberte znovu možnost **ladit** . Podokno **proměnné** se znovu zobrazí. 

1. Klikněte pravým tlačítkem na cíl na kartě **ladění** a pak vyberte **nastavit hodnotu**. Potom zadejte novou hodnotu proměnné. Pak vyberte **zadat** a uložte hodnotu. 

   ![Nastavit hodnotu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Vyberte ikonu **pokračovat v programu** a pokračujte v spouštění programu. Tentokrát není zachycena žádná výjimka. Můžete vidět, že projekt se úspěšně spouští bez jakýchkoli výjimek.

   ![Ladit bez výjimky](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Další kroky
* [Přehled Apache Spark ve službě Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvořit projekt Scala (video): [Vytváření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Sparku ve službě HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k analýze teploty budovy pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
