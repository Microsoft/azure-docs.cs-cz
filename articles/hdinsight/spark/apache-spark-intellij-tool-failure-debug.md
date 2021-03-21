---
title: Ladění úlohy Spark pomocí sady IntelliJ Azure Toolkit (Preview) – HDInsight
description: Doprovodné materiály k ladění aplikací pomocí nástrojů HDInsight v Azure Toolkit for IntelliJ
keywords: dálková ladění IntelliJ, vzdálené ladění IntelliJ, SSH, IntelliJ, HDInsight, ladění IntelliJ, ladění
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942543"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Selhání ladění úloh Sparku pomocí Azure Toolkit for IntelliJ (Preview)

Tento článek poskytuje podrobné pokyny k používání nástrojů HDInsight v [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) ke spouštění aplikací pro **ladění chyb Spark** .

## <a name="prerequisites"></a>Předpoklady

* [Sada Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). V tomto kurzu se používá Java verze 8.0.202.
  
* IntelliJ nápad. Tento článek používá [INTELLIJ nápad Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Viz [instalace Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Připojte se ke clusteru HDInsight. Viz [připojení ke clusteru HDInsight](apache-spark-intellij-tool-plugin.md).

* Průzkumník služby Microsoft Azure Storage. Viz [stažení Průzkumník služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Vytvoření projektu pomocí šablony ladění

Vytvořte projekt Spark 2.3.2 pro pokračování v ladění selhání. v tomto dokumentu proveďte chybu ukázkového souboru ladění úlohy.

1. Otevřete IntelliJ IDEA. Otevřete okno **Nový projekt** .

   a. V levém podokně vyberte **Azure Spark/HDInsight** .

   b. V hlavním okně vyberte **projekt Spark s úlohou selhání ukázka ladění (Preview) (Scala)** .

     ![IntelliJ vytvořit projekt ladění](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Vyberte **Další**.

2. V okně **Nový projekt** proveďte následující kroky:

   ![IntelliJ nový projekt vybrat verzi Sparku](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Zadejte název projektu a umístění projektu.

   b. V rozevíracím seznamu **sada SDK projektu** vyberte **Java 1,8** pro cluster **Spark 2.3.2** .

   c. V rozevíracím seznamu **verze Sparku** vyberte **Spark 2.3.2 (Scala 2.11.8)**.

   d. Vyberte **Dokončit**.

3. Vyberte **Src**  >  **Main**  >  **Scala** a otevřete svůj kód v projektu. V tomto příkladu se používá skript **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Spuštění aplikace Spark Scala/Java v clusteru HDInsight

Vytvořte aplikaci Spark Scala/Java a spusťte aplikaci v clusteru Spark pomocí následujících kroků:

1. Kliknutím na **Přidat konfiguraci** otevřete okno **Konfigurace spuštění nebo ladění** .

   ![HDI IntelliJ Přidat konfiguraci](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. V dialogovém okně **Spustit/ladit konfigurace** vyberte znaménko plus ( **+** ). Pak vyberte možnost **Apache Spark na HDInsight** .

   ![Přidat novou konfiguraci IntelliJ](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Přepněte na **vzdálené spuštění na kartě cluster** . Zadejte informace pro **název**, **cluster Spark** a **název hlavní třídy**. Naše nástroje podporují ladění pomocí **prováděcích** modulů. Výchozí hodnota **numExectors** je 5 a Vy byste lépe nestavili hodnotu vyšší než 3. Chcete-li zkrátit dobu běhu, můžete do **Konfigurace úlohy** přidat **Spark. příze. maxAppAttempts** a nastavit hodnotu na 1. Kliknutím na tlačítko **OK** konfiguraci uložíte.

   ![IntelliJ spustit konfiguraci ladění – nové](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfigurace je nyní uložena s názvem, který jste zadali. Chcete-li zobrazit podrobnosti o konfiguraci, vyberte název konfigurace. Chcete-li provést změny, vyberte **Upravit konfigurace**.

5. Po dokončení nastavení konfigurace můžete spustit projekt proti vzdálenému clusteru.

   ![Tlačítko pro vzdálenou běh úlohy IntelliJ ladění vzdáleného Sparku](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. ID aplikace můžete ověřit v okně výstup.

   ![Výsledek vzdáleného spuštění úlohy IntelliJ ladění vzdáleného Sparku](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Neúspěšný profil úlohy stažení

Pokud se odeslání úlohy nepovede, můžete pro další ladění stáhnout profil neúspěšné úlohy do místního počítače.

1. Otevřete **Průzkumník služby Microsoft Azure Storage**, vyhledejte účet HDInsight clusteru pro neúspěšnou úlohu, Stáhněte neúspěšné prostředky úlohy z odpovídajícího umístění: **\hdp\spark2-Events \\ . Spark – selhání \\ \<application ID>** do místní složky. V okně **aktivity** se zobrazí průběh stahování.

   ![Průzkumník služby Azure Storage Chyba stahování](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Průzkumník služby Azure Storage stahování bylo úspěšné.](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurace místního ladicího prostředí a ladění při selhání

1. Otevřete původní projekt nebo vytvořte nový projekt a přidružte jej k původnímu zdrojovému kódu. Pro ladění chyb aktuálně podporuje pouze verzi Spark 2.3.2.

1. V IntelliJ NÁPADu vytvořte konfigurační soubor **ladění chyby Sparku** , vyberte soubor FTD z dříve stažených zdrojů neúspěšných úloh pro pole **umístění kontextu selhání úlohy Spark** .

   ![Konfigurace selhání Crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Na panelu nástrojů klikněte na tlačítko místní spuštění. chyba se zobrazí v okně Spustit.

   ![spuštění – chyba – configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![spuštění – chyba – configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Nastavte bod přerušení, který označuje protokol, a pak klikněte na tlačítko místní ladění a proveďte místní ladění stejně jako normální projekty Scala/Java v IntelliJ.

1. Po ladění je možné, že pokud se projekt úspěšně dokončí, můžete znovu odeslat neúspěšnou úlohu do svého clusteru Spark v HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Další kroky

* [Přehled: ladění aplikací Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

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
* [Používání nástrojů HDInsight pro IntelliJ s izolovaným prostorem Hortonworks](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](./apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)