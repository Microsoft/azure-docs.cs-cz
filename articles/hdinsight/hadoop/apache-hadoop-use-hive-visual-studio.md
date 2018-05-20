---
title: Hive pomocí nástrojů Data Lake (Hadoop) pro Visual Studio – Azure HDInsight | Microsoft Docs
description: Další informace o použití nástroje Data Lake pro Visual Studio ke spouštění dotazů Apache Hive s Apache Hadoop v Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 862a2aae2e9d417ccf9daf336177b23842dd3db7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Spouštění dotazů Hive pomocí nástroje Data Lake pro Visual Studio

Zjistěte, jak pomocí nástrojů Data Lake pro Visual Studio pro Apache Hive dotaz. Nástroje Data Lake umožňují snadno vytvářet, odesílání a sledování dotazů Hive k systému Hadoop v Azure HDInsight.

## <a id="prereq"></a>Požadavky

* Cluster Azure HDInsight (Hadoop v HDInsight)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (jedna z následujících verzí):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate s aktualizací 4

    * Visual Studio 2015 (všechny edice)

    * Visual Studio 2017 (všechny edice)

* Nástroje HDInsight pro Visual Studio nebo nástroje Azure Data Lake pro Visual Studio. V tématu [začněte používat nástroje Visual Studio Hadoop pro HDInsight](apache-hadoop-visual-studio-tools-get-started.md) informace o instalaci a konfiguraci nástroje.

## <a id="run"></a> Spouštění dotazů Hive pomocí sady Visual Studio

1. Otevřete **Visual Studio** a vyberte **nový** > **projektu** > **Azure Data Lake**  >   **HIVE** > **podregistru aplikace**. Zadejte název pro tento projekt.

2. Otevřete **Script.hql** souboru, který je vytvořen s tímto projektu a vložte následující příkazy HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Tyto příkazy provádět následující akce:

   * `DROP TABLE`: Pokud existuje v tabulce, tento příkaz se odstraní.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku "externí" v Hive. Externí tabulky pouze uložit definici tabulky Hive (data je ponechán v původním umístění).

     > [!NOTE]
     > Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje. Například MapReduce úlohy nebo služby Azure.
     >
     > Vyřazení externí tabulku nemá **není** odstranit data, jenom definici tabulky.

   * `ROW FORMAT`: Určuje způsob formátování data Hive. V takovém případě polí v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Hive informuje, že jsou data uložena v adresáři příklad nebo dat a je uložen jako text.

   * `SELECT`: Vyberte počet všech řádků kde sloupec `t4` obsahuje hodnotu `[ERROR]`. Tento příkaz vrátí hodnotu `3` vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.

   * `INPUT__FILE__NAME LIKE '%.log'` -Informuje Hive, jsme by měl vrátit pouze data ze souborů končící na. log. Tuto klauzuli omezuje hledání sample.log soubor, který obsahuje data.

3. Na panelu nástrojů vyberte **clusteru HDInsight** , kterou chcete použít pro tento dotaz. Vyberte **odeslání** spustit příkazy jako úlohy Hive.

   ![Odeslání panelu](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. **Souhrn úlohy Hive** se zobrazí a zobrazí informace o probíhající úloze. Použití **aktualizovat** odkaz aktualizovat informace o úloze, dokud **stav úlohy** změny **dokončeno**.

   ![Souhrn úlohy zobrazení dokončené úlohy](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Použití **výstup úlohy** odkaz zobrazte výstup této úlohy. Zobrazuje `[ERROR] 3`, což je hodnoty vrácené tímto dotazem.

6. Můžete také spustit dotazů Hive bez vytvoření projektu. Pomocí **Průzkumníka serveru**, rozbalte položku **Azure** > **HDInsight**, klikněte pravým tlačítkem na serveru HDInsight a pak vyberte **napsat dotaz Hive** .

7. V **temp.hql** dokument, který se zobrazí, přidejte následující příkazy HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Tyto příkazy provádět následující akce:

   * `CREATE TABLE IF NOT EXISTS`: Vytvoří tabulku, pokud ještě neexistuje. Protože `EXTERNAL` – klíčové slovo není, tento příkaz vytvoří interní tabulku. Interní tabulky jsou uložena v datovém skladu Hive a jsou spravovány nástrojem Hive.

     > [!NOTE]
     > Na rozdíl od `EXTERNAL` tabulky, vyřazení interní tabulku taky odstraní zadaná data.

   * `STORED AS ORC`: Ukládá data ve sloupcovém formátu (ORC) optimalizované řádek. ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.

   * `INSERT OVERWRITE ... SELECT`: Vybere řádky z `log4jLogs` tabulku, která obsahují `[ERROR]`, pak vkládá data do `errorLogs` tabulky.

8. Na panelu nástrojů vyberte **odeslání** chcete spustit úlohu. Použití **stav úlohy** k určení, zda byla úloha úspěšně dokončena.

9. Chcete-li ověřit, zda úloha vytvořena v tabulce, použijte **Průzkumníka serveru** a rozbalte **Azure** > **HDInsight** > clusteru HDInsight >  **Hive databáze** > **výchozí**. **ErrorLogs** tabulky a **log4jLogs** tabulce jsou uvedeny.

## <a id="nextsteps"></a>Další kroky

Jak vidíte, nástroje HDInsight pro Visual Studio poskytují snadný způsob, jak pracovat s dotazy Hive v HDInsight.

Obecné informace o Hive v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)

* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Další informace o nástrojích HDInsight pro Visual Studio:

* [Začínáme s nástroje HDInsight pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

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
