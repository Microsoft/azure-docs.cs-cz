---
title: Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight
description: Pro sledování a ladění úloh spuštěných v clusteru Spark v Azure HDInsight použijte uživatelské rozhraní, rozhraní Spark a server historie Sparku.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 0e80aa44652efbc58f8259944058aabe59ca5d1a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338467"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight

V tomto článku se dozvíte, jak sledovat a ladit [Apache Spark](https://spark.apache.org/) úlohy spuštěné v clusterech HDInsight pomocí uživatelského rozhraní [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , uživatelského rozhraní Spark a serveru pro historii Spark. Úlohu Sparku spustíte pomocí poznámkového bloku, který je k dispozici v clusteru Spark, **Machine Learning: Prediktivní analýza dat kontroly potravin pomocí MLLib**. Pomocí následujících kroků můžete sledovat aplikaci, kterou jste odeslali pomocí jakéhokoli jiného přístupu, například **Spark-Submit**.

## <a name="prerequisites"></a>Požadavky

Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Měli byste začít spustit Poznámkový blok **, [Machine Learning: Prediktivní analýza dat kontroly potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md).** Pokyny, jak spustit tento poznámkový blok, získáte pomocí odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v uživatelském rozhraní PŘÍZe

1. Spusťte uživatelské rozhraní PŘÍZe. Klikněte na položku **příze** v části **řídicí panely clusteru**.

    ![Azure Portal spustit rozhraní PŘÍZe](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternativně můžete také spustit rozhraní PŘÍZe z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, klikněte na **Ambari domů** v části **řídicí panely clusteru**. V uživatelském rozhraní Ambari klikněte na **příze**, klikněte na **Rychlé odkazy**, klikněte na aktivní Správce prostředků a potom klikněte na **Správce prostředků uživatelské rozhraní**.

2. Vzhledem k tomu, že jste spustili úlohu Spark pomocí poznámkových bloků Jupyter, aplikace má název **remotesparkmagics** (Toto je název pro všechny aplikace, které jsou spouštěny z poznámkových bloků). Další informace o úloze získáte kliknutím na ID aplikace v názvu aplikace. Tím se spustí zobrazení aplikace.

    ![Server historie Sparku najít ID aplikace Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    U takových aplikací, které se spouštějí z notebooků Jupyter, je stav vždycky **spuštěný** , dokud neukončíte Poznámkový blok.
3. V zobrazení aplikace můžete podrobněji přejít k části informace o kontejnerech přidružených k aplikaci a protokolech (stdout/stderr). Uživatelské rozhraní Spark můžete také spustit kliknutím na propojení odpovídající **adrese URL pro sledování**, jak je znázorněno níže.

    ![Server historie Spark stáhnout protokoly kontejneru](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v uživatelském rozhraní Spark

V uživatelském rozhraní Spark můžete přejít k podrobnostem úloh Spark, které vytváří aplikace, kterou jste spustili dříve.

1. Chcete-li spustit uživatelské rozhraní Spark, v zobrazení aplikace klikněte na odkaz na **adresu URL pro sledování**, jak je znázorněno na snímku obrazovky výše. Zobrazí se všechny úlohy Sparku spouštěné aplikací běžícími na poznámkovém bloku Jupyter.

    ![Karta úlohy serveru historie Sparku](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Klikněte na kartu **vykonavatelé** a zobrazte informace o zpracování a uložení pro každý prováděcí modul. Můžete také načíst zásobník volání kliknutím na odkaz **Výpis paměti vlákna** .

    ![Karta vykonavatelé serveru historie Spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Kliknutím na kartu **fáze** zobrazíte fáze spojené s aplikací.

    ![Karta fáze serveru historie Sparku]–(./media/apache-spark-job-debugging/view-apache-spark-stages.png "zobrazení fází Sparku")

    Každá fáze může mít několik úloh, pro které můžete zobrazit statistiku spuštění, jak je znázorněno níže.

    ![Podrobnosti karty fáze serveru s historií Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Zobrazit podrobnosti o fázích Sparku")

4. Na stránce Podrobnosti fáze můžete spustit vizualizaci DAG. Rozbalte odkaz **vizualizace DAG** v horní části stránky, jak je znázorněno níže.

    ![Zobrazit vizualizaci DAG ve fázích Sparku](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG nebo Direct Aclyic Graph představuje různé fáze aplikace. Každé modré pole v grafu představuje operaci Spark vyvolanou z aplikace.

5. Na stránce Podrobnosti fáze můžete také spustit zobrazení Časová osa aplikace. Rozbalte odkaz **Časová osa události** v horní části stránky, jak je znázorněno níže.

    ![Zobrazit časovou osu události fází Sparku](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Tím se zobrazí události Sparku ve formě časové osy. Zobrazení Časová osa je k dispozici na třech úrovních, napříč úlohami v rámci úlohy a v rámci fáze. Výše uvedený obrázek zachytí zobrazení časové osy pro danou fázi.

   > [!TIP]  
   > Pokud zaškrtnete políčko **Povolit zvětšení velikosti** , můžete v zobrazení Časová osa přejít doleva a doprava.

6. Další karty v uživatelském rozhraní Spark obsahují také užitečné informace o instanci Spark.

   * Karta úložiště – Pokud vaše aplikace vytvoří RDD, můžete najít informace o těchto možnostech na kartě úložiště.
   * Karta prostředí – Tato karta poskytuje spoustu užitečných informací o vaší instanci Spark, jako je:
     * Verze Scala
     * Adresář protokolu událostí přidružený ke clusteru
     * Počet jader prováděcích modulů pro aplikaci
     * Atd.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Vyhledání informací o dokončených úlohách pomocí serveru historie Spark

Po dokončení úlohy jsou informace o úloze trvalé na serveru historie Sparku.

1. Pokud chcete spustit historii serveru Spark, klikněte v okně Přehled na možnost **Server historie Spark** v části **řídicí panely clusteru**.

    ![Azure Portal spustit historii Spark serveru](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spustit historii Sparku Server1")

   > [!TIP]  
   > Alternativně můžete také spustit uživatelské rozhraní serveru historie Sparku z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, v okně Přehled klikněte na **řídicí panely clusteru** **Ambari domů** . V uživatelském rozhraní Ambari klikněte na **Spark**, klikněte na **Rychlé odkazy**a pak na **uživatelské rozhraní serveru historie Sparku**.

2. Zobrazí se všechny dokončené aplikace v seznamu. Kliknutím na ID aplikace přejdete k podrobnostem o aplikaci, kde najdete další informace.

    ![Dokončené aplikace serveru historie Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Spustit historii Spark Server2")

## <a name="see-also"></a>Viz také:

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Ladění úloh Apache Spark pomocí serveru rozšířených historií Sparku](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Pro analytiky dat

* [Apache Spark s Machine Learning: Použití Sparku ve službě HDInsight k analýze teploty budovy pomocí dat TVK](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insight pomocí Apache Spark ve službě HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Použití Caffe na Azure HDInsight Spark pro distribuované hloubkové učení](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Pro vývojáře Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
