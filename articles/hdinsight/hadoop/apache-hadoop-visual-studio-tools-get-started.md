---
title: Apache Hadoop & Visual Studio Data Lake Tools – Azure HDInsight
description: Naučte se instalovat a používat nástroje pro Data Lake pro Visual Studio. Pomocí nástroje se můžete připojit k Apache Hadoop clusterům ve službě Azure HDInsight a pak spouštět dotazy na podregistr.
keywords: hadoop tools, hive query, visual studio, visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 8d8e9784ea21bf5f2b6902e3d93c5c09c1ec5670
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944556"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Použití Data Lake nástrojů pro Visual Studio pro připojení k Azure HDInsight a spouštění dotazů Apache Hive

Naučte se používat Microsoft Azure Data Lake a Stream Analytics Tools for Visual Studio (nástroje Data Lake). Pomocí tohoto nástroje se můžete připojit k [Apache Hadoop clusterům ve službě Azure HDInsight](apache-hadoop-introduction.md) a odesílat dotazy na podregistry.  

Další informace o používání služby HDInsight najdete v tématu [Začínáme se službou HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Další informace o připojení k Apache Storm najdete v tématu [Vývoj topologií C# pro Apache Storm pomocí data Lakech nástrojů](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete přistupovat ke službě Azure Data Lake Analytics i HDInsight. Informace o nástrojích Data Lake najdete v tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku a použití Data Lakech nástrojů pro Visual Studio potřebujete následující položky:

* Cluster Azure HDInsight. Pokud chcete vytvořit cluster HDInsight, přečtěte si téma Začínáme s [používáním Apache Hadoop ve službě Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pokud chcete spustit interaktivní Apache Hive dotazy, potřebujete cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md) .  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Edice Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) je zdarma. Zde uvedené pokyny jsou pro [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio  

Postupujte podle příslušných pokynů k instalaci Data Lake nástrojů pro vaši verzi sady Visual Studio:

* Pro Visual Studio 2017 nebo Visual Studio 2019:

    Během instalace sady Visual Studio se ujistěte, že jste zahrnuli úlohu **vývoj pro Azure** nebo **úložiště dat a zpracování** .  

    Pro existující instalace sady Visual Studio přejděte na panel nabídek rozhraní IDE a výběrem **Možnosti**  >  **získat nástroje a funkce** otevřete instalační program pro Visual Studio. Na kartě **úlohy** vyberte aspoň úlohu **vývoj pro Azure** (v části **Web & Cloud**). Nebo vyberte úlohu **ukládání a zpracování dat** (v části **jiné sady nástrojů**).

  ![Výběr úlohy, Instalační program pro Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Pro Visual Studio 2015:

    [Stažení data Lakech nástrojů](https://www.microsoft.com/download/details.aspx?id=49504). Zvolte verzi nástrojů Data Lake, která se shoduje s vaší verzí sady Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizace Data Lakech nástrojů pro Visual Studio  

Potom nezapomeňte aktualizovat Data Lake nástroje na nejnovější verzi.

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **pokračovat bez kódu**.

3. V řádku nabídek rozhraní IDE sady Visual Studio vyberte **rozšíření**  >  **Spravovat rozšíření**.

4. V dialogovém okně **Spravovat rozšíření** rozbalte uzel **aktualizace** .

5. Pokud seznam dostupných aktualizací zahrnuje **Azure Data Lake a analytické nástroje pro streamování**, vyberte ji. Pak vyberte příslušné tlačítko **aktualizovat** . Po zobrazení a ukončení dialogového okna **Stáhnout a nainstalovat** Visual Studio přidá do plánu aktualizace rozšíření **analytické nástroje Azure Data Lake a streamování** .

6. Zavřete všechna okna sady Visual Studio. Zobrazí se dialogové okno **instalátor VSIX** .

7. Vyberte **licenci** pro přečtení licenčních podmínek a pak vyberte **Zavřít** a vraťte se do dialogového okna **instalačního programu VSIX** .

8. Vyberte **Upravit**. Je zahájena instalace aktualizace rozšíření. Po chvíli se dialogové okno změní, aby bylo vidět, že se dokončilo provádění úprav. Kliknutím na **Zavřít** a dokončete instalaci restartováním sady Visual Studio.

> [!NOTE]  
> Pro připojení ke clusterům Interactive Query a spouštění interaktivních dotazů Hive můžete použít pouze nástroje Data Lake verze 2.3.0.0 nebo novější.

## <a name="connect-to-azure-subscriptions"></a>Připojení k předplatným služby Azure

Pomocí nástrojů Data Lake pro Visual Studio se můžete připojit ke clusterům HDInsight, provádět některé základní operace správy a spouštět dotazy na podregistry.

> [!NOTE]  
> Informace o připojení k obecnému clusteru Hadoop najdete v tématu [jak zapisovat a odesílat dotazy na podregistry pomocí sady Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio).

### <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

Připojení k předplatnému Azure:

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **pokračovat bez kódu**.

3. V panelu nabídek rozhraní IDE vyberte možnost **Zobrazit**  >  **Průzkumník serveru**.

4. V **Průzkumník serveru** klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit se k Microsoft Azure předplatného** a dokončete proces ověřování. V **Průzkumník serveru** rozbalte **Azure**  >  **HDInsight** a zobrazte seznam existujících clusterů HDInsight.

5. Pokud nemáte žádné clustery, vytvořte ho pomocí Azure Portal, Azure PowerShell nebo sady SDK HDInsight. Další informace najdete v tématu [Nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Seznam clusteru HDInsight, Průzkumník serveru, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Rozbalte cluster služby HDInsight. Cluster obsahuje uzly pro **databáze podregistru**. Také výchozí účet úložiště, všechny další propojené účty úložiště a **protokol služby Hadoop**. Entity můžete dále rozšířit.

Po připojení k předplatnému Azure můžete provádět následující úlohy.

### <a name="connect-to-azure-from-visual-studio"></a>Připojení k Azure ze sady Visual Studio

Připojení k webu Azure Portal ze sady Visual Studio:

1. V **Průzkumník serveru** rozbalte položku **Azure**  >  **HDInsight** a vyberte svůj cluster.

2. Klikněte pravým tlačítkem na cluster HDInsight a **v Azure Portal vyberte Spravovat cluster**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Nabídka otázek a zpětná vazba ze sady Visual Studio

Dotazování na dotazy a nebo poskytnutí zpětné vazby ze sady Visual Studio:

1. V Průzkumník serveru vyberte **Azure**  >  **HDInsight**.

2. Klikněte pravým tlačítkem na **HDInsight** a vyberte **Fórum MSDN** , kde se budou klást dotazy, nebo **sdělte zpětnou** vazbu, která vám poskytne svůj názor.

## <a name="link-to-or-edit-a-cluster"></a>Propojení nebo úprava clusteru

> [!NOTE]
> V současné době je jediným typem clusteru HDInsight, ke kterému se dá připojit, typ podregistru.

Propojení clusteru HDInsight:

1. Klikněte pravým tlačítkem na **HDInsight** a pak vyberte **propojit cluster HDInsight** pro zobrazení dialogového okna **propojit cluster HDInsight** .

2. Do formuláře zadejte **adresu URL připojení** `https://CLUSTERNAME.azurehdinsight.net` . **Název clusteru** se při přechodu na jiné pole automaticky vyplní názvem v části adresy URL clusteru. Pak zadejte **uživatelské jméno** a **heslo** a pak vyberte **Další**.

    ![Propojení clusteru, HDInsight, sady Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Vyberte **Dokončit**. Pokud je propojení clusteru úspěšné, cluster se pak zobrazí v uzlu **HDInsight** .

Pokud chcete aktualizovat propojený cluster, klikněte pravým tlačítkem na cluster a vyberte **Upravit**. Pak můžete informace o clusteru aktualizovat.

![Úprava propojeného clusteru, HDInsight, sady Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Zkoumání propojených prostředků

V Průzkumníkovi serveru můžete zobrazit výchozí účet úložiště a všechny propojené účty úložiště. Pokud rozbalíte výchozí účet úložiště, uvidíte kontejnery na účtu úložiště. Jsou označeny jako výchozí účet úložiště a výchozí kontejner.

![Data Lake nástroje pro propojené prostředky sady Visual Studio v Průzkumník serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Klikněte pravým tlačítkem na kontejner a výběrem **Zobrazit kontejner** zobrazíte obsah kontejneru. Po otevření kontejneru můžete pomocí tlačítek na panelu nástrojů **aktualizovat** seznam obsahu, **nahrát objekt BLOB**, **Odstranit vybrané objekty blob**, **otevřít objekt BLOB** a stáhnout (**Uložit jako**) vybrané objekty blob.

![Seznam kontejnerů a operace objektů blob, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Spouštění interaktivních dotazů Apache Hive

[Apache Hive](https://hive.apache.org) je infrastruktura datového skladu postavená na Hadoop. Hive se používá k souhrnům dat, dotazům a analýzám. Pomocí nástrojů Data Lake pro Visual Studio můžete spouštět dotazy Hive ze sady Visual Studio. Další informace o podregistru najdete v tématu [co je Apache Hive a HiveQL ve službě Azure HDInsight?](hdinsight-use-hive.md).

[Interaktivní dotaz v Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) používá [podregistr v LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) v Apache Hive 2,1. Interaktivní dotaz přináší interaktivitu složitých dotazů ve stylu datového skladu u rozsáhlých uložených datových sad. Spouštění dotazů na podregistr u interaktivního dotazu je mnohem rychlejší než tradiční dávkové úlohy pro podregistr. 

> [!NOTE]  
> Interaktivní dotazy Hive můžete spouštět pouze po připojení ke clusteru [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

K zobrazení toho, co se nachází v rámci úlohy podregistru, můžete také použít nástroje Data Lake Tools for Visual Studio. Nástroje Data Lake pro Visual Studio shromažďují a zpřístupňují protokoly Yarn určitých úloh Hive.

V **Průzkumník serveru** vyberte **Azure**  >  **HDInsight** a vyberte svůj cluster.  Tento uzel je výchozím bodem v **Průzkumník serveru** pro následující oddíly.

### <a name="view-hivesampletable"></a>Zobrazení tabulky hivesampletable

Všechny clustery HDInsight mají výchozí ukázkovou tabulku podregistru s názvem `hivesampletable` .  

Z clusteru vyberte možnost **databáze podregistru**  >  **výchozí**  >  **hivesampletable**.

* Postup zobrazení `hivesampletable` schématu:

    Rozbalte **hivesampletable**. Zobrazí se názvy a datové typy `hivesampletable` sloupců.

* Zobrazení `hivesampletable` dat:

    Klikněte pravým tlačítkem na **hivesampletable** a vyberte **Zobrazit prvních 100 řádků**. Seznam výsledků 100 se zobrazí v okně **tabulka podregistru: hivesampletable** . Tato akce je stejná jako spuštění následujícího dotazu na podregistr pomocí ovladače rozhraní ODBC pro podregistr:

    `SELECT * FROM hivesampletable LIMIT 100`

    Počet řádků můžete přizpůsobit změnou **počtu řádků**; z rozevíracího seznamu můžete vybrat 50, 100, 200 nebo 1000 řádky.

### <a name="create-hive-tables"></a>Vytváření tabulek Hive

K vytvoření tabulky Hive můžete použít grafické rozhraní (GUI) nebo dotazy Hive. Informace o použití dotazů na podregistry najdete v tématu [Vytvoření a spuštění dotazů na podregistr](#create-and-run-hive-queries).

1. Z clusteru vyberte možnost **databáze podregistru**  >  **výchozí**.

2. Klikněte pravým tlačítkem na **výchozí** a vyberte **vytvořit tabulku**.

3. Konfigurace tabulky.

4. Kliknutím na tlačítko **vytvořit tabulku** odešlete úlohu, která vytvoří novou tabulku podregistru.

    ![Vytvoření okna tabulky, podregistr, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Vytvoření a spuštění dotazů na podregistr

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad-hoc
* Vytvoření aplikace Hive

#### <a name="create-an-ad-hoc-query"></a>Vytvoření dotazu ad-hoc

Vytvoření a spuštění dotazu ad-hoc:

1. Klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz, a vyberte **zapsat dotaz na podregistr**.  

2. Zadejte dotaz na podregistr.

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM` , IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF.

    ![IntelliSense příklad 1, dotaz ad hoc v podregistru, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense příklad 2, dotaz ad hoc v podregistru, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

    Tady je ukázkový dotaz, který můžete použít:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Vyberte režim spuštění:

    * **Interaktivní**  

        V prvním rozevíracím seznamu vyberte možnost **interaktivní** a potom vyberte možnost **Spustit**.

        ![Interaktivní režim, dotaz ad-hoc v podregistru, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        V prvním rozevíracím seznamu zvolte **Batch** a pak vyberte **Odeslat**. Případně vyberte ikonu rozevíracího seznamu vedle možnosti **Odeslat** a zvolte možnost **Upřesnit**.

        ![Dávkový režim, dotaz ad-hoc v podregistru, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Pokud zaškrtnete možnost Upřesnit odeslání, zobrazí se dialogové okno **Odeslat skript** . Nakonfigurujte **název úlohy**, **argumenty**, **Další konfigurace** a **stavový adresář** pro skript.

        ![Dialogové okno pro odeslání skriptu, dotaz ad hoc pro podregistr, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Do clusterů interaktivních dotazů nemůžete odesílat dávky.  Je nutné použít interaktivní režim.

#### <a name="create-a-hive-application"></a>Vytvoření aplikace Hive

Vytvoření a spuštění řešení Hive:

1. Z panelu nabídek vyberte možnost **soubor**  >  **Nový**  >  **projekt**.

2. V okně **vytvořit nový projekt** vyberte vyhledávací pole a zadejte **podregistr**. Pak zvolte možnost **aplikace podregistru** a vyberte možnost **Další**.

3. V okně **Konfigurovat nový projekt** zadejte **název projektu**, vyberte nebo vytvořte **umístění** projektu a pak vyberte **vytvořit**.

    ![Nová aplikace pro podregistr, konfigurace nového okna projektu, Visual Studio HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. V **Průzkumníku řešení** dvojím kliknutím otevřete skript **Script.hql**.

### <a name="view-job-summary-and-output"></a>Zobrazení souhrnu a výstupu úlohy

Souhrn úlohy se mírně liší mezi **Batch** a **interaktivním** režimem.

![Okna souhrnu úloh podregistru, Batch a interaktivní režim, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Pomocí ikony **aktualizovat** aktualizujte stav, dokud se stav úlohy nezmění na **dokončeno**.  

* V podrobnostech o úloze z **dávkového** režimu vyberte odkazy v dolní části, kde můžete **Zobrazit dotaz na úlohu**, **výstup úlohy** nebo **protokol úlohy** nebo **Zobrazit záznamy příze**.

* Podrobnosti o úloze z **interaktivního** režimu naleznete v podoknech **výstupní** a **HiveServer2 výstupní** .

    ![Výstup interaktivních úloh v podregistru, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Zobrazit graf úlohy

V současné době jsou grafy úloh zobrazeny pouze pro úlohy podregistru, které používají tez jako spouštěcí modul.  Informace o povolení tez najdete v tématu [co je Apache Hive a HiveQL ve službě Azure HDInsight?](hdinsight-use-hive.md).  Viz také [Apache tez místo zmenšování mapy použijte](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Chcete-li zobrazit všechny operátory uvnitř vrcholu, dvakrát klikněte na vrcholy grafu úlohy. Můžete také ukázat na konkrétní operátor a zobrazit tak další podrobnosti o tomto operátoru.

I v případě, že je tez zadán jako spouštěcí modul, nemusí se graf úlohy zobrazit, pokud se nespustí žádná aplikace TEZ.  K této situaci může dojít, protože úloha neobsahuje příkazy DML. Nebo vzhledem k tomu, že příkazy DML mohou vracet bez spuštění aplikace v TEZ. Například `SELECT * FROM table1` nespustí aplikaci TEZ.

![Apache Hive graf úloh, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Zobrazit podrobnosti spuštění úlohy

Z grafu úlohy můžete vybrat **Podrobnosti spuštění úlohy** a získat tak strukturované a vizuální informace pro úlohy podregistru. Můžete také získat další podrobnosti o úloze. Pokud dojde k problémům s výkonem, můžete pomocí tohoto zobrazení získat další podrobnosti o problému. Můžete například načíst informace o tom, jak každý úkol pracuje, a podrobné informace o jednotlivých úlohách (čtení a zápis dat, naplánovaný/počáteční/koncový čas atd.). Na základě vizualizovaných informací můžete tyto informace využít k ladění konfigurací úloh nebo architektury systémy.

![Okno zobrazení spuštění úlohy, Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Zobrazení úloh Hive

Můžete zobrazit dotazy úlohy, výstup úlohy, protokoly úlohy a protokoly Yarn pro úlohy Hive.

V nejnovější verzi nástrojů uvidíte, co se nachází uvnitř vašich úloh v podregistru, pomocí shromažďování a zpřístupněních protokolů příze. Protokol Yarn vám může pomoci prozkoumat problémy s výkonem. Další informace o tom, jak HDInsight shromažďuje protokoly příze, najdete v tématu [přístup k protokolům aplikací Apache HADOOP nitě](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Zobrazení úloh Hive:

1. Klikněte pravým tlačítkem na cluster HDInsight a vyberte **Zobrazit úlohy**.

    ![Zobrazení úloh, Apache Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Zobrazí se seznam úloh Hive, které se v clusteru spustily.  

2. Vyberte úlohu. V okně **Souhrn úlohy podregistr** vyberte jeden z následujících odkazů:
    - **Dotaz úlohy**
    - **Výstup úlohy**
    - **Protokol úlohy**  
    - **Protokol příze**

## <a name="run-apache-pig-scripts"></a>Spustit skripty Apache prasete

1. Z panelu nabídek vyberte možnost **soubor**  >  **Nový**  >  **projekt**.

2. V okně **Start** vyberte vyhledávací pole a zadejte **prase**. Pak vyberte **aplikace pro prase** a vyberte **Další**.

3. V okně **Konfigurovat nový projekt** zadejte **název projektu** a vyberte nebo vytvořte **umístění** projektu. Potom vyberte **Vytvořit**.

4. V podokně **Průzkumník řešení** IDE poklikejte na **script. prase** a otevře se skript.

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy

* Opravili jsme problém, kdy se nezobrazovaly výsledky začínající hodnotami null. Pokud vás tento problém blokuje, kontaktujte tým podpory.

* Skript HQL, který Visual Studio vytvoří, je kódovaný v závislosti na nastavení místní oblasti uživatele. Skript se nespustí správně, pokud ho nahrajete do clusteru jako binární soubor.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak se pomocí balíčku nástrojů Data Lake pro Visual Studio připojit ke clusterům HDInsight ze sady Visual Studio. Také jste se naučili spustit dotaz Hive. 

* [Spouštění dotazů Apache Hivu pomocí Nástrojů Data Lake pro Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](hdinsight-use-hive.md)
* [Vytvoření clusteru Apache Hadoop – šablona](apache-hadoop-linux-tutorial-get-started.md)
* [Odesílání úloh Apache Hadoop v HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analýza dat Twitteru pomocí Apache Hive a Apache Hadoop v HDInsight](../hdinsight-analyze-twitter-data-linux.md)