---
title: Apache Hive pomocí nástrojů Data Lake (Apache Hadoopu) pro Visual Studio – Azure HDInsight
description: Naučte se používat nástroje Data Lake pro Visual Studio ke spouštění dotazů Apache Hive s Apache Hadoop v Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861601"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštějte dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio

Naučte se používat nástroje Data Lake pro Visual Studio k dotazu Apache Hive. Data Lake tools umožňují snadno vytvářet, odesílání a monitorování dotazů Hive pro Apache Hadoop v Azure HDInsight.

## <a id="prereq"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (jedna z následujících verzí):

    * Visual Studio 2015 a 2017 (libovolná edice)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate s aktualizací Update 4

* Nástroje HDInsight pro Visual Studio nebo Azure Data Lake tools pro Visual Studio. Zobrazit [začněte používat nástroje Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md) informace o instalaci a konfiguraci nástroje.

## <a id="run"></a> Spouštějte dotazy Apache Hive pomocí sady Visual Studio

Vytvářet a spouštět dotazy Hive můžete dvěma způsoby:

* Vytváření dotazů ad hoc
* Vytvoření aplikace Hive

### <a name="ad-hoc"></a>Ad hoc

Ad hoc dotazy mohou být provedeny buď **Batch** nebo **interaktivní** režimu.

1. Otevřít **sady Visual Studio**.

2. Z **Průzkumníka serveru**, přejděte na **Azure** > **HDInsight**.

3. Rozbalte **HDInsight**a klikněte pravým tlačítkem na cluster, ve kterém chcete spustit dotaz a pak vyberte **napsat dotaz Hive**.

4. Zadejte následující dotaz hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Vyberte **Provést**. Všimněte si, že je nastavena na výchozí režim spuštění **interaktivní**.

    ![Snímek spouštění interaktivních dotazů Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Při spuštění stejné dotazů **Batch** režimu, přepínací tlačítko rozevíracího seznamu z **interaktivní** k **Batch**. Všimněte si, že na spouštěcí tlačítko se změní z **Execute** k **odeslat**.

    ![Snímek odesílání dotazu Hive](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Editor Hive podporuje technologii IntelliSense. Nástroje Data Lake pro Visual Studio podporují načítání vzdálených metadat při úpravách skriptu Hive. Pokud zadáte například `SELECT * FROM`, IntelliSense vypíše všechny navrhované názvy tabulek. Pokud zadáte název tabulky, IntelliSense vypíše názvy sloupců. Nástroje podporují většinu příkazů DML Hive, poddotazů a integrovaných UDF. IntelliSense navrhuje pouze metadata clusteru vybraného na panelu nástrojů služby HDInsight.

    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Snímek obrazovky s IntelliSense ve Visual Studio Tools pro HDInsight – příklad 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Vyberte **Odeslat** nebo **Odeslat (rozšířené)** .

   Pokud jste použili možnost rozšířeného odeslání, nakonfigurujte pro skript **Název úlohy**, **Argumenty**, **Další konfigurace** a **Stavový adresář**:

    ![Snímek obrazovky s dotazem Hive v HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Odeslání dotazů")

### <a name="hive-application"></a>Aplikace Hive

1. Otevřít **sady Visual Studio**.

2. V řádku nabídek, přejděte na **souboru** > **nový** > **projektu**.

3. Z **nový projekt** okno, přejděte na **šablony** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive aplikace**. 

4. Zadejte název pro tento projekt a pak vyberte **OK**.

5. Otevřít **Script.hql** soubor vytvořený k tomuto projektu a vložte následující příkazy HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Tyto příkazy provádět následující akce:

   * `DROP TABLE`: Pokud v tabulce existuje, tento příkaz odstraní jej.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku "externí" v podregistru. Externí tabulky pouze uložte definici tabulky Hive (data zůstane v původním umístění).

     > [!NOTE]  
     > Pokud očekáváte, že podkladová data aktualizovat externího zdroje je třeba použít externí tabulky. Například úlohy MapReduce nebo služby Azure.
     >
     > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.

   * `ROW FORMAT`: Říká Hive formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Hive říká, že jsou data uložená v adresáři příklad/dat a uložená jako text.

   * `SELECT`: Vyberte počet všech řádků ve kterém sloupci `t4` obsahuje hodnotu `[ERROR]`. Tento příkaz vrátí hodnotu `3` vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.

   * `INPUT__FILE__NAME LIKE '%.log'` -Říká Hive, že jsme by měl vrátit pouze data ze souborů s koncovkou. log. Tato klauzule omezuje vyhledávání na souboru sample.log, který obsahuje data.

6. Na panelu nástrojů vyberte **clusteru HDInsight** , kterou chcete použít pro tento dotaz. Vyberte **odeslat** spustit příkazy jako úlohy Hive.

   ![Odeslat řádek](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. **Souhrn úlohy Hive** se zobrazí informace o běžící úlohu. Použití **aktualizovat** odkaz aktualizovat informace o úloze, dokud **stav úlohy** změny **dokončeno**.

   ![Souhrn úlohy zobrazení dokončená úloha](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Použití **výstup úlohy** odkaz umožňující zobrazení výstupem této úlohy. Zobrazí se `[ERROR] 3`, což je hodnota vrácená tímto dotazem.

### <a name="additional-example"></a>Další příklad

V tomto příkladu se může spolehnout `log4jLogs` tabulku vytvořenou v předchozím kroku.

1. Z **Průzkumníka serveru**, klikněte pravým tlačítkem na cluster a vyberte **napsat dotaz Hive**.

2. Zadejte následující dotaz hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Tyto příkazy provádět následující akce:

    * `CREATE TABLE IF NOT EXISTS`: Pokud ještě neexistuje, vytvoří tabulku. Vzhledem k tomu, `EXTERNAL` – klíčové slovo se nepoužívá, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložené v datovém skladu Hive a jsou spravované pomocí Hive.
    
    > [!NOTE]  
    > Na rozdíl od `EXTERNAL` tabulek, vyřadit interní tabulku také odstraní podkladová data.

    * `STORED AS ORC`: Ukládá data ve sloupcovém formátu (ORC) optimalizované řádek. ORC je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Vybere řádky z `log4jLogs` tabulce, která obsahuje `[ERROR]`, pak vloží data do `errorLogs` tabulky.

3. Provedení dotazu ve **Batch** režimu.

4. Chcete-li ověřit, že vytvoření úlohy v tabulce, použijte **Průzkumníka serveru** a rozbalte **Azure** > **HDInsight** > váš cluster HDInsight >  **Databáze Hive** > **výchozí**. **Nepřenesl** tabulky a **log4jLogs** tabulce jsou uvedeny.

## <a id="nextsteps"></a>Další kroky

Jak je vidět, nástroje HDInsight pro Visual Studio poskytují snadný způsob, jak pracovat s dotazy Hive v HDInsight.

Obecné informace o Hivu ve službě HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o nástrojích HDInsight pro Visual Studio:

* [Začínáme s nástroji HDInsight pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)