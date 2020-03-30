---
title: Úloha Ladění Spark pomocí sady Nástrojů IntelliJ Azure (preview) - HDInsight
description: Pokyny pomocí nástrojů HDInsight v sadě nástrojů Azure pro IntelliJ k ladění aplikací
keywords: ladění vzdáleně intellij, vzdálené ladění intellij, ssh, intellij, hdinsight, ladění intellij, ladění
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494601"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Ladění úlohy jiskry selhání pomocí sady Azure Toolkit pro IntelliJ (preview)

Tento článek obsahuje podrobné pokyny, jak používat nástroje HDInsight v [sadě Nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) ke spuštění aplikací ladění selhání **spark.**

## <a name="prerequisites"></a>Požadavky

* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Tento kurz používá java verzi 8.0.202.
  
* IntelliJ IDEA. Tento článek používá [IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit pro IntelliJ. Viz [Instalace sady nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Připojte se ke svému clusteru HDInsight. Viz [Připojení k clusteru HDInsight](apache-spark-intellij-tool-plugin.md).

* Průzkumník úložiště Microsoft Azure. Viz [Stažení Průzkumníka úložiště Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Vytvoření projektu s ladicí šablonou

Vytvořte projekt spark2.3.2 pokračovat v ladění selhání, převzít selhání úlohy ladění ukázkový soubor v tomto dokumentu.

1. Otevřete IntelliJ IDEA. Otevřete okno **Nový projekt.**

   a. V levém podokně vyberte **Azure Spark/HDInsight.**

   b. V hlavním okně vyberte **Projekt spark s laděním úlohy selhání(náhled) (Scala).**

     ![Intellij Vytvoření ladicího projektu](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Vyberte **další**.

2. V okně **Nový projekt** proveďte následující kroky:

   ![Intellij Nový projekt vybrat verzi Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V rozevíracím seznamu **sady Project SDK** vyberte **Java 1.8** pro cluster **Spark 2.3.2.**

   c. V rozevíracím seznamu **Verze Spark** vyberte **Spark 2.3.2 (Scala 2.11.8)**.

   d. Vyberte **Finish** (Dokončit).

3. Vyberte **src** > **main** > **scala,** chcete-li otevřít kód v projektu. Tento příklad používá skript **AgeMean_Div().**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spuštění aplikace Spark Scala/Java v clusteru HDInsight

Vytvořte aplikaci Scala/Java a spusťte aplikaci v clusteru Spark tak, že provedete následující kroky:

1. Kliknutím na **Přidat konfiguraci** otevřete okno **Konfigurace spuštění/ladění.**

   ![HDI Intellij Přidat konfiguraci](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. V dialogovém okně **Spustit/ladit konfigurace** vyberte**+** znaménko plus ( ). Pak vyberte **možnost Apache Spark na HDInsight.**

   ![Intellij Přidat novou konfiguraci](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Přepněte na kartu Vzdálené spuštění na kartě **Cluster.** Zadejte informace pro **název**, **cluster Spark**a název **hlavní třídy**. Naše nástroje podporují ladění s **executory**. **NumExectors**, výchozí hodnota je 5, a raději není nastavena vyšší než 3. Chcete-li zkrátit dobu běhu, můžete přidat **spark.yarn.maxAppAttempts** do **konfigurace úlohy** a nastavit hodnotu na 1. Chcete-li konfiguraci uložit, klepněte na tlačítko **OK.**

   ![Nové konfigurace ladění Intellij Spustit](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti konfigurace, vyberte název konfigurace. Chcete-li provést změny, vyberte **možnost Upravit konfigurace**.

5. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru.

   ![Tlačítko spuštění vzdálené úlohy intellij Remote Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. ID aplikace můžete zkontrolovat z výstupního okna.

   ![Výsledek vzdáleného spuštění úlohy Intellij Pro vzdálené spuštění úlohy](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Stáhnout neúspěšný profil úlohy

Pokud se odeslání úlohy nezdaří, můžete stáhnout neúspěšný profil úlohy do místního počítače pro další ladění.

1. Sem otevřete **Průzkumníka úložiště Microsoft Azure**, vyhledejte účet HDInsight clusteru pro neúspěšnou úlohu, stáhněte prostředky neúspěšné úlohy z odpovídajícího umístění: **\hdp\spark2-events\\.spark-failures\\\<ID aplikace>** do místní složky. Okno **aktivity** zobrazí průběh stahování.

   ![Selhání stahování Průzkumníka azure storage exploreru](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Úspěch stahování Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurace místního prostředí ladění a ladění při selhání

1. Otevřete původní projekt nebo vytvořte nový projekt a přidružte jej k původnímu zdrojovému kódu. Pro ladění selhání je aktuálně podporována pouze verze spark2.3.2.

1. V aplikaci IntelliJ IDEA vytvořte konfigurační soubor **ladění selhání jiskry** a vyberte soubor FTD z dříve stažených zdrojů úlohy, které selhaly, pro pole **umístění Kontext selhání úlohy Spark.**

   ![konfigurace selhání kréty](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klepněte na tlačítko místní spuštění v panelu nástrojů, chyba se zobrazí v okně Spustit.

   ![run-selhání konfigurace1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![run-selhání-konfigurace2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Nastavte bod přerušení, jak naznačuje protokol, pak klikněte na tlačítko místní ladění dělat místní ladění stejně jako vaše normální Scala / Java projekty v IntelliJ.

1. Po ladění, pokud projekt úspěšně dokončí, můžete znovu odeslat neúspěšnou úlohu do clusteru HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Další kroky

* [Přehled: Ladění aplikací Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Ukázka

* Vytvoření projektu Scala (video): [Vytvoření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Pomocí sady Azure Toolkit pro IntelliJ můžete vzdáleně ladit aplikace Apache Spark v clusteru HDInsight.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Proveďte interaktivní analýzu dat pomocí Spark v HDInsightu s nástroji BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Pomocí Spark v HDInsightu můžete analyzovat teplotu budov pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití sady Nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím sítě VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů HDInsight pro IntelliJ s sandboxem Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě nástrojů Azure pro Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
