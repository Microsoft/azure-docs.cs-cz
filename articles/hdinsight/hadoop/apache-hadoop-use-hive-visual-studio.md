---
title: Apache Hive s Data Lake Tools for Visual Studio – Azure HDInsight
description: Naučte se používat nástroje Data Lake pro Visual Studio ke spouštění dotazů Apache Hive s Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 129f200bc9f61d70f4403b1154978d57e09fee26
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917488"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštění dotazů Apache Hive pomocí nástrojů Data Lake pro Visual Studio

Naučte se používat nástroje Data Lake pro Visual Studio k dotazování Apache Hive. Nástroje Data Lake umožňují snadno vytvářet, odesílat a monitorovat dotazy na podregistry pro Apache Hadoop v Azure HDInsight.

## <a id="prereq"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (jedna z následujících verzí):

    * Visual Studio 2015, 2017 (libovolná edice)
    * Visual Studio 2013 komunita/Professional/Premium/Ultimate s aktualizací Update 4

* Nástroje HDInsight pro Visual Studio nebo nástroje pro Azure Data Lake pro Visual Studio. Informace o instalaci a konfiguraci nástrojů najdete v tématu [Začínáme s nástroji Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md) .

## <a id="run"></a>Spouštění dotazů Apache Hive pomocí sady Visual Studio

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad hoc
* Vytvoření aplikace Hive

### <a name="ad-hoc"></a>Ad hoc

Ad hoc dotazy se dají spustit buď v **dávce** , nebo v **interaktivním** režimu.

1. Otevřete **Visual Studio**.

2. Z **Průzkumník serveru**přejděte na **Azure** > **HDInsight**.

3. Rozbalte **HDInsight**a klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz, a pak vyberte **zapsat dotaz na podregistr**.

4. Zadejte následující dotaz na podregistr:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Vyberte **Provést**. Všimněte si, že režim spuštění je nastaven na hodnotu **Interactive**.

    ![Snímek spouštění interaktivních dotazů Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Pokud chcete spustit stejný dotaz v režimu **dávky** , přepněte rozevírací seznam z **Interactive** do **Batch**. Všimněte si, že tlačítko spuštění se změní z příkazu **Spustit** na **odeslání**.

    ![Snímek odesílání dotazu Hive](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM`, IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF. IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Vyberte **Odeslat** nebo **Odeslat (rozšířené)** .

   Pokud jste použili možnost rozšířeného odeslání, nakonfigurujte pro skript **Název úlohy**, **Argumenty**, **Další konfigurace** a **Stavový adresář**:

    ![Snímek obrazovky s dotazem Hive v HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Odeslání dotazů")

### <a name="hive-application"></a>Aplikace podregistru

1. Otevřete **Visual Studio**.

2. Z řádku nabídek přejděte na **soubor** > **Nový** > **projekt**.

3. V okně **Nový projekt** přejděte do části **šablony** > **Azure Data Lake** > podregistr **(HDInsight)**  > **aplikace**. 

4. Zadejte název tohoto projektu a pak vyberte **OK**.

5. Otevřete soubor **script. HQL** , který je vytvořen pomocí tohoto projektu, a vložte následující příkazy HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Tyto příkazy provádějí následující akce:

   * `DROP TABLE`: Pokud tabulka existuje, tento příkaz je odstraní.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku External v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru (data jsou ponechána v původním umístění).

     > [!NOTE]  
     > Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například úloha MapReduce nebo služba Azure.
     >
     > Vyřazení externí tabulky **neodstraní data** , pouze definici tabulky.

   * `ROW FORMAT`: Instruuje podregistr, jak jsou data formátovaná. V tomto případě jsou pole v každém protokolu oddělená mezerou.

   * `STORED AS TEXTFILE LOCATION`: Říká podregistru, že data jsou uložená v příkladu nebo v datovém adresáři a že se ukládají jako text.

   * `SELECT`: Vyberte počet všech řádků, ve kterých sloupec `t4` obsahuje hodnotu. `[ERROR]` Tento příkaz vrátí hodnotu `3` , protože existují tři řádky, které obsahují tuto hodnotu.

   * `INPUT__FILE__NAME LIKE '%.log'`– Instruuje podregistr, že bychom měli vracet jenom data ze souborů, které končí. log. Tato klauzule omezí hledání na soubor Sample. log, který obsahuje data.

6. Na panelu nástrojů vyberte **cluster HDInsight** , který chcete použít pro tento dotaz. Vyberte **Odeslat** a spusťte příkazy jako úlohu podregistru.

   ![Odeslat panel](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. Zobrazí se **Souhrn úlohy podregistr** a zobrazí se informace o běžící úloze. Pomocí odkazu **aktualizovat** aktualizujte informace úlohy, dokud se **stav úlohy** nezmění na **dokončeno**.

   ![Souhrn úlohy znázorňující dokončenou úlohu](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. Výstup této úlohy můžete zobrazit pomocí odkazu **výstup úlohy** . Zobrazuje `[ERROR] 3`se hodnota vrácená tímto dotazem.

### <a name="additional-example"></a>Další příklad

Tento příklad spoléhá na `log4jLogs` tabulku vytvořenou v předchozím kroku.

1. Z **Průzkumník serveru**klikněte pravým tlačítkem na svůj cluster a vyberte **zapsat dotaz na podregistr**.

2. Zadejte následující dotaz na podregistr:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádějí následující akce:

    * `CREATE TABLE IF NOT EXISTS`: Vytvoří tabulku, pokud ještě neexistuje. `EXTERNAL` Protože klíčové slovo není použito, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uloženy v datovém skladu podregistru a jsou spravovány podregistru.
    
    > [!NOTE]  
    > Na rozdíl `EXTERNAL` od tabulek vyřazení interní tabulky také odstraní podkladová data.

    * `STORED AS ORC`: Ukládá data ve formátu optimalizovaného řádku (ORC). ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.
    
    * `INSERT OVERWRITE ... SELECT`: Vybere řádky z `log4jLogs` tabulky, která obsahuje `[ERROR]`, a pak data `errorLogs` vloží do tabulky.

3. Spusťte dotaz v režimu **dávky** .

4. Pokud chcete ověřit, že úloha vytvořila tabulku, použijte **Průzkumník serveru** a rozbalte službu **Azure** > **HDInsight** > ve**výchozím nastavení**clusteru HDInsight > >  **databáze podregistru**. Zobrazí **se tabulka chyb a** tabulka **log4jLogs** .

## <a id="nextsteps"></a>Další kroky

Jak vidíte, nástroje HDInsight pro Visual Studio poskytují snadný způsob práce s dotazy na podregistry v HDInsight.

Obecné informace o podregistru v HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o dalších způsobech, jak můžete pracovat se systémem Hadoop ve službě HDInsight:

* [Použití systému Apache prasete s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o nástrojích HDInsight pro Visual Studio:

* [Začínáme s nástroji HDInsight pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)