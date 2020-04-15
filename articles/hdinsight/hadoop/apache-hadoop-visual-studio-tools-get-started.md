---
title: Apache Hadoop & Nástroje datového jezera Visual Studia – Azure HDInsight
description: Přečtěte si, jak nainstalovat a používat nástroje data lake pro Visual Studio. Pomocí nástroje se můžete připojit ke clusterům Apache Hadoop v Azure HDInsight a pak spustit dotazy Hive.
keywords: hadoop tools, hive query, visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 7504826f267d717f30c5e88621578412c744e5f9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383507"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Použití nástrojů data lake pro Visual Studio pro připojení k Azure HDInsight a spouštění dotazů Apache Hive

Přečtěte si, jak používat nástroje Microsoft Azure Data Lake a Stream Analytics pro Visual Studio (Nástroje pro data jezera). Pomocí nástroje se můžete připojit ke [clusterům Apache Hadoop v Azure HDInsight](apache-hadoop-introduction.md) a odesílat dotazy Hive.  

Další informace o používání HDInsightu najdete [v tématu Začínáme s HDInsightem](apache-hadoop-linux-tutorial-get-started.md).  

Další informace o připojení k Apache Storm najdete [v tématu Vývoj topologií Jazyka C# pro Apache Storm pomocí nástrojů Data Lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pomocí nástrojů Data Lake pro Visual Studio můžete přistupovat ke službě Azure Data Lake Analytics i HDInsight. Informace o nástrojích Data Lake najdete v tématu [Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento článek a použít nástroje data lake pro Visual Studio, budete potřebovat následující položky:

* Cluster Azure HDInsight. Pokud chcete vytvořit cluster HDInsight, [přečtěte si, že začínáme pomocí Apache Hadoop v Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Chcete-li spustit interaktivní dotazy Apache Hive, potřebujete cluster [interaktivních dotazů HDInsight.](../interactive-query/apache-interactive-query-get-started.md)  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). Visual [Studio Community edition](https://visualstudio.microsoft.com/vs/community/) je zdarma. Zde uvedené pokyny jsou pro [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Data Lake Tools pro Visual Studio  

Postupujte podle příslušných pokynů k instalaci nástrojů Data Lake Tools pro vaši verzi sady Visual Studio:

* Pro Visual Studio 2017 nebo Visual Studio 2019:

    Během instalace Visual Studia nezapomeňte zahrnout vývojové úlohy **Azure** nebo úlohy **úložiště dat a zpracování.**  

    Existující instalace sady Visual Studio najdete na panelu nabídek IDE a vyberte **Nástroje** > **získat nástroje a funkce** a otevřete Instalační službu sady Visual Studio. Na kartě Úlohy vyberte alespoň **vývojové** **úlohy** Azure (v části Web **& Cloud).** Nebo vyberte úlohu **ukládání a zpracování dat** (v části Jiné sady **nástrojů).**

  ![Výběr úloh, Instalační program sady Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Pro Visual Studio 2015:

    [Stáhnout Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Zvolte verzi nástrojů Data Lake, která se shoduje s vaší verzí sady Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualizace nástrojů datového jezera pro Visual Studio  

Dále se ujistěte, že aktualizujete nástroje Data Lake Tools na nejnovější verzi.

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **Pokračovat bez kódu**.

3. Na panelu nabídek IDE sady Visual Studio zvolte **Rozšíření** > **Spravovat rozšíření**.

4. V dialogovém okně **Spravovat rozšíření** rozbalte uzel **Aktualizace.**

5. Pokud seznam dostupných aktualizací zahrnuje **Azure Data Lake a Stream Analytic Tools**, vyberte ho. Pak vyberte jeho **tlačítko Aktualizovat.** Po zobrazení dialogového okna **Stáhnout a nainstalovat** a zmizí Visual Studio přidá rozšíření Azure Data Lake a Stream Analytic **Tools** do plánu aktualizací.

6. Zavřete všechna okna sady Visual Studio. Zobrazí se dialogové okno Instalační služba **VSIX.**

7. Chcete-li si přečíst licenční podmínky, vyberte **Možnost Licence** a pak se klepnutím na **tlačítko Zavřít** vraťte do dialogového okna Instalační služba **VSIX.**

8. Vyberte **Změnit**. Spustí se instalace aktualizace rozšíření. Po chvíli se dialogové okno změní tak, aby se ukázalo, že se provádí provádění úprav. Vyberte **Zavřít**a restartujte aplikaci Visual Studio a dokončete instalaci.

> [!NOTE]  
> Pro připojení ke clusterům Interactive Query a spouštění interaktivních dotazů Hive můžete použít pouze nástroje Data Lake verze 2.3.0.0 nebo novější.

## <a name="connect-to-azure-subscriptions"></a>Připojení k předplatným služby Azure

Pomocí nástrojů Data Lake Tools pro Visual Studio se můžete připojit ke clusterům HDInsight, provést některé základní operace správy a spustit dotazy Hive.

> [!NOTE]  
> Informace o připojení k obecnému clusteru Hadoop naleznete v tématu [Jak psát a odesílat dotazy Hive pomocí sady Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

Připojení k předplatnému Azure:

1. Otevřete sadu Visual Studio.

2. V okně **Start** vyberte **Pokračovat bez kódu**.

3. Na řádku nabídek IDE zvolte **Zobrazit** > **Průzkumníka serveru**.

4. V **Průzkumníkovi serveru**klikněte pravým tlačítkem myši na **Azure**, vyberte Připojit **k předplatnému Microsoft Azure**a dokončete proces ověřování. Z **Průzkumníka serveru**rozbalte **Azure** > **HDInsight** a zobrazte seznam existujících clusterů HDInsight.

5. Pokud nemáte žádné clustery, vytvořte je pomocí portálu Azure, Azure PowerShellu nebo sady HDInsight SDK. Další informace naleznete v tématu [Nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Seznam clusterů HDInsight, Průzkumník serveru, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Rozbalte cluster služby HDInsight. Cluster obsahuje uzly pro **databáze Hive**. Také výchozí účet úložiště, všechny další propojené účty úložiště a **Hadoop Service Log**. Entity můžete dále rozšířit.

Po připojení k předplatnému Azure můžete provádět následující úlohy.

### <a name="connect-to-azure-from-visual-studio"></a>Připojení k Azure z Visual Studia

Připojení k webu Azure Portal ze sady Visual Studio:

1. V **Průzkumníkovi serveru**rozbalte **Azure** > **HDInsight** a vyberte svůj cluster.

2. Klikněte pravým tlačítkem myši na cluster HDInsight a vyberte **Spravovat cluster na webu Azure Portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Nabízí otázky a zpětnou vazbu z Visual Studia

Chcete-li klást otázky a nebo poskytnout zpětnou vazbu z Visual Studia:

1. Z Průzkumníka serveru zvolte **Azure** > **HDInsight**.

2. Klikněte pravým tlačítkem myši na **HDInsight** a vyberte buď **Fórum MSDN,** kde chcete klást otázky, nebo **poskytnout zpětnou vazbu,** abyste poskytli zpětnou vazbu.

## <a name="link-to-or-edit-a-cluster"></a>Propojení nebo úprava clusteru

> [!NOTE]
> V současné době je jediným typem clusteru HDInsight, na který můžete vytvořit odkaz, typ Hive.

Propojení clusteru HDInsight:

1. Klikněte pravým tlačítkem myši na **HDInsight**a pak vyberte **Propojit cluster HDInsight,** abyste zobrazili dialogové okno **Propojit cluster HDInsight.**

2. Zadejte **adresu URL** `https://CLUSTERNAME.azurehdinsight.net`připojení do formuláře . **Název clusteru** automaticky vyplní část názvu clusteru adresy URL, když přejdete do jiného pole. Potom zadejte **uživatelské jméno** a **heslo**a vyberte **další**.

    ![Propojení clusteru, HDInsightu, Visual Studia](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Vyberte **Finish** (Dokončit). Pokud je propojení clusteru úspěšné, je cluster uveden pod uzlem **HDInsight.**

Chcete-li aktualizovat propojený cluster, klepněte pravým tlačítkem myši na cluster a vyberte příkaz **Upravit**. Poté můžete aktualizovat informace o clusteru.

![Úprava propojeného clusteru, HDInsightu a Visual Studia](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Zkoumání propojených prostředků

V Průzkumníkovi serveru můžete zobrazit výchozí účet úložiště a všechny propojené účty úložiště. Pokud rozbalíte výchozí účet úložiště, uvidíte kontejnery na účtu úložiště. Jsou označeny jako výchozí účet úložiště a výchozí kontejner.

![Nástroje datového jezera pro propojené prostředky sady Visual Studio v Průzkumníkovi serveru](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Klikněte pravým tlačítkem myši na kontejner a výběrem **možnosti Zobrazit kontejner** zobrazíte obsah kontejneru. Po otevření kontejneru můžete pomocí tlačítek panelu nástrojů **aktualizovat** seznam obsahu, **Nahrát objekt Blob**, **Odstranit vybrané objekty BLOB**, **Otevřít objekt Blob**a stáhnout (**Uložit jako**) vybrané objekty BLOB.

![Seznam kontejnerů a operací objektů blob, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Spuštění interaktivních dotazů Apache Hive

[Apache Hive](https://hive.apache.org) je infrastruktura datového skladu postavená na Hadoop. Hive se používá k souhrnům dat, dotazům a analýzám. Pomocí nástrojů Data Lake pro Visual Studio můžete spouštět dotazy Hive ze sady Visual Studio. Další informace o Hive najdete v tématu [Co je Apache Hive a HiveQL na Azure HDInsight?](hdinsight-use-hive.md).

[Interaktivní dotaz v Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) používá [Hive na LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) v Apache Hive 2.1. Interaktivní dotaz přináší interaktivitu do složitých dotazů ve stylu datového skladu na velkých uložených datových sadách. Spuštění dotazů Hive v interaktivním dotazu je mnohem rychlejší než tradiční dávkové úlohy Hive. 

> [!NOTE]  
> Interaktivní dotazy Hive můžete spouštět pouze po připojení ke clusteru [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Pomocí nástrojů Data Lake Tools pro Visual Studio můžete také zjistit, co je uvnitř úlohy Hive. Nástroje Data Lake pro Visual Studio shromažďují a zpřístupňují protokoly Yarn určitých úloh Hive.

Z **Průzkumníka serveru**zvolte **Azure** > **HDInsight** a vyberte svůj cluster.  Tento uzel je výchozím bodem v **Průzkumníkovi serveru** pro oddíly, které chcete sledovat.

### <a name="view-hivesampletable"></a>Zobrazení tabulky hivesampletable

Všechny clustery HDInsight mají výchozí ukázkovou tabulku Hive nazvanou `hivesampletable`.  

Ve vašem clusteru zvolte > **Výchozí** > **hivesampletable** **Databáze Hive**.

* Zobrazení schématu: `hivesampletable`

    Rozbalte **hivesampletable**. Zobrazí se názvy `hivesampletable` a datové typy sloupců.

* Zobrazení `hivesampletable` dat:

    Klepněte pravým tlačítkem myši na **položku hivesampletable**a vyberte **zobrazit prvních 100 řádků**. Seznam 100 výsledků se zobrazí v **tabulce Hive: hivesampletable** okno. Tato akce je ekvivalentní spuštění následujícího dotazu Hive pomocí ovladače ODBC Hive:

    `SELECT * FROM hivesampletable LIMIT 100`

    Počet řádků můžete přizpůsobit změnou **počet řádků**; můžete vybrat 50, 100, 200 nebo 1000 řádků z rozevíracího seznamu.

### <a name="create-hive-tables"></a>Vytváření tabulek Hive

K vytvoření tabulky Hive můžete použít grafické rozhraní (GUI) nebo dotazy Hive. Informace o používání dotazů Hive naleznete v [tématu Vytvoření a spuštění dotazů Hive](#create-and-run-hive-queries).

1. V clusteru zvolte**výchozí** **databáze** > Hive .

2. Klepněte pravým tlačítkem myši na **výchozí** **položku**a vyberte příkaz Vytvořit tabulku .

3. Konfigurace tabulky.

4. Kliknutím na tlačítko **Vytvořit tabulku** odešlete úlohu, která vytvoří novou tabulku Hive.

    ![Vytvořit okno tabulky, hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Vytváření a spouštění dotazů Hive

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad-hoc
* Vytvoření aplikace Hive

#### <a name="create-an-ad-hoc-query"></a>Vytvoření dotazu ad hoc

Vytvoření a spuštění dotazu ad-hoc:

1. Klepněte pravým tlačítkem myši na cluster, ve kterém chcete dotaz spustit, a vyberte **příkaz Napsat dotaz hive**.  

2. Zadejte dotaz Hive.

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM`, technologie IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF.

    ![Příklad technologie IntelliSense 1, dotaz Hive ad-hoc, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Příklad Technologie IntelliSense 2, dotaz Hive ad-hoc, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

    Zde je ukázkový dotaz, který můžete použít:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Zvolte režim spuštění:

    * **Interaktivní**  

        V prvním rozevíracím seznamu zvolte **Interaktivní**a pak vyberte **Spustit**.

        ![Interaktivní režim, dotaz Ad-hoc v Úlu, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        V prvním rozevíracím seznamu zvolte **Dávka**a pak vyberte **Odeslat**. Nebo vyberte ikonu rozevíracího panelu vedle **možnosti Odeslat** a zvolte **Upřesnit**.

        ![Dávkový režim, dotaz AD-hoc, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Pokud vyberete možnost rozšířené ho odeslat, zobrazí se dialogové okno **Odeslat skript.** Nakonfigurujte **název úlohy**, **argumenty**, **další konfigurace**a **stavový adresář** skriptu.

        ![Dialogové okno Odeslat skript, dotaz AD-hoc, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Dávky nelze odeslat do clusterů interaktivních dotazů.  Je nutné použít interaktivní režim.

#### <a name="create-a-hive-application"></a>Vytvoření aplikace Hive

Vytvoření a spuštění řešení Hive:

1. Na řádku nabídek zvolte **Soubor** > **nového** > **projektu**.

2. V okně **Vytvořit nový projekt** vyberte vyhledávací pole a zadejte **Podregistr**. Pak zvolte **Aplikace Hive** a vyberte **Další**.

3. V okně **Konfigurovat nový projekt** zadejte název **projektu**, vyberte nebo vytvořte **umístění**projektu a pak vyberte **Vytvořit**.

    ![Nová aplikace Hive, Konfigurace nového okna projektu, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. V **Průzkumníku řešení** dvojím kliknutím otevřete skript **Script.hql**.

### <a name="view-job-summary-and-output"></a>Zobrazit souhrn a výstup úlohy

Souhrn úlohy se mezi **dávkou** a **interaktivním** režimem mírně liší.

![Souhrn úloh y úlu, dávkový a interaktivní režim, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Pomocí ikony **Aktualizovat** aktualizujte stav, dokud se stav úlohy nezmění na **Dokončeno**.  

* Podrobnosti o úloze z **dávkového** režimu vyberte odkazy v dolní části, abyste viděli **dotaz na úlohu**, **výstup úlohy**nebo **protokol úloh**nebo **zobrazit protokoly příze**.

* Podrobnosti o úloze z **interaktivního** režimu najdete v podokně **Výstup** a **Výstup HiveServer2.**

    ![Výstup interaktivní úlohy Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Zobrazit graf úlohy

V současné době jsou grafy úloh zobrazeny pouze pro úlohy Hive, které používají Tez jako modul provádění.  Informace o povolení Tez, najdete [v tématu Co je Apache Hive a HiveQL na Azure HDInsight?](hdinsight-use-hive.md).  Viz [také, Použijte Apache Tez místo Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Chcete-li zobrazit všechny operátory uvnitř vrcholu, poklepejte na vrcholy grafu úlohy. Můžete také ukázat na konkrétní operátor a zobrazit tak další podrobnosti o tomto operátoru.

I v případě, že Tez je zadán jako modul provádění, graf úlohy se nemusí zobrazit, pokud je spuštěna žádná aplikace Tez.  K této situaci může dojít, protože úloha neobsahuje příkazy DML. Nebo protože dml příkazy můžete vrátit bez spuštění aplikace Tez. Například `SELECT * FROM table1` nespustí aplikaci Tez.

![Graf úloh Apache Hive, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Zobrazit podrobnosti o spuštění úlohy

V grafu úlohy můžete vybrat **detail provádění úloh,** chcete-li získat strukturované a vizualizované informace pro úlohy Hive. Můžete také získat další podrobnosti o práci. Pokud dojde k problémům s výkonem, můžete pomocí tohoto zobrazení získat další podrobnosti o problému. Můžete například načíst informace o tom, jak jednotlivé úkoly fungují, a podrobné informace o jednotlivých úkolech (čtení/zápis dat, čas zahájení/ukončení a další). Na základě vizualizovaných informací můžete tyto informace využít k ladění konfigurací úloh nebo architektury systémy.

![Okno Zobrazení spuštění úlohy, Nástroje aplikace Data Lake Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Zobrazení úloh Hive

Můžete zobrazit dotazy úlohy, výstup úlohy, protokoly úlohy a protokoly Yarn pro úlohy Hive.

V nejnovější verzi nástrojů můžete vidět, co je uvnitř úloh Hive, a to shromažďováním a navařováním protokolů příze. Protokol Yarn vám může pomoci prozkoumat problémy s výkonem. Další informace o tom, jak HDInsight shromažďuje protokoly příze, naleznete [v tématu Access Apache Hadoop YARN application logs](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Zobrazení úloh Hive:

1. Klikněte pravým tlačítkem myši na cluster HDInsight a vyberte **zobrazit úlohy**.

    ![Zobrazit úlohy, Apache Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Zobrazí se seznam úloh Hive, které se v clusteru spustily.  

2. Vyberte úlohu. V okně **Souhrn úloh úlu** vyberte jeden z následujících odkazů:
    - **Dotaz úlohy**
    - **Výstup úlohy**
    - **Protokol úlohy**  
    - **Protokol příze**

## <a name="run-apache-pig-scripts"></a>Spusťte skripty Apache Pig

1. Na řádku nabídek zvolte **Soubor** > **nového** > **projektu**.

2. V okně **Start** vyberte vyhledávací pole a zadejte **Prase**. Pak vyberte **Pig Aplikace** a vyberte **Další**.

3. V okně **Konfigurovat nový projekt** zadejte název **projektu**a vyberte nebo vytvořte **umístění** projektu. Pak vyberte **Vytvořit**.

4. V podokně **Průzkumník řešení** IDE otevřete skript poklepáním na soubor **Script.pig.**

## <a name="feedback-and-known-issues"></a>Zpětná vazba a známé problémy

* Opravili jsme problém, kdy se nezobrazovaly výsledky začínající hodnotami null. Pokud vás tento problém blokuje, kontaktujte tým podpory.

* Skript HQL, který vytvoří visual studio je kódován, v závislosti na nastavení místní oblasti uživatele. Skript se nespustí správně, pokud ho nahrajete do clusteru jako binární soubor.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak se pomocí balíčku nástrojů Data Lake pro Visual Studio připojit ke clusterům HDInsight ze sady Visual Studio. Také jste se naučili spustit dotaz Hive. 

* [Spouštění dotazů Apache Hivu pomocí Nástrojů Data Lake pro Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Co je Apache Hive a HiveQL na Azure HDInsight?](hdinsight-use-hive.md)
* [Vytvoření clusteru Apache Hadoop – šablona](apache-hadoop-linux-tutorial-get-started.md)
* [Odeslat pracovní příležitosti apache hadoop v HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analyzujte data twitteru pomocí Apache Hive a Apache Hadoop na HDInsight](../hdinsight-analyze-twitter-data-linux.md)
