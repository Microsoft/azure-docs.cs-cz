---
title: Hivu se službou Data Lake (Hadoop) tools pro Visual Studio – Azure HDInsight
description: Naučte se používat nástroje Data Lake pro Visual Studio ke spouštění dotazů Apache Hive s Apache Hadoop v Azure HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: fd57e713a24fb83e42d46b4a1978530b706bf583
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049892"
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio

Naučte se používat nástroje Data Lake pro Visual Studio k dotazu Apache Hive. Data Lake tools umožňují snadno vytvářet, odeslání a monitorování dotazů Hive ke clusteru Hadoop v Azure HDInsight.

## <a id="prereq"></a>Požadavky

* Cluster Azure HDInsight (Hadoop v HDInsight)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (jedna z následujících verzí):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate s aktualizací Update 4

    * Visual Studio 2015 (libovolná edice)

    * Visual Studio 2017 (libovolná edice)

* Nástroje HDInsight pro Visual Studio nebo Azure Data Lake tools pro Visual Studio. Zobrazit [začněte používat nástroje Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md) informace o instalaci a konfiguraci nástroje.

## <a id="run"></a> Spouštění dotazů Hive pomocí sady Visual Studio

1. Otevřít **sady Visual Studio** a vyberte **nový** > **projektu** > **Azure Data Lake**  >   **HIVE** > **Hive aplikace**. Zadejte název pro tento projekt.

2. Otevřít **Script.hql** soubor vytvořený k tomuto projektu a vložte následující příkazy HiveQL:

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

   * `ROW FORMAT`: Přikáže Hive formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Přikáže Hive, že jsou data uložená v adresáři příklad/dat a uložená jako text.

   * `SELECT`: Vyberte počet všech řádků ve kterém sloupci `t4` obsahuje hodnotu `[ERROR]`. Tento příkaz vrátí hodnotu `3` vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.

   * `INPUT__FILE__NAME LIKE '%.log'` -Říká Hive, že jsme by měl vrátit pouze data ze souborů s koncovkou. log. Tato klauzule omezuje vyhledávání na souboru sample.log, který obsahuje data.

3. Na panelu nástrojů vyberte **clusteru HDInsight** , kterou chcete použít pro tento dotaz. Vyberte **odeslat** spustit příkazy jako úlohy Hive.

   ![Odeslat řádek](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **Souhrn úlohy Hive** se zobrazí informace o běžící úlohu. Použití **aktualizovat** odkaz aktualizovat informace o úloze, dokud **stav úlohy** změny **dokončeno**.

   ![Souhrn úlohy zobrazení dokončená úloha](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Použití **výstup úlohy** odkaz umožňující zobrazení výstupem této úlohy. Zobrazí se `[ERROR] 3`, což je hodnota vrácená tímto dotazem.

6. Bez vytvoření projektu můžete také spouštění dotazů Hive. Pomocí **Průzkumníka serveru**, rozbalte **Azure** > **HDInsight**, klikněte pravým tlačítkem na HDInsight server a pak vyberte **napsat dotaz Hive** .

7. V **temp.hql** dokument, který se zobrazí, přidejte následující příkazy HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Tyto příkazy provádět následující akce:

   * `CREATE TABLE IF NOT EXISTS`: Vytvoří tabulku, pokud ještě neexistuje. Vzhledem k tomu, `EXTERNAL` – klíčové slovo se nepoužívá, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložené v datovém skladu Hive a jsou spravované pomocí Hive.

     > [!NOTE]
     > Na rozdíl od `EXTERNAL` tabulek, vyřadit interní tabulku také odstraní podkladová data.

   * `STORED AS ORC`: Ukládá data ve sloupcovém formátu (ORC) optimalizované řádek. ORC je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.

   * `INSERT OVERWRITE ... SELECT`: Vybere řádky z `log4jLogs` tabulce, která obsahuje `[ERROR]`, pak vloží data do `errorLogs` tabulky.

8. Na panelu nástrojů vyberte **odeslat** na spuštění úlohy. Použití **stav úlohy** k určení, jestli se úloha úspěšně dokončil.

9. Chcete-li ověřit, že vytvoření úlohy v tabulce, použijte **Průzkumníka serveru** a rozbalte **Azure** > **HDInsight** > váš cluster HDInsight >  **Databáze Hive** > **výchozí**. **Nepřenesl** tabulky a **log4jLogs** tabulce jsou uvedeny.

## <a id="nextsteps"></a>Další kroky

Jak je vidět, nástroje HDInsight pro Visual Studio poskytují snadný způsob, jak pracovat s dotazy Hive v HDInsight.

Obecné informace o Hivu ve službě HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)

* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o nástrojích HDInsight pro Visual Studio:

* [Začínáme s nástroji HDInsight pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
