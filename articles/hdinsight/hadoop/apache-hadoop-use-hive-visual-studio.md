---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Naučte se používat nástroje Data Lake pro Visual Studio ke spouštění dotazů Apache Hive s Apache Hadoop ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 4512c9d9fdb66713ba24fbf30278e5d5dbb2ae23
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863747"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštění dotazů Apache Hivu pomocí Nástrojů Data Lake pro Visual Studio

Naučte se používat nástroje Data Lake pro Visual Studio k dotazování Apache Hive. Nástroje Data Lake umožňují snadno vytvářet, odesílat a monitorovat dotazy na podregistry pro Apache Hadoop v Azure HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Hadoop v HDInsight. Informace o vytvoření této položky najdete v tématu [Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí šablony Správce prostředků](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). Kroky v tomto článku používají Visual Studio 2019.

* Nástroje HDInsight pro Visual Studio nebo nástroje pro Azure Data Lake pro Visual Studio. Informace o instalaci a konfiguraci nástrojů naleznete v tématu [Install Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Spouštění dotazů Apache Hive pomocí sady Visual Studio

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad-hoc.
* Vytvořte aplikaci podregistru.

### <a name="create-an-ad-hoc-hive-query"></a>Vytvoření dotazu na podregistr pro ad hoc

Ad hoc dotazy se dají spustit buď v **dávce** , nebo v **interaktivním** režimu.

1. Spusťte **Visual Studio** a vyberte **pokračovat bez kódu**.

2. V **Průzkumník serveru** klikněte pravým tlačítkem myši na **Azure**, vyberte **připojit se k Microsoft Azure předplatného...** a dokončete proces přihlašování.

3. Rozbalte **HDInsight**, klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz, a pak vyberte **zapsat dotaz na podregistr**.

4. Zadejte následující dotaz na podregistr:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Vyberte **Execute** (Provést). Režim spuštění je ve výchozím nastavení **interaktivní**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png" alt-text="Spustit interaktivní dotaz na podregistr, Visual Studio" border="true":::

6. Pokud chcete spustit stejný dotaz v režimu **dávky** , přepněte rozevírací seznam z **Interactive** do **Batch**. Tlačítko spuštění se změní z příkazu **Spustit** na **odeslání**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png" alt-text="Odeslat dotaz na podregistr Batch, Visual Studio" border="true":::

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud například zadáte `SELECT * FROM` , IntelliSense zobrazí seznam všech navržených názvů tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF. IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

7. Na panelu nástrojů dotazu (oblast pod kartou dotaz a nad textem dotazu) vyberte **Odeslat** nebo vyberte šipku rozevírací vedle možnosti **Odeslat** a zvolte možnost **Upřesnit** v seznamu rozevírací. Pokud vyberete možnost druhé,

8. Pokud jste vybrali možnost Upřesnit odeslání, nakonfigurujte **název úlohy**, **argumenty**, **Další konfigurace** a **stavový adresář** v dialogovém okně **Odeslat skript** . Pak vyberte **Odeslat**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png" alt-text="Dialogové okno Odeslat skript, dotaz na podregistr pro HDInsight Hadoop" border="true":::

### <a name="create-a-hive-application"></a>Vytvoření aplikace Hive

Chcete-li spustit dotaz na podregistr vytvořením aplikace podregistru, postupujte takto:

1. Otevřete **Visual Studio**.

2. V okně **Start** vyberte **vytvořit nový projekt**.

3. V okně **vytvořit nový projekt** zadejte do pole **Hledat šablony** *podregistr*. Pak zvolte možnost **aplikace podregistru** a vyberte možnost **Další**.

4. V okně **Konfigurovat nový projekt** zadejte **název projektu**, vyberte nebo vytvořte **umístění** pro nový projekt a pak vyberte **vytvořit**.

5. Otevřete soubor **script. HQL** , který je vytvořen pomocí tohoto projektu, a vložte následující příkazy HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Tyto příkazy provedou následující akce:

    * `DROP TABLE`: Odstraní tabulku, pokud existuje.

    * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku External v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. (Data zůstanou v původním umístění.)

        > [!NOTE]  
        > Externí tabulky by se měly používat, pokud očekáváte, že se podkladová data aktualizují externím zdrojem, jako je například úloha MapReduce nebo služba Azure.
        >
        > Vyřazení externí tabulky **neodstraní data** , pouze definici tabulky.

    * `ROW FORMAT`: Instruuje podregistr, jak jsou data formátovaná. V tomto případě jsou pole v každém protokolu oddělená mezerou.

    * `STORED AS TEXTFILE LOCATION`: Říká podregistru, že data jsou uložená v *příkladu nebo v datovém* adresáři a že se ukládají jako text.

    * `SELECT`: Vybere počet všech řádků, ve kterých sloupec `t4` obsahuje hodnotu `[ERROR]` . Tento příkaz vrátí hodnotu `3` , protože tři řádky obsahují tuto hodnotu.

    * `INPUT__FILE__NAME LIKE '%.log'`: Oznamuje podregistru, aby vracel pouze data ze souborů končících log. log. Tato klauzule omezí hledání na soubor *Sample. log* , který obsahuje data.

6. Z panelu nástrojů souboru dotazu (který má podobný vzhled jako na panelu nástrojů ad-hoc) vyberte cluster HDInsight, který chcete použít pro tento dotaz. Pak změňte **Interactive** na **Batch** (v případě potřeby) a vyberte **Odeslat** , aby se příkazy spouštěly jako úloha podregistru.

   Zobrazí se **Souhrn úlohy podregistr** a zobrazí se informace o běžící úloze. Pomocí odkazu **aktualizovat** aktualizujte informace úlohy, dokud se **stav úlohy** nezmění na **dokončeno**.

   :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png" alt-text="Dokončená souhrn úlohy podregistru, aplikace pro podregistr, Visual Studio" border="true":::

7. Vyberte **výstup úlohy** a zobrazte výstup této úlohy. Zobrazuje `[ERROR] 3` se hodnota vrácená tímto dotazem.

### <a name="additional-example"></a>Další příklad

Následující příklad závisí na `log4jLogs` tabulce vytvořené v předchozím postupu, [Vytvoření aplikace v podregistru](#create-a-hive-application).

1. Z **Průzkumník serveru** klikněte pravým tlačítkem na svůj cluster a vyberte **zapsat dotaz na podregistr**.

2. Zadejte následující dotaz na podregistr:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provedou následující akce:

    * `CREATE TABLE IF NOT EXISTS`: Vytvoří tabulku, pokud ještě neexistuje. Protože `EXTERNAL` klíčové slovo není použito, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uloženy v datovém skladu podregistru a jsou spravovány podregistru.

        > [!NOTE]  
        > Na rozdíl od `EXTERNAL` tabulek vyřazení interní tabulky také odstraní podkladová data.

    * `STORED AS ORC`: Ukládá data ve formátu *optimalizovaného řádku* (Orc). ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.

    * `INSERT OVERWRITE ... SELECT`: Vybere řádky z `log4jLogs` tabulky, která obsahuje `[ERROR]` , a pak vloží data do `errorLogs` tabulky.

3. V případě potřeby změňte **Interactive** na **Batch** a pak vyberte **Odeslat**.

4. Pokud chcete ověřit, že úloha vytvořila tabulku, přejít na **Průzkumník serveru** a rozšířit **Azure**  >  **HDInsight**. Rozbalte svůj cluster HDInsight a pak rozbalte položku **databáze podregistru**  >  **výchozí**. Zobrazí **se tabulka chyb a** tabulka **log4jLogs** .

## <a name="next-steps"></a>Další kroky

Jak vidíte, nástroje HDInsight pro Visual Studio poskytují snadný způsob práce s dotazy na podregistry v HDInsight.

* Obecné informace o podregistru v HDInsight najdete v tématu [co je Apache Hive a HiveQL v Azure HDInsight?](hdinsight-use-hive.md)

* Informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight najdete v tématu [použití MapReduce v Apache Hadoop](hdinsight-use-mapreduce.md) ve službě HDInsight.

* Další informace o nástrojích HDInsight Tools for Visual Studio najdete v tématu[použití Data Lake nástrojů pro Visual Studio pro připojení k Azure HDInsight a spouštění dotazů Apache Hive](apache-hadoop-visual-studio-tools-get-started.md) .
