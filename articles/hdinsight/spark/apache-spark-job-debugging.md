---
title: Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight
description: Použití ui, ui rozhraní Spark, ui spark a serveru Historie Spark ke sledování a ladění úloh spuštěných v clusteru Spark v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932141"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight

V tomto článku se dozvíte, jak sledovat a ladit úlohy [Apache Spark](https://spark.apache.org/) spuštěné na clusterech HDInsight pomocí ui [Apache Hadoop YARN,](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ui Spark a Spark History Server. Úlohu Spark zahájíte pomocí poznámkového bloku dostupného v clusteru Spark, **Strojové učení: Prediktivní analýza dat kontroly potravin pomocí MLLib**. Následující kroky můžete použít ke sledování aplikace, kterou jste odeslali pomocí jiného přístupu, například **spark-submit**.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Měli jste začít s notebookem **[Machine learning: Prediktivní analýza údajů o kontrole potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Pokyny ke spuštění tohoto poznámkového bloku najdete na odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v uzlovém řízení YARN

1. Spusťte ui. V části Řídicí **panely clusteru**vyberte **Přízi** .

    ![Spuštění ui portálu Azure](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Případně můžete také spustit UI YARN z ui Ambari. Chcete-li spustit ui Ambari, vyberte **ambari domů** v **řídicích panelech clusteru**. V uzdu Ambari přejděte na**rychlé odkazy** **yarn** > > aktivního správce prostředků > **ui správce prostředků**.

2. Vzhledem k tomu, že jste spustili úlohu Spark pomocí poznámkových bloků Jupyter, aplikace má název **remotesparkmagics** (to je název pro všechny aplikace, které jsou spuštěny z poznámkových bloků). Vyberte ID aplikace proti názvu aplikace, abyste získali další informace o úloze. Tím se spustí zobrazení aplikace.

    ![Spark historie server Najít Spark ID aplikace](./media/apache-spark-job-debugging/find-application-id1.png)

    Pro takové aplikace, které jsou spuštěny z poznámkových bloků Jupyter, stav je vždy **spuštěn,** dokud neukončíte poznámkový blok.

3. V zobrazení aplikace můžete přejít k podrobnostem dále zjistit kontejnery přidružené k aplikaci a protokoly (stdout/stderr). Ui Spark můžete spustit také kliknutím na odkaz odpovídající **měřicí adrese URL**, jak je znázorněno níže.

    ![Protokoly kontejnerů pro stahování kontejnerů serveru historie jiskry](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v uzlech Spark

V uzdviže ní můžete přejít k podrobnostem úloh Spark, které jsou zplozené aplikací, kterou jste spustili dříve.

1. Chcete-li spustit uzdu Spark, vyberte ze zobrazení aplikace odkaz na **měřicí adresu URL**, jak je znázorněno na obrazovce. Můžete vidět všechny úlohy Spark, které jsou spuštěny aplikací spuštěnou v poznámkovém bloku Jupyter.

    ![Karta Úlohy serveru historie jiskřitek](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Výběrem karty **Executors** zobrazíte informace o zpracování a úložišti pro každého vykonavatele. Zásobník volání můžete také načíst výběrem odkazu **Výpis vláken.**

    ![Karta Vykonavatelé serveru historie historie spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Výběrem karty **Fáze** zobrazíte fáze přidružené k aplikaci.

    ![Karta Fáze historie historie jiskry](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Zobrazit fáze Zapalovací hodu")

    Každá fáze může mít více úkolů, pro které můžete zobrazit statistiky provádění, jako je uvedeno níže.

    ![Podrobnosti k artě Fáze historie spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Zobrazit podrobnosti o fázích Spark")

4. Na stránce podrobností o vymezené ploše můžete spustit vizualizaci DAG. Rozbalte odkaz **Vizualizace DAG** v horní části stránky, jak je znázorněno níže.

    ![Zobrazit Spark fáze DAG vizualizace](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG nebo direct aclyic graph představuje různé fáze aplikace. Každý modrý rámeček v grafu představuje operaci Spark vyvolána z aplikace.

5. Na stránce podrobností o vymezené ploše můžete také spustit zobrazení časové osy aplikace. Rozbalte odkaz **Časová osa události** v horní části stránky, jak je znázorněno níže.

    ![Zobrazit časovou osu událostí fází Jiskry](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Tím se zobrazí události Spark ve formě časové osy. Zobrazení časové osy je k dispozici na třech úrovních, napříč úlohami, v rámci úlohy a ve fázi. Výše uvedený obrázek zachycuje zobrazení časové osy pro danou fázi.

   > [!TIP]  
   > Pokud zaškrtnete políčko **Povolit přiblížení,** můžete se posouvat doleva a doprava v zobrazení časové osy.

6. Další karty v uzdu Spark poskytují užitečné informace o instanci Spark také.

   * Karta Úložiště – Pokud vaše aplikace vytvoří RDD, můžete najít informace o těch na kartě Úložiště.
   * Karta Prostředí – tato karta obsahuje užitečné informace o instanci Spark, například:
     * Verze Scala
     * Adresář protokolu událostí přidružený ke clusteru
     * Počet jader exekutorů pro aplikaci
     * Atd.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Vyhledání informací o dokončených úlohách pomocí serveru Historie spark

Po dokončení úlohy jsou informace o úloze na serveru historie spark ustáleny.

1. Chcete-li spustit spark history server, vyberte na stránce **Přehled** v části **Řídicí panely clusteru** **vyberte server historie Spark** .

    ![Spuštění serveru historie Spark portálu Azure](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spuštění serveru historie Spark1")

   > [!TIP]  
   > Případně můžete také spustit spark History Server UI z ui Ambari. Chcete-li spustit ui Ambari, vyberte v okně Přehled **vyberte ambari domů** v **řídicích panelech clusteru**. Z ui Ambari přejděte na **Spark2** > **Quick Links** > **Spark2 History Server UI**.

2. Zobrazí se všechny dokončené aplikace uvedené. Vyberte ID aplikace, chcete-li přejít k podrobnostem aplikace pro další informace.

    ![Aplikace dokončené na serveru historie spark](./media/apache-spark-job-debugging/view-completed-applications.png "Spuštění serveru historie Spark2")

## <a name="see-also"></a>Viz také

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Ladění úloh Apache Spark pomocí rozšířeného serveru historie Spark](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Pro datové analytiky

* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza telemetrických dat Application Insight pomocí Apache Spark v HDInsightu](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>Pro vývojáře Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
