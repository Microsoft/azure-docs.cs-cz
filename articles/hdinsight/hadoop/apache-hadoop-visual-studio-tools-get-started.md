---
title: Připojení k Apache Hadoopu pomocí nástrojů Data Lake pro Visual Studio – Azure HDInsight
description: Zjistěte, jak nainstalovat a používat nástroje Data Lake pro Visual Studio pro připojení ke clusterům Apache Hadoop v Azure HDInsight a následné spouštění dotazů Hive.
keywords: hadoop tools, hive query, visual studio, visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: cf392bb254e38b2e07a92e87927b12e144b26f16
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447476"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Pomocí nástrojů Data Lake pro Visual Studio pro připojení k Azure HDInsight a spouštět dotazy Apache Hive

Další informace o použití [Microsoft Azure Data Lake a Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (označované také jako Data Lake Tools) pro připojení k Apache Hadoop clusterů v [Azure HDInsight](../hdinsight-hadoop-introduction.md) a odesílání dotazů Hive.  

Další informace o používání služby HDInsight najdete v tématech [Úvod do služby HDInsight](../hdinsight-hadoop-introduction.md) a [Začínáme se službou HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Další informace o připojení ke clusteru Apache Storm, najdete v části [vývoj C# topologií pro Apache Storm v HDInsight pomocí sady Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete přistupovat ke službě Azure Data Lake Analytics i HDInsight. Informace o nástrojích Data Lake najdete v tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu a používání nástrojů Data Lake pro Visual Studio potřebujete následující položky:

* Cluster Azure HDInsight. Vytvoření clusteru služby HDInsight najdete v tématu [začněte používat Apache Hadoop v Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pokud chcete spouštět interaktivní dotazy Apache Hive, musíte [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md) clusteru.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 nebo novější).  [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) je zdarma.  Viz také [instalaci sady Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

  > [!IMPORTANT]  
  > Nástroje data Lake je již nejsou podporovány pro Visual Studio 2013. 

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017  
  Během instalace, ujistěte se vám to zahrnovat přinejmenším úlohy **vývoj pro Azure** nebo **ukládání a zpracování dat**.  

  Pro existující instalace, v řádku nabídek, přejděte na **nástroje** > **získat nástroje a funkce...**  otevřít instalační program sady Visual Studio.  Vyberte nejméně úlohy **vývoj pro Azure** nebo **ukládání a zpracování dat**.

  ![Snímek obrazovky instalačního programu sady Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 a 2015  
  [Stáhnout Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Zvolte verzi nástrojů Data Lake, která se shoduje s vaší verzí sady Visual Studio.  

> [!NOTE]  
> V současné době je dostupná pouze anglická verze nástrojů Data Lake pro Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizace nástrojů Data Lake pro Visual Studio  

1. Otevřete sadu Visual Studio.

2. V řádku nabídek, přejděte na **nástroje** > **rozšíření a aktualizace...** .

3. Z **rozšíření a aktualizace** okna rozbalte **aktualizace** na levé straně.

4. Pokud je k dispozici aktualizace **Azure Data Lake a Stream analytických nástrojů** se zobrazí v hlavním okně.  Vyberte **aktualizace**.

> [!NOTE]  
> Pro připojení ke clusterům Interactive Query a spouštění interaktivních dotazů Hive můžete použít pouze nástroje Data Lake verze 2.3.0.0 nebo novější.

## <a name="connect-to-azure-subscriptions"></a>Připojení k předplatným služby Azure
Pomocí nástrojů Data Lake pro Visual Studio se můžete připojit ke clusterům HDInsight, provádět některé základní operace správy a spouštět dotazy Hive.

> [!NOTE]  
> Informace o připojení k obecnému clusteru Hadoop najdete v tématu [Zapisování a odesílání dotazů Hive pomocí sady Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Připojení k předplatnému Azure:

1. Otevřete sadu Visual Studio.

2. V řádku nabídek, přejděte na **zobrazení** > **Průzkumníka serveru**.

3. Z Průzkumníka serveru, klikněte pravým tlačítkem na **Azure**vyberte **připojit k předplatnému Microsoft Azure...** a dokončete proces přihlašování.

4. Z Průzkumníka serveru zobrazí se seznam stávajících clusterů HDInsight. Pokud nemáte žádné clustery, můžete nějaký vytvořit pomocí webu Azure Portal, Azure PowerShellu nebo sady SDK HDInsight. Další informace najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Snímek obrazovky se seznamem clusterů nástrojů Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Seznam clusterů nástrojů Data Lake pro Visual Studio v Průzkumníku serveru")

5. Rozbalte cluster služby HDInsight. Zobrazí se **Databáze Hive**, výchozí účet úložiště, propojené účty úložiště a **protokol služby Hadoop**. Entity můžete dále rozšířit.

Po připojení k předplatnému Azure, můžete provádět následující úlohy.

Připojení k webu Azure Portal ze sady Visual Studio:

1. V Průzkumníku serveru přejděte na **Azure** > **HDInsight** a vyberte svůj cluster.

2. Klikněte pravým tlačítkem na HDInsight cluster a vyberte **spravovat Cluster webu Azure portal**.

Můžete klást otázky nebo poskytnutí zpětné vazby ze sady Visual Studio:

1. V Průzkumníku serveru přejděte na **Azure** > **HDInsight**.

2. Klikněte pravým tlačítkem na **HDInsight** a vyberte buď **fórum na webu MSDN** klást otázky, nebo **odeslat zpětnou vazbu** poskytnout zpětnou vazbu.

## <a name="explore-linked-resources"></a>Zkoumání propojených prostředků
V Průzkumníkovi serveru můžete zobrazit výchozí účet úložiště a všechny propojené účty úložiště. Pokud rozbalíte výchozí účet úložiště, uvidíte kontejnery na účtu úložiště. Jsou označeny jako výchozí účet úložiště a výchozí kontejner. Kliknutím pravým tlačítkem na jakýkoli kontejner zobrazíte jeho obsah.

![Snímek obrazovky se seznamem propojených prostředků nástrojů Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Seznam propojených prostředků")

Po otevření kontejneru můžete k nahrání, odstranění a stažení objektů blob použít následující tlačítka:

![Snímek obrazovky s operacemi s objekty blob pro nástroje Data Lake pro Visual Studio v Průzkumníku serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Nahrání, odstranění a stažení objektů blob v Průzkumníku serveru")

## <a name="run-interactive-apache-hive-queries"></a>Spouštění interaktivních dotazů Apache Hive
[Apache Hive](https://hive.apache.org) je infrastruktura datového skladu postavená na Hadoop. Hive se používá k souhrnům dat, dotazům a analýzám. Pomocí nástrojů Data Lake pro Visual Studio můžete spouštět dotazy Hive ze sady Visual Studio. Další informace o Hivu najdete v tématu [použití Apache Hivu se službou HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) využívá [Hive s funkcí LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) v Apache Hive 2.1. Interactive Query přináší interaktivitu do složitých dotazů ve stylu datového skladu nad rozsáhlými uloženými datovými sadami. Spouštění dotazů Hive v Interactive Query je v porovnání s tradičními dávkovými úlohami Hive mnohem rychlejší. Další informace najdete v tématu spouštění Apache dávkových úloh Hive.

> [!NOTE]  
> Interaktivní dotazy Hive můžete spouštět pouze po připojení ke clusteru [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete také zobrazit obsah úlohy Hive. Nástroje Data Lake pro Visual Studio shromažďují a zpřístupňují protokoly Yarn určitých úloh Hive.

V Průzkumníku serveru přejděte na **Azure** > **HDInsight** a vyberte svůj cluster.  To bude počáteční bod v Průzkumníku serveru pro oddíly dodržovat.

### <a name="view-hivesampletable"></a>Zobrazení hivesampletable
Všechny clustery HDInsight mají výchozí vzorovou tabulkou Hive se nazývá `hivesampletable`.  

V clusteru, přejděte na **databáze Hive** > **výchozí** > **hivesampletable**.

* Chcete-li zobrazit `hivesampletable` schématu:  
Rozbalte **hivesampletable**.

* Chcete-li zobrazit `hivesampletable` dat:  
Klikněte pravým tlačítkem na **hivesampletable**a vyberte **zobrazit prvních 100 řádků**.  Jedná se o ekvivalent procesu spuštění následujícího dotazu Hive pomocí ovladače ODBC Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  Počet řádků můžete přizpůsobit.

  ![Snímek obrazovky s dotazem na schéma HDInsight Hive v sadě Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Výsledky dotazu Hive")

### <a name="create-hive-tables"></a>Vytváření tabulek Hive
K vytvoření tabulky Hive můžete použít grafické rozhraní (GUI) nebo dotazy Hive. Informace o použití dotazů Hive naleznete v tématu [dotazy spustit Apache Hive](#run.queries).

1. V clusteru, přejděte na **databáze Hive** > **výchozí**.

2. Klikněte pravým tlačítkem na **výchozí**a vyberte **Create Table**.

3. Podle potřeby nakonfigurujte v tabulce.  

4. Vyberte **Vytvořit tabulku** a odešlete úlohu pro vytvoření nové tabulky Hive.

    ![Snímek obrazovky s oknem Vytvoření tabulky ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Vytvoření tabulky Hive")

### <a name="run.queries"></a>Vytváření a spouštění dotazů Hive
Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad hoc
* Vytvoření aplikace Hive

Vytvoření a spouštění dotazů ad hoc:

1. Klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz a vybrat **napsat dotaz Hive**.  

2. Zadejte dotazy Hive.  

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud zadáte například `SELECT * FROM`, IntelliSense vypíše všechny navrhované názvy tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF.

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

3. Zvolte režim spuštění:

    * **Interaktivní**  

      Zajištění **interaktivní** je vybrána a potom vyberte **Execute**.

      ![Snímek obrazovky z dotazu a spustit](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Zajištění **Batch** je vybrána a potom vyberte **odeslat**.  Pokud vyberete možnost rozšířeného odeslání, nakonfigurujte **název úlohy**, **argumenty**, **další konfigurace, které**, a **stavový adresář**skriptu.

      ![Snímek obrazovky s dotazy a batch](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Snímek obrazovky s dotazem Hive v HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Odeslání dotazů")

      > [!NOTE]  
      > Nejde odeslat dávky ke clusterům Interactive Query.  Je nutné použít interaktivní režim.

Vytvoření a spuštění řešení Hive:

1. V řádku nabídek, přejděte na **souboru** > **nový** > **projektu...** .

2. V levém podokně přejděte do **nainstalováno** > **Azure Data Lake** > **HIVE (HDInsight)**.  

3. V prostředním podokně vyberte **Aplikace Hive**. Zadejte vlastnosti a pak vyberte **OK**.

    ![Snímek obrazovky s novým projektem Hive ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Vytvoření aplikací Hive v sadě Visual Studio")

4. V **Průzkumníku řešení** dvojím kliknutím otevřete skript **Script.hql**.

### <a name="view-job-summary-and-output"></a>Zobrazení souhrnu úlohy a výstup

V souhrnu úlohy se mírně liší mezi liší **Batch** a **interaktivní** režimu.

![Souhrn úlohy](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Souhrn úlohy Hive")

Použití **aktualizovat** tlačítko pro aktualizaci stavu, dokud se stav úlohy změní na **dokončeno**.  

* Podrobnosti úlohy z **Batch** režimu, výběrem odkazů v dolní části můžete zobrazit **dotaz na úlohu**, **výstup úlohy**, **protokol úloh**, nebo **Protokol yarn**.

* Podrobnosti úlohy z **interaktivní** režim, viz karty **výstup** a **výstup serveru HiveServer2**.

  ![Podrobnosti úlohy](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "podrobností o úloze Hive")

### <a name="view-job-graph"></a>Zobrazit graf úlohy

V současné době grafy úloh se zobrazují pouze pro úlohy Hive, které používají jako prováděcí modul Tez.  Informace o povolení Tez najdete v tématu [použití Apache Hive v HDInsight](hdinsight-use-hive.md).  Viz také [použití rozhraní Apache Tez místo Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Pokud chcete zobrazit všechny operátory uvnitř vrcholu, dvakrát klikněte na vrcholy grafu úlohy. Můžete také ukázat na konkrétní operátor a zobrazit tak další podrobnosti o tomto operátoru.

Graf úlohy se nemusí zobrazit i v případě, že Tez je zadán jako prováděcí modul, pokud je spuštěna žádná aplikace Tez.  Příčinou může být úloha neobsahuje příkazy DML nebo příkazy DML můžou vrátit, aniž by spustily aplikaci Tez. Například `SELECT * FROM table1` nespustí aplikaci Tez.

![Graf úlohy](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Souhrn úlohy Hive")


### <a name="task-execution-detail"></a>Podrobnosti o provedení úkolu

Graf úlohy, můžete vybrat **podrobnosti o provedení úkolu** získat strukturované a vizualizované informace pro úlohy Hive. Můžete také získat další podrobnosti o úloze. Pokud dojde k problémům s výkonem, můžete pomocí tohoto zobrazení získat další podrobnosti o problému. Například můžete získat informace o tom, jak jednotlivé úlohy fungují, a podrobné informace o jednotlivých úlohách (zápis/čtení dat, naplánovaný/počáteční/konečný čas atd.). Na základě vizualizovaných informací můžete tyto informace využít k ladění konfigurací úloh nebo architektury systémy.

![Snímek obrazovky s oknem Zobrazení spouštění úlohy ve Visual Studio Tools pro Data Lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Zobrazení spouštění úlohy")


### <a name="view-hive-jobs"></a>Zobrazení úloh Hive
Můžete zobrazit dotazy úlohy, výstup úlohy, protokoly úlohy a protokoly Yarn pro úlohy Hive.

V nejnovější verzi nástrojů můžete zobrazit obsah úloh Hive prostřednictvím shromažďování a zpřístupnění protokolů Yarn. Protokol Yarn vám může pomoci prozkoumat problémy s výkonem. Další informace o tom, jak služba HDInsight shromažďuje protokoly Yarn, najdete v tématu [Programový přístup k protokolům aplikace HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Zobrazení úloh Hive:

1. Klikněte pravým tlačítkem na HDInsight cluster a vyberte **zobrazit úlohy**. Zobrazí se seznam úloh Hive, které se v clusteru spustily.  

2. Vyberte úlohu. V okně **Souhrn úlohy Hive** vyberte jednu z následujících možností:
   - **Dotaz úlohy**
   - **Výstup úlohy**
   - **Protokol úlohy**  
   - **Protokol Yarn**

     ![Snímek obrazovky s oknem Zobrazení úloh Hive ve Visual Studio Tools pro HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Zobrazení úloh Hive")


## <a name="run-apache-pig-scripts"></a>Spouštění skriptů Apache Pig

1. V řádku nabídek, přejděte na **souboru** > **nový** > **projektu...** .

2. V levém podokně přejděte do **nainstalováno** > **Azure Data Lake** > **Pig (HDInsight)**.  

3. V prostředním podokně vyberte **Pig aplikace**. Zadejte vlastnosti a pak vyberte **OK**.

4. V **Průzkumníka řešení**, dvakrát klikněte na panel **Script.pig** otevřete skript.

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy
* Opravili jsme problém, kdy se nezobrazovaly výsledky začínající hodnotami null. Pokud vás tento problém blokuje, kontaktujte tým podpory.
* Skript HQL, který sada Visual Studio vytváří, je zakódovaný v závislosti na nastavení pro místní oblast uživatele. Skript se nespustí správně, pokud ho nahrajete do clusteru jako binární soubor.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak se pomocí balíčku nástrojů Data Lake pro Visual Studio připojit ke clusterům HDInsight ze sady Visual Studio. Také jste se naučili spustit dotaz Hive. Další informace najdete v těchto článcích:

* [Spouštějte dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Použití Hadoop Hive ve službě HDInsight](hdinsight-use-hive.md)
* [Začněte používat Apache Hadoop v HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Odeslání úloh systému Apache Hadoop v HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analýza dat Twitteru pomocí Apache Hadoop v HDInsight](../hdinsight-analyze-twitter-data-linux.md)

