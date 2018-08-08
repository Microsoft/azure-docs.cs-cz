---
title: 'Sada Azure Toolkit pro IntelliJ: aplikací Spark ladit vzdáleně pomocí SSH '
description: Podrobné pokyny o tom, jak pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ ladit aplikace vzdáleně na HDInsight clusterů přes SSH
keywords: vzdálené ladění intellij, vzdálené ladění intellij, ssh, intellij, hdinsight, ladění intellij, ladění
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 53094e3f8b489f972dc3e22441c66c8487a01fca
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622054"
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Ladění aplikací Spark místně nebo vzdáleně v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH

Tento článek obsahuje podrobné pokyny o tom, jak ladit aplikace vzdáleně v clusteru služby HDInsight pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ. Chcete-li ladit projekt, můžete také zobrazit [HDInsight Spark ladění aplikací pomocí sady Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videa.

**Požadavky**
* **Nástroje HDInsight v sadě Azure Toolkit pro IntelliJ**. Tento nástroj je součástí sady Azure Toolkit pro IntelliJ. Další informace najdete v tématu [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). A **sada Azure Toolkit pro IntelliJ**. Použijte tato sada nástrojů pro vytváření aplikací pro HDInsight cluster Spark. Další informace, postupujte podle pokynů v [použití sady Azure Toolkit pro IntelliJ umožňující vytváření aplikací pro HDInsight cluster Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH služby pomocí uživatelského jména a hesla správu**. Další informace najdete v tématu [připojení k HDInsight (Hadoop) pomocí protokolu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [použití SSH se tunelové propojení pro přístup k Ambari webové uživatelské rozhraní, JobHistory, NameNode, Oozie a dalších webových uživatelských rozhraní](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Zjistěte, jak provádět místní spuštění a ladění
### <a name="scenario-1-create-a-spark-scala-application"></a>Scénář 1: Vytvoření aplikací Spark Scala 

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky:

   a. Vyberte **HDInsight**. 

   b. Vyberte šablonu, Java nebo Scala podle dáváte přednost. Vyberte z následujících možností:

      - **Spark v HDInsight (Scala)**

      - **Spark v HDInsight (Java)**

      - **Spark v HDInsight vzorku (Scala)**

      Tento příklad používá **Spark v HDInsight vzorku (Scala)** šablony.

   c. V seznamu **Build tool** (Nástroj sestavení) vyberte podle svých potřeb některý z následujících:

      - **Maven** – pro podporu průvodce vytvořením projektu Scala

      -  **SBT** – pro správu závislostí a vytváření pro projekt Scala 

      ![Vytvořte projekt ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Vyberte **Další**.     
 
1. V dalším **nový projekt** okno, postupujte takto:

   ![Vyberte Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V **SDK projektu** rozevíracího seznamu vyberte **Java 1.8** pro **Spark 2.x** clusteru nebo vyberte **Java 1.7** pro **Spark 1.x**  clusteru.

   c. V **verzi Sparku** rozevíracího seznamu, v Průvodci vytvořením projektu Scala Spark SDK a sady SDK Scala integruje správnou verzi. Pokud je starší než 2.0 verze clusteru spark, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x.** V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.

   d. Vyberte **Finish** (Dokončit).

1. Vyberte **src** > **hlavní** > **scala** otevřete kód v projektu. V tomto příkladu **SparkCore_wasbloTest** skriptu.

### <a name="prerequisite-for-windows"></a>Předpokladem pro windows
Když používáte místní aplikace Spark Scala na počítači Windows, může se zobrazit výjimka, jak je vysvětleno v [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). K výjimce dochází, protože WinUtils.exe chybí na Windows. 

Chcete-li vyřešit tuto chybu [stažení spustitelného souboru](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako **C:\WinUtils\bin**. Pak přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné, která **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scénář 2: Provést místní spuštění
1. Otevřít **SparkCore_wasbloTest** skriptu, klikněte pravým tlačítkem na editor skriptů a potom vyberte možnost **spuštění "XXX [úloha Sparku]"** provádět místní spuštění.
1. Jednou místní spuštění dokončena, zobrazí se výstupní soubor uložte do aktuální project Exploreru **data** > **__výchozí__**.

    ![Místní spuštění výsledku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Naše nástroje nastavili výchozí místní konfigurace spuštění automaticky při provádění místní spuštění a místní ladění. Otevřete konfiguraci **[úloha Sparku] XXX** v pravém horním rohu se zobrazí **[úloha Sparku] XXX** již vytvořené v rámci **úlohy Azure HDInsight Spark**. Přepnout na **místně spustit** kartu.

    ![Konfigurace místního spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Proměnné prostředí](#prerequisite-for-windows): Pokud jste již nastavili proměnné prostředí systému **HADOOP_HOME** k **C:\WinUtils**, může automaticky zjistit, který není potřeba ručně přidat.
    - [Umístění WinUtils.exe](#prerequisite-for-windows): Pokud jste nenastavili systémové proměnné prostředí, můžete najít umístění kliknutím na jeho tlačítko.
    - Zvolte pouze jednu ze dvou možností a nejsou potřebné v systémech MacOS a Linux.
1. Můžete také nastavit konfiguraci ručně, než se pustíte do místní spuštění a místní ladění. Na předchozím snímku obrazovky, vyberte symbol plus (**+**). Vyberte **úlohy Azure HDInsight Spark** možnost. Zadejte informace pro **název**, **hlavní název třídy** uložit, klikněte na tlačítko pro místní spuštění.

### <a name="scenario-3-perform-local-debugging"></a>Scénář 3: Provedení místní ladění
1. Otevřít **SparkCore_wasbloTest** skriptu, nastavit zarážky.
1. Pravým tlačítkem myši na editor skriptů a potom vyberte možnost **ladění "XXX [úloha Sparku]"** provádět místní ladění.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Zjistěte, jak provést vzdálené spuštění a ladění
### <a name="scenario-1-perform-remote-run"></a>Scénář 1: Provést vzdálené spuštění

1. Přístup **upravit konfigurace** nabídku, vyberte ikonu v pravém horním rohu. V této nabídce můžete vytvořit nebo upravit konfigurace pro vzdálené ladění.

   ![Upravit konfigurace](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. V **konfigurace spuštění/ladění** dialogového okna, vyberte znaménko plus (**+**). Vyberte **úlohy Azure HDInsight Spark** možnost.

   ![Přidat novou konfiguraci](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Přepnout na **vzdáleně spouštět v clusteru** kartu. Zadejte informace pro **název**, **clusteru Spark**, a **hlavní název třídy**. Potom vyberte **pokročilou konfiguraci**. Naše nástroje podpory ladění s **prováděcí moduly**. **NumExectors**, výchozí hodnota je 5. Lepší není nastavíte vyšší než 3.

   ![Ladění konfigurace spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. V **Spark odeslání Advanced Configuration** dialogu **vzdálené ladění povolit Spark**. Zadejte uživatelské jméno SSH a pak zadejte heslo nebo použijte soubor privátního klíče. Chcete-li uložit konfiguraci, vyberte **OK**. Pokud chcete provádět vzdálené ladění, musíte ho nastavit. Není nutné ji nastavit, pokud chcete jenom používat vzdálené spuštění.

   ![Povolit vzdálené ladění Sparku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurace je nyní uložen s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **upravit konfigurace**. 

1. Po dokončení nastavení konfigurace, můžete projekt spustil s využitím vzdáleného clusteru nebo provádět vzdálené ladění.
   
   ![Tlačítko pro vzdálené spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Klikněte na tlačítko **odpojit** tlačítko, které protokoly odeslání nenacházely se v levém panelu. Ale je stále spuštěn na back-endu.

   ![Tlačítko pro vzdálené spuštění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scénář 2: Provádět vzdálené ladění
1. Nastavit body přerušení a klikněte **vzdálené ladění** ikonu. Rozdíl oproti vzdálené odeslání je, které vyžadují uživatelského jména a hesla SSH a nakonfigurovat.

   ![Vyberte ikonu ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Při spuštění programu dosáhne bodu zlomu, uvidíte **ovladač** kartu a dva **prováděcí modul** karty v **ladicí program** podokně. Vyberte **programu pokračovat** ikona bude moct být spuštěná kód, který pak dosáhne k další zarážce. Je potřeba přepnout na správnou **prováděcí modul** karta najít prováděcí modul cíl ladění. Můžete zobrazit protokoly spuštění u odpovídajícího **konzoly** kartu.

   ![Karta ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénář 3: Provedení vzdálené ladění a opravy chyb
1. Nastavit dva body přerušení a pak vyberte **ladění** ikona spustit vzdálený ladicí proces.

1. Kód se zastaví na prvním bodu zlomu a parametrů a proměnných informace jsou uvedeny v **proměnné** podokně. 

1. Vyberte **programu pokračovat** ikonu, abyste mohli pokračovat. Kód přestane v druhém místě. Výjimka je zachycena podle očekávání.

   ![Vyvolat chyby](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Vyberte **programu pokračovat** znovu na ikonu. **HDInsight Spark odeslání** v okně se zobrazí chyba "spuštění neúspěšné úlohy".

   ![Chyba odesílání](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Chcete-li dynamicky aktualizují hodnotu proměnné to s využitím IntelliJ funkci pro ladění, vyberte **ladění** znovu. **Proměnné** podokně se zobrazí znovu. 

1. Klikněte pravým tlačítkem na cíl na **ladění** kartu a potom vyberte **nastavit hodnotu**. Dále zadejte novou hodnotu pro proměnnou. Potom vyberte **Enter** uložte hodnotu. 

   ![Nastavit hodnotu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Vyberte **programu pokračovat** ikonu a pokračovat v běhu programu. Tentokrát je zachycena žádná výjimka. Uvidíte, že projekt úspěšně spustí bez jakékoli výjimky.

   ![Ladění bez výjimky](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvořit projekt Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně v clusteru služby HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes síť VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
