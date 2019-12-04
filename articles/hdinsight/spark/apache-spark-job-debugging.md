---
title: Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight
description: Pro sledování a ladění úloh spuštěných v clusteru Spark v Azure HDInsight použijte uživatelské rozhraní, rozhraní Spark a server historie Sparku.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 110a8e86fc1916254ab914630ce10d2b7ae073b7
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74775329"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight

V tomto článku se dozvíte, jak sledovat a ladit [Apache Spark](https://spark.apache.org/) úlohy spuštěné v clusterech HDInsight pomocí uživatelského rozhraní [Apache Hadoop nitě](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , uživatelského rozhraní Spark a serveru pro historii Spark. Pomocí poznámkového bloku, který je k dispozici pro cluster Spark, můžete spustit úlohu Sparku, **Machine Learning: prediktivní analýza dat kontroly potravin pomocí MLLib**. Pomocí následujících kroků můžete sledovat aplikaci, kterou jste odeslali pomocí jakéhokoli jiného přístupu, například **Spark-Submit**.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Měli byste začít s poznámkovým blokem **[Machine Learning: prediktivní analýza dat kontroly potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md)** . Pokyny, jak spustit tento poznámkový blok, získáte pomocí odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v uživatelském rozhraní PŘÍZe

1. Spusťte uživatelské rozhraní PŘÍZe. V části **řídicí panely clusteru**vyberte **příze** .

    ![Azure Portal spustit rozhraní PŘÍZe](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternativně můžete také spustit rozhraní PŘÍZe z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, vyberte **Ambari domů** v části **řídicí panely clusteru**. V uživatelském rozhraní Ambari přejděte na **příze** > **Rychlé odkazy** > **uživatelského rozhraní**aktivní Správce prostředků > Správce prostředků.

2. Vzhledem k tomu, že jste spustili úlohu Spark pomocí poznámkových bloků Jupyter, aplikace má název **remotesparkmagics** (Toto je název pro všechny aplikace, které jsou spouštěny z poznámkových bloků). Chcete-li získat další informace o úloze, vyberte ID aplikace proti názvu aplikace. Tím se spustí zobrazení aplikace.

    ![Server historie Sparku najít ID aplikace Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    U takových aplikací, které se spouštějí z notebooků Jupyter, je stav vždycky **spuštěný** , dokud neukončíte Poznámkový blok.

3. V zobrazení aplikace můžete podrobněji přejít k části informace o kontejnerech přidružených k aplikaci a protokolech (stdout/stderr). Uživatelské rozhraní Spark můžete také spustit kliknutím na propojení odpovídající **adrese URL pro sledování**, jak je znázorněno níže.

    ![Server historie Spark stáhnout protokoly kontejneru](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v uživatelském rozhraní Spark

V uživatelském rozhraní Spark můžete přejít k podrobnostem úloh Spark, které vytváří aplikace, kterou jste spustili dříve.

1. Chcete-li spustit uživatelské rozhraní Spark, v zobrazení aplikace vyberte odkaz na **adresu URL pro sledování**, jak je znázorněno na snímku obrazovky výše. Zobrazí se všechny úlohy Sparku spouštěné aplikací běžícími na poznámkovém bloku Jupyter.

    ![Karta úlohy serveru historie Sparku](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Vyberte kartu **vykonavatelé** a zobrazte informace o zpracování a uložení pro každý prováděcí modul. Můžete také načíst zásobník volání výběrem odkazu **výpisu vlákna** .

    ![Karta vykonavatelé serveru historie Spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Vyberte kartu **fáze** a zobrazte fáze spojené s aplikací.

    ![Karta fáze serveru historie Sparku](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Zobrazit fáze Sparku")

    Každá fáze může mít několik úloh, pro které můžete zobrazit statistiku spuštění, jak je znázorněno níže.

    ![Podrobnosti karty fáze serveru pro historii Sparku](./media/apache-spark-job-debugging/view-spark-stages-details.png "Zobrazit podrobnosti o fázích Sparku")

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
   * Karta prostředí – Tato karta poskytuje užitečné informace o vaší instanci Spark, jako je:
     * Verze Scala
     * Adresář protokolu událostí přidružený ke clusteru
     * Počet jader prováděcích modulů pro aplikaci
     * Atd.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Vyhledání informací o dokončených úlohách pomocí serveru historie Spark

Po dokončení úlohy jsou informace o úloze trvalé na serveru historie Sparku.

1. Pokud chcete spustit server historie Sparku, na stránce **Přehled** vyberte v části **řídicí panely clusteru**možnost **Server historie Spark** .

    ![Azure Portal spustit historii serveru Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spustit historii Sparku Server1")

   > [!TIP]  
   > Alternativně můžete také spustit uživatelské rozhraní serveru historie Sparku z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, v okně Přehled vyberte **Ambari domů** v části **řídicí panely clusteru**. V uživatelském rozhraní Ambari přejděte na **Spark2** > **Rychlé odkazy** > **uživatelské rozhraní serveru historie Spark2**.

2. Zobrazí se všechny dokončené aplikace v seznamu. Pro další informace vyberte ID aplikace a přejděte k podrobnostem o aplikaci.

    ![Server historie Spark dokončil aplikace](./media/apache-spark-job-debugging/view-completed-applications.png "Spustit historii Spark Server2")

## <a name="see-also"></a>Další informace najdete v tématech

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Ladění úloh Apache Spark pomocí serveru rozšířených historií Sparku](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Pro analytiky dat

* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
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
