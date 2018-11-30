---
title: Ladění Apache Spark úloh spuštěných v Azure HDInsight
description: Použití uživatelského rozhraní YARN, Spark uživatelského rozhraní a server historie Sparku ke sledování a ladění úloh spuštěných v clusteru Spark v Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: hrasheed
ms.openlocfilehash: 6afb54caca572988c566ab7c6325d511e77fbd3e
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582084"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění Apache Spark úloh spuštěných v Azure HDInsight

V tomto článku se dozvíte, jak ke sledování a ladění [Apache Spark](https://spark.apache.org/) úloh spuštěných na HDInsight clusterů pomocí [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) uživatelského rozhraní, Spark uživatelského rozhraní a Server historie Sparku. Spustit úlohu Spark pomocí poznámkového bloku, které jsou dostupné s clusterem Spark **Machine learning: prediktivní analýzy na data kontroly potravin pomocí MLLib**. Následující postup můžete použít ke sledování aplikace, které jste odeslali používáte jiný přístup, například **skriptu spark-submit**.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Měli jste spustili spuštění poznámkového bloku,  **[Machine learning: prediktivní analýzy na data kontroly potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Pokyny, jak spustit tento poznámkový blok postupujte podle odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v Uživatelském rozhraní YARN
1. Spuštění uživatelského rozhraní YARN. Klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **YARN**.
   
    ![Spuštění uživatelského rozhraní YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Alternativně můžete také spustit z uživatelského rozhraní Ambari Uživatelském rozhraní YARN. Chcete-li spustit uživatelské rozhraní Ambari, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **řídicí panel clusteru HDInsight**. Z uživatelského rozhraní Ambari, klikněte na tlačítko **YARN**, klikněte na tlačítko **rychlé odkazy**, klikněte na aktivní Resource Manageru a pak klikněte na **uživatelského rozhraní správce prostředků**.    
   > 
   > 
2. Protože jste spustili úlohu Spark pomocí poznámkových bloků Jupyter, aplikace má název **remotesparkmagics** (jde o název pro všechny aplikace, které jsou spuštěny z poznámkových bloků). Klikněte na ID aplikace proti název aplikace, chcete-li získat další informace o úloze. Otevře se zobrazení aplikace.
   
    ![Vyhledání ID aplikací Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Pro takové aplikace, které jsou spouštěny z poznámkových bloků Jupyter, stav je vždy **systémem** dokud neukončíte poznámkového bloku.
3. Ze zobrazení aplikací můžete procházet hierarchii dále najdete kontejnerů přidružených k aplikaci a protokoly (stdout/stderr). Rozhraní Spark můžete také spustit kliknutím propojení odpovídá **sledování adresy URL**, jak je znázorněno níže. 
   
    ![Stáhnout protokoly kontejneru](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v Uživatelském rozhraní Spark
V uživatelském rozhraní Spark můžete přejít ke Sparkových úloh, které jsou vyvolaných pomocí aplikace, kterou jste dříve spustili.

1. Spuštění rozhraní Spark ze zobrazení aplikací, klikněte na odkaz proti **sledování adresy URL**, jak je znázorněno na snímku obrazovky výše. Zobrazí se všechny Sparkových úloh, které se spustí aplikace běžící v poznámkovém bloku Jupyter.
   
    ![Zobrazení Sparkových úloh](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Klikněte na tlačítko **prováděcí moduly** kartu zobrazíte informace o zpracování a úložiště pro každý prováděcího modulu. Můžete také načíst zásobníku volání po kliknutí na **vlákna vypsat** odkaz.
   
    ![Prováděcí moduly zobrazení Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klikněte na tlačítko **fáze** kartu pro zobrazení v jednotlivých fázích přidružené k aplikaci.
   
    ![Zobrazit Spark fáze](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Každá fáze může mít více úloh, pro které se zobrazí spuštění statistiky, jako je třeba je uvedené níž.
   
    ![Zobrazit Spark fáze](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Na stránce podrobností fázi můžete spustit DAG vizualizace. Rozbalte **DAG vizualizace** odkaz v horní části stránky, jak je znázorněno níže.
   
    ![Zobrazit vizualizaci fáze DAG Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG nebo přímé Aclyic grafu představuje různé fáze v aplikaci. Každé pole modrá v grafu představuje Spark operace vyvolané z aplikace.
5. Na stránce podrobností fáze můžete také spustit časová osa zobrazení aplikace. Rozbalte **časová osa událostí** odkaz v horní části stránky, jak je znázorněno níže.
   
    ![Zobrazit časovou osu Spark fáze události](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Tato funkce zobrazí události Spark ve formě časové osy. Zobrazení časové osy je k dispozici ve třech úrovních mezi úlohami v rámci úlohy a v rámci fáze. Na obrázku výše jsou zaznamenané zobrazení časové osy pro danou fázi.
   
   > [!TIP]
   > Pokud vyberete **zapnout zvětšování** zaškrtávací políčko, mohou posunete doleva a doprava napříč zobrazení časové osy.
   > 
   > 
6. Ostatní karty v Uživatelském rozhraní Spark poskytují užitečné informace o instanci Spark také.
   
   * Karta úložiště – Pokud vaše aplikace vytvoří Rdd, najdete informace o těch na kartě úložiště.
   * Karta prostředí – tato karta obsahuje velké množství užitečných informací o vaší instanci prostředí Spark, jako 
     * Verze Scala
     * Adresář protokolu události přidružené ke clusteru
     * Počet jader prováděcí modul pro aplikaci
     * Atd.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Najít informace o dokončené úlohy pomocí Server historie Sparku
Po dokončení úlohy se ukládají informace o úloze v Server historie Sparku.

1. Spusťte Server historie Sparku, v okně clusteru klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **Server historie Sparku**.
   
    ![Spusťte Server historie Sparku](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Alternativně můžete také spustit uživatelské rozhraní konektoru Spark historie serveru z uživatelského rozhraní Ambari. Chcete-li spustit uživatelské rozhraní Ambari, v okně clusteru klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na tlačítko **řídicí panel clusteru HDInsight**. Z uživatelského rozhraní Ambari, klikněte na tlačítko **Spark**, klikněte na tlačítko **rychlé odkazy**a potom klikněte na tlačítko **rozhraní Server historie Sparku**.
   > 
   > 
2. Zobrazí všechny dokončené aplikace uvedené. Klikněte na ID aplikace a přejít k podrobnostem do aplikace pro další informace.
   
    ![Spusťte Server historie Sparku](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Další informace najdete v tématech
*  [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
*  [Ladění Sparkových úloh Apache pomocí rozšířený Server historie Sparku](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Pro datové analytiky

* [Apache Spark s Machine Learning: používejte Spark v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: používejte Spark v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight analýza dat telemetrie používat Apache Spark v HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Použití Caffe pro distribuované obsáhlého learningu v Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Pro vývojáře Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)


