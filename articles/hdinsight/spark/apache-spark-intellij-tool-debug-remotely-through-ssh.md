---
title: 'Azure nástrojů pro IntelliJ: ladění Spark aplikací vzdáleně přes SSH | Microsoft Docs'
description: Podrobné pokyny o tom, jak používat nástroje HDInsight v Azure nástrojů pro IntelliJ k ladění aplikací vzdáleně na HDInsight clustery prostřednictvím SSH
keywords: vzdálené ladění intellij, vzdálené ladění intellij, ssh, intellij, hdinsight, ladění intellij, ladění
services: hdinsight
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: ''
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: ad0b1bbfc74f992a646ac375583f3399243873f5
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Ladění aplikací Spark místně nebo vzdáleně v clusteru s Azure nástrojů HDInsight pro IntelliJ prostřednictvím SSH

Tento článek obsahuje podrobné pokyny o tom, jak používat nástroje HDInsight v Azure nástrojů pro IntelliJ k ladění aplikací vzdáleně v clusteru HDInsight. K ladění projektu, můžete také zobrazit [HDInsight Spark ladění aplikace s Azure Toolkit pro IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) videa.

**Požadavky**
* **Nástroje HDInsight v Azure nástrojů pro IntelliJ**. Tento nástroj je součástí sady nástrojů Azure pro IntelliJ. Další informace najdete v tématu [instalaci nástrojů Azure pro IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). A **Azure nástrojů pro IntelliJ**. Tato sada nástrojů použijte k vytvoření aplikací Spark pro cluster služby HDInsight. Další informace, postupujte podle pokynů v [pomocí nástrojů Azure pro IntelliJ k vytvoření aplikací Spark pro cluster služby HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH služby pomocí uživatelského jména a hesla správu**. Další informace najdete v tématu [připojení k HDInsight (Hadoop) pomocí protokolu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) a [použití SSH tunelového propojení pro přístup k Ambari webové uživatelské rozhraní, JobHistory, NameNode, Oozie a jiné webové uživatelská](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Zjistěte, jak provádět místní spuštění a ladění
### <a name="scenario-1-create-a-spark-scala-application"></a>Scénář 1: Vytvoření aplikací Spark Scala 

1. Spusťte IntelliJ IDEA a pak vytvořte projekt. V **nový projekt** dialogové okno pole, postupujte takto:

   a. Vyberte **HDInsight**. 

   b. Vyberte Java nebo Scala šablony založené na vaši volbu. Vyberte jednu z těchto možností:

      - **Spark v HDInsight (Scala)**

      - **Spark v HDInsight (Java)**

      - **Spark v HDInsight ukázkové (Scala)**

      Tento příklad používá **Spark v HDInsight ukázkové (Scala)** šablony.

   c. V **nástroj pro sestavení** vyberte některý z následujících akcí podle potřeby vašeho:

      - **Maven**, podpora Průvodce vytvoření projektu Scala

      -  **SBT**, ke správě závislosti a vytváření projektu Scala 

      ![Vytvoření projektu ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Vyberte **Next** (Další).     
 
2. V dalším **nový projekt** okno, postupujte takto:

   ![Vyberte Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V **SDK projektu** rozevíracího seznamu vyberte **Java 1.8** pro **Spark 2.x** clusteru nebo vyberte **Java 1.7** pro **Spark 1.x**  clusteru.

   c. V **Spark verze** rozevíracího seznamu, Průvodce vytvořením projektu Scala integruje správná verze pro sadu SDK Spark a Scala SDK. Pokud verze clusteru spark je starší než 2.0, vyberte **Spark 1.x**. Jinak vyberte možnost **Spark 2.x.** Tento příklad používá **Spark bodu 2.0.2 (Scala 2.11.8)**.

   d. Vyberte **Finish** (Dokončit).

3. Vyberte **src** > **hlavní** > **scala** otevřete kódu v projektu. Tento příklad používá **SparkCore_wasbloTest** skriptu.

### <a name="prerequisite-for-windows"></a>Předpokladem pro windows
Když používáte místní aplikací Spark Scala na počítači se systémem Windows, může získat výjimku, jak je popsáno v [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Výjimka nastává, protože WinUtils.exe chybí v systému Windows. 

Chcete tuto chybu vyřešit [stáhnout spustitelný soubor](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako **C:\WinUtils\bin**. Pak přidejte proměnnou prostředí **HADOOP_HOME**a nastavte hodnotu proměnné na **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scénář 2: Provedení místním spuštění
1. Otevřete **SparkCore_wasbloTest** skriptu, klikněte pravým tlačítkem na editor skriptů a potom vyberte možnost **spustit '[úlohy Spark] XXX'** provést místní spuštění.
2. Spustit jednou místní dokončit, zobrazí se výstupní soubor uložte do vaší aktuální project Exploreru **data** > **__výchozí__**.

    ![Místní spuštění výsledku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Našich nástrojích nastavili výchozí místní spuštění konfigurace automaticky, když provádíte místní spuštění a místní ladění. Otevřete konfiguraci **XXX [úlohy Spark]** v pravém horním rohu, uvidíte **[úlohy Spark] XXX** již vytvořil v rámci **úlohu Azure HDInsight Spark**. Přepnout na **místně spustit** kartě.

    ![Místní spuštění konfigurace](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Proměnné prostředí](#prerequisite-for-windows): Pokud jste již nastavili proměnnou prostředí systému **HADOOP_HOME** k **C:\WinUtils**, může automaticky zjistit, který není potřeba ručně přidat.
    - [Umístění WinUtils.exe](#prerequisite-for-windows): Pokud jste nenastavili proměnné prostředí systému, můžete nějakého najít umístění klepnutím na její tlačítko.
    - Právě vyberte buď ze dvou možností, a nejsou potřebné v systému MacOS a Linux.
4. Můžete také nastavit konfiguraci ručně před provedením místní spuštění a místní ladění. Na předchozím snímku obrazovky vyberte znaménko plus (**+**). Vyberte **úlohu Azure HDInsight Spark** možnost. Zadejte informace pro **název**, **hlavní název třídy** uložit, pak klikněte na tlačítko místní spuštění.

### <a name="scenario-3-perform-local-debugging"></a>Scénář 3: Provedení místní ladění
1. Otevřete **SparkCore_wasbloTest** skriptu, nastavit zarážky.
2. Klikněte pravým tlačítkem na editor skriptů a potom vyberte možnost **ladění '[úlohy Spark] XXX'** provést místní ladění.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Zjistěte, jak provést vzdálené spuštění a ladění
### <a name="scenario-1-perform-remote-run"></a>Scénář 1: Provést vzdálené spuštění

1. Pro přístup k **upravit konfigurace** nabídce vyberte ikonu v pravém horním rohu. Z této nabídky můžete vytvořit nebo upravit konfiguraci pro vzdálené ladění.

   ![Upravit konfigurace](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. V **konfigurace spustit/Debug** dialogovém okně vyberte znaménko plus (**+**). Vyberte **úlohu Azure HDInsight Spark** možnost.

   ![Přidat novou konfiguraci](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Přepnout na **vzdáleně spouštět v clusteru** kartě. Zadejte informace pro **název**, **clusteru Spark**, a **hlavní název třídy**. Potom vyberte **pokročilou konfiguraci**. Podpora ladění pomocí našich nástrojích **vykonavatelů**. **NumExectors**, výchozí hodnota je 5. Lépe není nastavíte vyšší než 3.

   ![Spuštění konfigurace ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. V **Spark odeslání Upřesnit konfiguraci** dialogové okno, vyberte **Spark povolení vzdáleného ladění**. Zadejte uživatelské jméno SSH a pak zadejte heslo nebo použít soubor privátního klíče. Chcete-li uložit konfiguraci, vyberte **OK**. Pokud chcete provést vzdáleného ladění, budete muset nastavit. Není potřeba pro ni nastavit, pokud chcete použít vzdálené spuštění.

   ![Povolení vzdáleného ladění Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. Konfigurace je nyní uložit s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **upravit konfigurace**. 

6. Po dokončení nastavení konfigurace, můžete spustit projekt na vzdálený cluster nebo provést vzdálené ladění.
   
   ![Vzdálené spuštění tlačítko](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Klikněte **odpojení** tlačítko, které odeslání protokolů, které nejsou uvedené v levém panelu. Ale je stále spuštěna na back-end.

   ![Vzdálené spuštění tlačítko](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scénář 2: Provést vzdálené ladění
1. Nastavit body ukončování řádků a klikněte **vzdáleného ladění** ikonu. Rozdíl oproti vzdálené odesílání je které SSH uživatelské jméno nebo heslo je potřeba nakonfigurovat.

   ![Vyberte ikonu pro ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Při spuštění programu dosáhne bodem ukončování řádků, uvidíte **ovladač** kartě a dvě **vykonavatele** karty v **ladicí program** podokně. Vyberte **obnovit Program** ikonu dál spuštěný kód, který pak dosáhne další zarážky. Je nutné přepnout na správnou **vykonavatele** karta najít cílový vykonavatele k ladění. Můžete zobrazit protokoly spouštění na odpovídající **konzoly** kartě.

   ![Karta ladění](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénář 3: Provedení vzdálené ladění a opravy chyb
1. Nastavit dva body ukončování řádků a pak vyberte **ladění** ikonu pro spuštění vzdáleného ladění procesu.

2. Kód zastaví v prvním bodu ukončování řádků, a parametr a informace o proměnných jsou uvedeny v **proměnné** podokně. 

3. Vyberte **obnovit Program** ikonu pokračovat. Kód zastaví v druhém bodu. Výjimka je zachycena podle očekávání.

   ![Throw – chyba](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Vyberte **obnovit Program** ikonu znovu. **HDInsight Spark odeslání** okně se zobrazí chyba "Úloha spuštění se nepovedlo".

   ![Chyba odeslání](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Chcete-li dynamicky aktualizovat hodnotu proměnné pomocí IntelliJ ladění funkce, vyberte **ladění** znovu. **Proměnné** podokně se zobrazí znovu. 

6. Klikněte pravým tlačítkem na cíl na **ladění** a pak vyberte **nastavit hodnotu**. Potom zadejte novou hodnotu proměnné. Potom vyberte **Enter** uložte hodnotu. 

   ![Nastavit hodnotu](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Vyberte **obnovit Program** ikonu pokračujte ke spuštění programu. Tentokrát je žádná výjimka zachycena. Uvidíte, že projekt úspěšně běží bez jakékoli výjimky.

   ![Ladění bez výjimky](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvoření projektu Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [pomocí nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně v clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Spark v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí nástrojů Azure pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Používat nástroje HDInsight pro vytvoření aplikací Spark v Azure nástrojů pro Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
