---
title: Nástroje Apache Hive & Data Lake pro Visual Studio – Azure HDInsight
description: Zjistěte, jak pomocí nástrojů Data Lake pro Visual Studio spouštět dotazy Apache Hive s Apache Hadoop na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687800"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštění dotazů Apache Hivu pomocí Nástrojů Data Lake pro Visual Studio

Přečtěte si, jak používat nástroje Data Lake pro Visual Studio k dotazování Apache Hive. Nástroje Data Lake umožňují snadno vytvářet, odesílat a monitorovat dotazy Hive apache hadoop na Azure HDInsight.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Informace o vytvoření této položky najdete [v tématu Vytvoření clusteru Apache Hadoop v Azure HDInsight pomocí šablony Správce prostředků](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). Kroky v tomto článku používají Visual Studio 2019.

* Nástroje HDInsight pro Visual Studio nebo nástroje Azure Data Lake pro Visual Studio. Informace o instalaci a konfiguraci nástrojů naleznete v tématu [Instalace nástrojů data lake pro visual studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Spuštění dotazů Apache Hive pomocí sady Visual Studio

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytvořte dotazy ad-hoc.
* Vytvořte aplikaci Hive.

### <a name="create-an-ad-hoc-hive-query"></a>Vytvoření dotazu ad-hoc Hive

Ad hoc dotazy lze provádět v **dávkovém** nebo **interaktivním** režimu.

1. Spusťte **Visual Studio** a vyberte **Pokračovat bez kódu**.

2. V **Průzkumníkovi serveru**klikněte pravým tlačítkem na **Azure**, vyberte Připojit **k předplatnému Microsoft Azure...** a dokončete proces přihlášení.

3. Rozbalte **hdinsight**, klikněte pravým tlačítkem myši na cluster, ve kterém chcete dotaz spustit, a pak vyberte **příkaz Write a Hive Query**.

4. Zadejte následující dotaz na podregistru:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Vyberte **Provést**. Výchozí režim spuštění je **interaktivní**.

    ![Spuštění interaktivního dotazu Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Chcete-li spustit stejný dotaz v **dávkovém** režimu, přepněte rozevírací seznam z **interaktivní** na **dávkový**. Tlačítko spuštění se změní z **příkazu Execute** na **Submit**.

    ![Odeslat dávkový dotaz Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM`, technologie IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF. IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

7. Na panelu nástrojů dotazu (oblast pod kartou dotazu a nad textem dotazu) vyberte **Odeslat**nebo vyberte šipku pro stažení vedle **Odeslat** a z rozbalovacího seznamu zvolte **Upřesnit.** Pokud vyberete druhou možnost,

8. Pokud jste vybrali možnost rozšířeného odeslání, nakonfigurujte **název úlohy**, **argumenty**, **další konfigurace**a **stavový adresář** v dialogovém okně **Odeslat skript.** Pak vyberte **Odeslat**.

    ![Dialogové okno Odeslat skript, dotaz HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Vytvoření aplikace Hive

Chcete-li spustit dotaz Hive vytvořením aplikace Hive, postupujte takto:

1. Otevřete **visual studio**.

2. V okně **Start** vyberte **Vytvořit nový projekt**.

3. V okně **Vytvořit nový projekt** zadejte do pole Hledat **šablony** *Podregistr*. Pak zvolte **Aplikace Hive** a vyberte **Další**.

4. V okně **Konfigurovat nový projekt** zadejte název **projektu**, vyberte nebo vytvořte **umístění** pro nový projekt a pak vyberte **Vytvořit**.

5. Otevřete soubor **Script.hql,** který je vytvořen pomocí tohoto projektu, a vložte do následujících příkazů HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Tyto příkazy provést následující akce:

    * `DROP TABLE`: Odstraní tabulku, pokud existuje.

    * `CREATE EXTERNAL TABLE`: Vytvoří novou "externí" tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v Hive. (Data zůstanou v původním umístění.)

        > [!NOTE]  
        > Externí tabulky by měly být použity, pokud očekáváte, že podkladová data budou aktualizována externím zdrojem, jako je například úloha MapReduce nebo služba Azure.
        >
        > Uvolněním externí tabulky **neodstraníte** data, pouze definice tabulky.

    * `ROW FORMAT`: Sděluje Hive, jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělena mezerou.

    * `STORED AS TEXTFILE LOCATION`: Sděluje Hive, že data jsou uložena v *příkladu/datovém* adresáři a že jsou uložena jako text.

    * `SELECT`: Vybere počet všech řádků, `t4` ve `[ERROR]`kterých sloupec obsahuje hodnotu . Tento příkaz vrátí `3`hodnotu , protože tuto hodnotu obsahují tři řádky.

    * `INPUT__FILE__NAME LIKE '%.log'`: Sděluje Hive, aby vracel data pouze ze souborů končících na .log. Tato klauzule omezuje hledání na soubor *sample.log,* který obsahuje data.

6. Na panelu nástrojů souboru dotazu (který má podobný vzhled jako panel nástrojů dotazu ad-hoc) vyberte cluster HDInsight, který chcete pro tento dotaz použít. Pak změňte **interaktivní** **dávky** (v případě potřeby) a vyberte **Odeslat** spustit příkazy jako úlohu Hive.

   Zobrazí se **souhrn úloh y Hive** a zobrazí se informace o spuštěné úloze. Pomocí odkazu **Aktualizovat** aktualizujte informace o úloze, dokud se **stav úlohy** nezmění na **Dokončeno**.

   ![Souhrn úloh y Hive, aplikace Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Chcete-li zobrazit výstup této úlohy, vyberte **možnost Výstup úlohy.** Zobrazí `[ERROR] 3`, což je hodnota vrácená tímto dotazem.

### <a name="additional-example"></a>Další příklad

Následující příklad závisí na `log4jLogs` tabulce vytvořené v předchozím postupu [Vytvořit aplikaci Hive](#create-a-hive-application).

1. V **Průzkumníkovi serveru**klepněte pravým tlačítkem myši na cluster a vyberte **příkaz Napsat hive dotaz**.

2. Zadejte následující dotaz na podregistru:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provést následující akce:

    * `CREATE TABLE IF NOT EXISTS`: Vytvoří tabulku, pokud ještě neexistuje. Vzhledem `EXTERNAL` k tomu, že klíčové slovo se nepoužívá, tento příkaz vytvoří vnitřní tabulku. Interní tabulky jsou uloženy v datovém skladu Hive a jsou spravovány Hive.

        > [!NOTE]  
        > Na `EXTERNAL` rozdíl od tabulek, uvolněním vnitřní tabulky také odstraní podkladová data.

    * `STORED AS ORC`: Ukládá data v *optimalizovaném sloupcovém* (ORC) formátu. ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.

    * `INSERT OVERWRITE ... SELECT`: Vybere řádky `log4jLogs` z tabulky, které obsahují `[ERROR]`, `errorLogs` a pak vloží data do tabulky.

3. V případě potřeby změňte **možnost Interaktivní** na **dávku** a vyberte možnost **Odeslat**.

4. Chcete-li ověřit, zda úloha tabulku vytvořila, přejděte na **Průzkumníka serveru** a rozbalte **Azure** > **HDInsight**. Rozbalte cluster HDInsight a potom rozbalte**výchozí** **databáze** > Hive . **ErrorLogs** tabulka a **log4jLogs** tabulka jsou uvedeny.

## <a name="next-steps"></a>Další kroky

Jak můžete vidět, nástroje HDInsight pro Visual Studio poskytují snadný způsob, jak pracovat s dotazy Hive na HDInsight.

* Obecné informace o Hive v HDInsight najdete v tématu [Co je Apache Hive a HiveQL na Azure HDInsight?](hdinsight-use-hive.md)

* Informace o dalších způsobech práce s Hadoopem na HDInsight najdete v tématu [Použití MapReduce v Apache Hadoop na HDInsightu.](hdinsight-use-mapreduce.md)

* Další informace o nástrojích HDInsight pro Visual Studio najdete v tématu[Použití nástrojů data lake pro Visual Studio pro připojení k Azure HDInsight a spuštění dotazů Apache Hive.](apache-hadoop-visual-studio-tools-get-started.md)
