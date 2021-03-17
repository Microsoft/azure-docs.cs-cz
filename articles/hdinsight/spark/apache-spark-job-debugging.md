---
title: Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight
description: Pro sledování a ladění úloh spuštěných v clusteru Spark v Azure HDInsight použijte uživatelské rozhraní, rozhraní Spark a server historie Sparku.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: f332416a10aa86cb7e0bc7ba560537955d9f2faa
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930566"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění úloh Apache Spark spuštěných ve službě Azure HDInsight

V tomto článku se dozvíte, jak sledovat a ladit Apache Spark úlohy spuštěné v clusterech HDInsight. Proveďte ladění pomocí uživatelského rozhraní Apache Hadoop nitě, uživatelského rozhraní Spark a serveru s historií Spark. Pomocí poznámkového bloku, který je k dispozici pro cluster Spark, můžete spustit úlohu Sparku, **Machine Learning: prediktivní analýza dat kontroly potravin pomocí MLLib**. Pomocí následujících kroků můžete sledovat aplikaci, kterou jste odeslali pomocí jakéhokoli jiného přístupu, například **Spark-Submit**.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Měli byste začít s poznámkovým blokem **[Machine Learning: prediktivní analýza dat kontroly potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Pokyny, jak spustit tento poznámkový blok, získáte pomocí odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v uživatelském rozhraní PŘÍZe

1. Spusťte uživatelské rozhraní PŘÍZe. V části **řídicí panely clusteru** vyberte **příze** .

    ![Azure Portal spustit rozhraní PŘÍZe](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternativně můžete také spustit rozhraní PŘÍZe z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, vyberte **Ambari domů** v části **řídicí panely clusteru**. V uživatelském rozhraní Ambari přejděte na rychlé odkazy na **přízi**  >   > aktivní Správce prostředků > **Správce prostředků uživatelském rozhraní**.

2. Vzhledem k tomu, že jste spustili úlohu Sparku pomocí poznámkových bloků Jupyter, aplikace má název **remotesparkmagics** (název všech aplikací spuštěných z poznámkových bloků). Chcete-li získat další informace o úloze, vyberte ID aplikace proti názvu aplikace. Tato akce spustí zobrazení aplikace.

    ![Server historie Sparku najít ID aplikace Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    U takových aplikací, které se spouštějí z notebooků Jupyter, je stav vždycky **spuštěný** , dokud neukončíte Poznámkový blok.

3. V zobrazení aplikace můžete podrobněji přejít k části informace o kontejnerech přidružených k aplikaci a protokolech (stdout/stderr). Uživatelské rozhraní Spark můžete také spustit kliknutím na propojení odpovídající **adrese URL pro sledování**, jak je znázorněno níže.

    ![Server historie Spark stáhnout protokoly kontejneru](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v uživatelském rozhraní Spark

V uživatelském rozhraní Spark můžete přejít k podrobnostem úloh Spark, které vytváří aplikace, kterou jste spustili dříve.

1. Chcete-li spustit uživatelské rozhraní Spark, v zobrazení aplikace vyberte odkaz na **adresu URL pro sledování**, jak je znázorněno na snímku obrazovky výše. Můžete zobrazit všechny úlohy Sparku, které jsou spouštěny aplikací běžícími v Jupyter Notebook.

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

    Tento obrázek zobrazuje události Spark ve formě časové osy. Zobrazení Časová osa je k dispozici na třech úrovních, napříč úlohami v rámci úlohy a v rámci fáze. Výše uvedený obrázek zachytí zobrazení časové osy pro danou fázi.

   > [!TIP]  
   > Pokud zaškrtnete políčko **Povolit zvětšení velikosti** , můžete v zobrazení Časová osa přejít doleva a doprava.

6. Další karty v uživatelském rozhraní Spark obsahují také užitečné informace o instanci Spark.

   * Karta úložiště – Pokud vaše aplikace vytvoří RDD, můžete najít informace na kartě úložiště.
   * Karta prostředí – Tato karta poskytuje užitečné informace o vaší instanci Spark, jako je:
     * Verze Scala
     * Adresář protokolu událostí přidružený ke clusteru
     * Počet jader prováděcích modulů pro aplikaci

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Vyhledání informací o dokončených úlohách pomocí serveru historie Spark

Po dokončení úlohy jsou informace o úloze trvalé na serveru historie Sparku.

1. Pokud chcete spustit server historie Sparku, na stránce **Přehled** vyberte v části **řídicí panely clusteru** možnost **Server historie Spark** .

    ![Azure Portal spustit historii serveru Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spustit historii Sparku Server1")

   > [!TIP]  
   > Alternativně můžete také spustit uživatelské rozhraní serveru historie Sparku z uživatelského rozhraní Ambari. Pokud chcete spustit uživatelské rozhraní Ambari, v okně Přehled vyberte **Ambari domů** v části **řídicí panely clusteru**. V uživatelském rozhraní Ambari přejděte do části **Spark2**  >  **Rychlé odkazy**  >  **Spark2 v uživatelském rozhraní serveru**.

2. Zobrazí se všechny dokončené aplikace v seznamu. Pro další informace vyberte ID aplikace a přejděte k podrobnostem o aplikaci.

    ![Server historie Spark dokončil aplikace](./media/apache-spark-job-debugging/view-completed-applications.png "Spustit historii Spark Server2")

## <a name="see-also"></a>Viz také

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Ladění úloh Apache Spark pomocí serveru rozšířených historií Sparku](apache-azure-spark-history-server.md)
* [Ladění aplikací Apache Spark pomocí Azure Toolkit for IntelliJ přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
