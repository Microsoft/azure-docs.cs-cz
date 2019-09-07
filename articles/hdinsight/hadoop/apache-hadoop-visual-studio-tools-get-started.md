---
title: Nástroje pro Apache Hadoop a Data Lake pro Visual Studio – Azure HDInsight
description: Naučte se instalovat a používat nástroje Data Lake pro Visual Studio pro připojení k Apache Hadoopm clusterům ve službě Azure HDInsight a následné spuštění dotazů na podregistr.
keywords: hadoop tools, hive query, visual studio, visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 7a243dd0eca179317309438c31c114c94f94bf00
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736437"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Použití Data Lake nástrojů pro Visual Studio pro připojení k Azure HDInsight a spouštění dotazů Apache Hive

Naučte se používat [Microsoft Azure Data Lake a Stream Analytics nástroje pro Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (označované také jako nástroje Data Lake) pro připojení k Apache Hadoopm clusterům ve [službě Azure HDInsight](../hdinsight-hadoop-introduction.md) a odesílání dotazů na podregistry.  

Další informace o používání služby HDInsight najdete v tématech [Úvod do služby HDInsight](../hdinsight-hadoop-introduction.md) a [Začínáme se službou HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Další informace o připojení ke clusteru Apache Storm najdete v tématu [vývoj C# topologií pro Apache Storm ve službě HDInsight pomocí sady Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete přistupovat ke službě Azure Data Lake Analytics i HDInsight. Informace o nástrojích Data Lake najdete v tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku a použití Data Lakech nástrojů pro Visual Studio potřebujete následující položky:

* Cluster Azure HDInsight. Pokud chcete vytvořit cluster HDInsight, přečtěte si téma Začínáme s [používáním Apache Hadoop ve službě Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pokud chcete spustit interaktivní Apache Hive dotazy, potřebujete cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md) .  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 nebo novější).  [Edice Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) je zdarma.  Viz také, [instalace sady Visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) a sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Existují mírné variace rozhraní v rámci sady Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake nástroje již nejsou podporovány pro Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 nebo Visual Studio 2019  
  Během instalace se ujistěte, že máte aspoň úlohy **vývoje Azure** nebo **úložiště a zpracování dat**.  

  V případě existujících instalací přejděte na panelu nabídek na **nástroje** > **získat nástroje a funkce...** a otevřete instalační program pro Visual Studio.  Pak vyberte aspoň úlohy vývoj pro **Azure** nebo **ukládání a zpracování dat**.

  ![Snímek obrazovky s Instalační program pro Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 a 2015  
  [Stažení data Lakech nástrojů](https://www.microsoft.com/download/details.aspx?id=49504). Zvolte verzi nástrojů Data Lake, která se shoduje s vaší verzí sady Visual Studio.  

> [!NOTE]  
> V současné době je dostupná pouze anglická verze nástrojů Data Lake pro Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizace Data Lakech nástrojů pro Visual Studio  

1. Otevřít Visual Studio.

2. Z řádku nabídek přejděte na **nástroje** > **rozšíření a aktualizace...** .

3. V okně **rozšíření a aktualizace** rozbalte vlevo možnost **aktualizace** .

4. Pokud je k dispozici aktualizace, **Azure Data Lake a Stream analytické nástroje** se zobrazí v hlavním okně.  Vyberte **aktualizovat**.

> [!NOTE]  
> Pro připojení ke clusterům Interactive Query a spouštění interaktivních dotazů Hive můžete použít pouze nástroje Data Lake verze 2.3.0.0 nebo novější.

## <a name="connect-to-azure-subscriptions"></a>Připojení k předplatným služby Azure
Pomocí nástrojů Data Lake pro Visual Studio se můžete připojit ke clusterům HDInsight, provádět některé základní operace správy a spouštět dotazy Hive.

> [!NOTE]  
> Informace o připojení k obecnému clusteru Hadoop najdete v tématu [Zapisování a odesílání dotazů Hive pomocí sady Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Připojení k předplatnému Azure:

1. Otevřít Visual Studio.

2. V řádku nabídek přejděte k **zobrazení** > **Průzkumník serveru**.

3. V Průzkumník serveru klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit se k Microsoft Azure předplatného...** a dokončete proces přihlašování.

4. Z Průzkumník serveru se zobrazí seznam stávajících clusterů HDInsight. Pokud nemáte žádné clustery, můžete nějaký vytvořit pomocí webu Azure Portal, Azure PowerShellu nebo sady SDK HDInsight. Další informace najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Snímek obrazovky se seznamem clusterů nástrojů Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Seznam clusterů nástrojů Data Lake pro Visual Studio v Průzkumníku serveru")

5. Rozbalte cluster služby HDInsight. Zobrazí se **databáze podregistru**, výchozí účet úložiště, propojené účty úložiště a **protokol služby Hadoop** . Entity můžete dále rozšířit.

Po připojení k předplatnému Azure můžete provádět následující úlohy.

Připojení k webu Azure Portal ze sady Visual Studio:

1. Z Průzkumník serveru přejděte na **Azure** > **HDInsight** a vyberte svůj cluster.

2. Klikněte pravým tlačítkem na cluster HDInsight a **v Azure Portal [sic] vyberte Spravovat cluster**.

Dotazování na dotazy nebo poskytnutí zpětné vazby ze sady Visual Studio:

1. Z Průzkumník serveru přejděte na **Azure** > **HDInsight**.

2. Klikněte pravým tlačítkem na **HDInsight** a vyberte **Fórum MSDN** , kde se budou klást dotazy, nebo **sdělte zpětnou** vazbu, která vám poskytne svůj názor.

## <a name="link-a-cluster"></a>Propojení clusteru
Cluster můžete propojit tak, že kliknete pravým tlačítkem na **HDInsight** a pak vyberete **propojit cluster HDInsight**. Zadejte **adresu URL připojení**, **uživatelské jméno** a **heslo**, klikněte na **Další** **a pak cluster**by měl být uvedený v uzlu HDInsight úspěšné.

![Snímek obrazovky s dialogem pro Data Lake nástrojů pro Visual Studio Link cluster](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Klikněte pravým tlačítkem na propojený cluster, vyberte **Upravit**a uživatel může aktualizovat informace o clusteru. Přidání clusteru HDInsight teď podporuje jenom podregistr.

![Snímek obrazovky Data Lake nástrojů pro Visual Studio Link cluster Update](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Zkoumání propojených prostředků
V Průzkumníkovi serveru můžete zobrazit výchozí účet úložiště a všechny propojené účty úložiště. Pokud rozbalíte výchozí účet úložiště, uvidíte kontejnery na účtu úložiště. Jsou označeny jako výchozí účet úložiště a výchozí kontejner. Kliknutím pravým tlačítkem na jakýkoli kontejner zobrazíte jeho obsah.

![Snímek obrazovky se seznamem propojených prostředků nástrojů Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Seznam propojených prostředků")

Po otevření kontejneru můžete k nahrání, odstranění a stažení objektů blob použít následující tlačítka:

![Snímek obrazovky s operacemi s objekty blob pro nástroje Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Nahrání, odstranění a stažení objektů blob v Průzkumníku serveru")

## <a name="run-interactive-apache-hive-queries"></a>Spouštění interaktivních dotazů Apache Hive
[Apache Hive](https://hive.apache.org) je infrastruktura datového skladu postavená na Hadoop. Hive se používá k souhrnům dat, dotazům a analýzám. Pomocí nástrojů Data Lake pro Visual Studio můžete spouštět dotazy Hive ze sady Visual Studio. Další informace o podregistru najdete v tématu [použití Apache Hive se službou HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) využívá [Hive s funkcí LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) v Apache Hive 2.1. Interactive Query přináší interaktivitu do složitých dotazů ve stylu datového skladu nad rozsáhlými uloženými datovými sadami. Spouštění dotazů Hive v Interactive Query je v porovnání s tradičními dávkovými úlohami Hive mnohem rychlejší. 

> [!NOTE]  
> Interaktivní dotazy Hive můžete spouštět pouze po připojení ke clusteru [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete také zobrazit obsah úlohy Hive. Nástroje Data Lake pro Visual Studio shromažďují a zpřístupňují protokoly Yarn určitých úloh Hive.

Z Průzkumník serveru přejděte na **Azure** > **HDInsight** a vyberte svůj cluster.  Toto je výchozí bod v Průzkumník serveru, ve kterém následují oddíly.

### <a name="view-hivesampletable"></a>Zobrazit hivesampletable
Všechny clustery HDInsight mají výchozí ukázkovou tabulku podregistru s názvem `hivesampletable`.  

Z clusteru přejděte do **podregistru databáze** > **výchozí** > **hivesampletable**.

* Zobrazení `hivesampletable` schématu:  
Rozbalte **hivesampletable**.

* Zobrazení `hivesampletable` dat:  
Klikněte pravým tlačítkem na **hivesampletable**a vyberte **Zobrazit prvních 100 řádků**.  Jedná se o ekvivalent procesu spuštění následujícího dotazu Hive pomocí ovladače ODBC Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  Počet řádků můžete přizpůsobit.

  ![Snímek obrazovky s dotazem na schéma HDInsight Hive v sadě Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Výsledky dotazu Hive")

### <a name="create-hive-tables"></a>Vytváření tabulek Hive
K vytvoření tabulky Hive můžete použít grafické rozhraní (GUI) nebo dotazy Hive. Informace o použití dotazů na podregistry najdete v tématu [spuštění dotazů Apache Hive](#run.queries).

1. Z clusteru přejděte na >  **databáze podregistru** **výchozí**.

2. Klikněte pravým tlačítkem na **výchozí**a vyberte **vytvořit tabulku**.

3. Nakonfigurujte tabulku podle potřeby.  

4. Vyberte **Vytvořit tabulku** a odešlete úlohu pro vytvoření nové tabulky Hive.

    ![Snímek obrazovky s oknem Vytvoření tabulky ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Vytvoření tabulky Hive")

### <a name="run.queries"></a>Vytvoření a spuštění dotazů na podregistr
Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad-hoc
* Vytvoření aplikace Hive

Vytvoření a spuštění dotazů ad hoc:

1. Klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz, a vyberte **zapsat dotaz na podregistr**.  

2. Zadejte následující dotaz na podregistr:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM`, IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF.

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

3. Vyberte režim spuštění:

    * **Interaktivní**  

      Ujistěte se, že je vybraná možnost **interaktivní** , a pak vyberte **Spustit**.

      ![Snímek obrazovky s dotazem a spuštěním](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Ujistěte se, že je vybraná možnost **Batch** , a pak vyberte **Odeslat**.  Pokud vyberete možnost Upřesnit odeslání, nakonfigurujte **název úlohy**, **argumenty**, **Další konfigurace**a **stavový adresář** pro skript.

      ![Snímek obrazovky s dotazem a dávkou](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Snímek obrazovky s dotazem Hive v HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Odeslání dotazů")

      > [!NOTE]  
      > Do clusterů interaktivních dotazů nelze odesílat dávky.  Je nutné použít interaktivní režim.

Vytvoření a spuštění řešení Hive:

1. Z řádku nabídek přejděte na **soubor** > **Nový** > **projekt...** .

2. V levém podokně přejděte do části **nainstalovaný** > **Azure Data Lake** > **podregistr (HDInsight)** .  

3. V prostředním podokně vyberte **Aplikace Hive**. Zadejte vlastnosti a pak vyberte **OK**.

    ![Snímek obrazovky s novým projektem Hive ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Vytvoření aplikací Hive v sadě Visual Studio")

4. V **Průzkumníku řešení** dvojím kliknutím otevřete skript **Script.hql**.

### <a name="view-job-summary-and-output"></a>Zobrazení souhrnu a výstupu úlohy

Souhrn úlohy se mírně liší mezi **Batch** a **interaktivním** režimem.

![Souhrn úlohy](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Souhrn úlohy podregistru")

Pomocí tlačítka **aktualizovat** aktualizujte stav, dokud se stav úlohy nezmění na **dokončeno**.  

* V podrobnostech o úloze z **dávkového** režimu vyberte odkazy v dolní části, abyste **viděli dotaz na úlohy**, **výstup úlohy**, **protokol úlohy**nebo **protokol příze**.

* Podrobnosti o úloze z **interaktivního** režimu najdete v tématu **výstup** na kartách Output a **HiveServer2**.

  ![Podrobnosti úlohy](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Podrobnosti úlohy podregistru")

### <a name="view-job-graph"></a>Zobrazit graf úlohy

V současné době jsou grafy úloh zobrazeny pouze pro úlohy podregistru, které používají tez jako spouštěcí modul.  Informace o povolení tez najdete v tématu [použití Apache Hive ve službě HDInsight](hdinsight-use-hive.md).  Viz také [Apache tez místo zmenšování mapy použijte](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Pokud chcete zobrazit všechny operátory uvnitř vrcholu, dvakrát klikněte na vrcholy grafu úlohy. Můžete také ukázat na konkrétní operátor a zobrazit tak další podrobnosti o tomto operátoru.

Graf úlohy se nesmí zobrazit ani v případě, že je jako spouštěcí modul zadán tez, pokud se nespustí žádná aplikace TEZ.  K tomu může dojít, protože úloha neobsahuje příkazy DML nebo příkazy DML mohou vracet bez spuštění aplikace TEZ. Například `SELECT * FROM table1` nebude spouštět aplikaci TEZ.

![Graf úlohy](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Souhrn úlohy podregistru")


### <a name="task-execution-detail"></a>Podrobnosti o provedení úkolu

Z grafu úlohy můžete vybrat **Podrobnosti spuštění úlohy** a získat tak strukturované a vizuální informace pro úlohy podregistru. Můžete také získat další podrobnosti o úloze. Pokud dojde k problémům s výkonem, můžete pomocí tohoto zobrazení získat další podrobnosti o problému. Například můžete získat informace o tom, jak jednotlivé úlohy fungují, a podrobné informace o jednotlivých úlohách (zápis/čtení dat, naplánovaný/počáteční/konečný čas atd.). Na základě vizualizovaných informací můžete tyto informace využít k ladění konfigurací úloh nebo architektury systémy.

![Snímek obrazovky s oknem Zobrazení spouštění úlohy ve Visual Studio Tools pro Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Zobrazení spouštění úlohy")


### <a name="view-hive-jobs"></a>Zobrazení úloh Hive
Můžete zobrazit dotazy úlohy, výstup úlohy, protokoly úlohy a protokoly Yarn pro úlohy Hive.

V nejnovější verzi nástrojů můžete zobrazit obsah úloh Hive prostřednictvím shromažďování a zpřístupnění protokolů Yarn. Protokol Yarn vám může pomoci prozkoumat problémy s výkonem. Další informace o tom, jak služba HDInsight shromažďuje protokoly Yarn, najdete v tématu [Programový přístup k protokolům aplikace HDInsight](../hdinsight-hadoop-access-yarn-app-logs.md).

Zobrazení úloh Hive:

1. Klikněte pravým tlačítkem na cluster HDInsight a vyberte **Zobrazit úlohy**. Zobrazí se seznam úloh Hive, které se v clusteru spustily.  

2. Vyberte úlohu. V okně **Souhrn úlohy Hive** vyberte jednu z následujících možností:
    - **Dotaz úlohy**
    - **Výstup úlohy**
    - **Protokol úlohy**  
    - **Protokol Yarn**

    ![Snímek obrazovky s oknem Zobrazení úloh Hive ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Zobrazení úloh Hive")


## <a name="run-apache-pig-scripts"></a>Spustit skripty Apache prasete

1. Z řádku nabídek přejděte na **soubor** > **Nový** > **projekt...** .

2. V levém podokně přejděte na **nainstalované** > **Azure Data Lake** > **prase (HDInsight)** .  

3. V prostředním podokně vyberte **aplikace pro prase**. Zadejte vlastnosti a pak vyberte **OK**.

4. V **Průzkumník řešení**poklikejte na **script. prase** a otevře se skript.

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy
* Opravili jsme problém, kdy se nezobrazovaly výsledky začínající hodnotami null. Pokud vás tento problém blokuje, kontaktujte tým podpory.
* Skript HQL, který sada Visual Studio vytváří, je zakódovaný v závislosti na nastavení pro místní oblast uživatele. Skript se nespustí správně, pokud ho nahrajete do clusteru jako binární soubor.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak se pomocí balíčku nástrojů Data Lake pro Visual Studio připojit ke clusterům HDInsight ze sady Visual Studio. Také jste se naučili spustit dotaz Hive. Další informace najdete v těchto článcích:

* [Spouštění dotazů Apache Hivu pomocí Nástrojů Data Lake pro Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Použití Hadoop Hive ve službě HDInsight](hdinsight-use-hive.md)
* [Začínáme používat Apache Hadoop ve službě HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Odesílání úloh Apache Hadoop v HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analýza dat Twitteru pomocí Apache Hadoop ve službě HDInsight](../hdinsight-analyze-twitter-data.md)

