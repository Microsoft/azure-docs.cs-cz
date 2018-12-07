---
title: Použití Apache Hivu v konzole pro dotazy v HDInsight – Azure
description: Zjistěte, jak spouštět dotazy Apache Hive v clusteru HDInsight Hadoop z prohlížeče pomocí webové konzoly pro dotazy.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 99cb1c4aed8e82afd819185d7acab88cdae6c418
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016789"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>Spouštění dotazů Apache Hive pomocí konzoly pro dotazy
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

V tomto článku se dozvíte, jak spouštět dotazy Apache Hive v clusteru HDInsight Hadoop z prohlížeče pomocí konzoly pro dotazy HDInsight.

> [!IMPORTANT]
> Konzole pro dotazy HDInsight je dostupná pouze na clusterech HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, přečtěte si téma [spouštění dotazů Hive v zobrazení Ambari Hive](apache-hadoop-use-hive-ambari-view.md) informace o spouštění dotazů Hive z webového prohlížeče.

## <a id="prereq"></a>Požadavky
K dokončení kroků v tomto článku, budete potřebovat.

* Cluster HDInsight Hadoop využívající systém Windows
* Moderní webový prohlížeč

## <a id="run"></a> Spouštění dotazů Hive pomocí konzoly pro dotazy
1. Otevřete webový prohlížeč a přejděte do **https://CLUSTERNAME.azurehdinsight.net**, kde **CLUSTERNAME** je název vašeho clusteru HDInsight. Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo, které jste použili při vytváření clusteru.
2. Z odkazů v horní části stránky vyberte **Hive Editor**. Zobrazí se formulář, který je možné zadat příkazy HiveQL, které chcete spustit v clusteru HDInsight.

    ![hive editor](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Nahraďte text `Select * from hivesampletable` s následující příkazy HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Tyto příkazy provádět následující akce:

   * **DROP TABLE**: Odstraní tabulku a datový soubor, pokud tabulka již existuje.
   * **CREATE EXTERNAL TABLE**: vytvoří novou tabulku "externí" v podregistru. Externí tabulky uložení pouze definice tabulky v podregistru; data zůstane v původním umístění.

     > [!NOTE]
     > Externí tabulky, které má být použit, při očekáváte, že podkladová data aktualizovat pomocí externího zdroje (například procesu nahrávání automatizovaných datových) nebo jiné operaci MapReduce, ale chcete, aby dotazy Hive používat nejnovější data.
     >
     > Vyřazení externí tabulky neodpovídá **není** odstranit data, pouze definici tabulky.
     >
     >
   * **ŘÁDEK formát**: říká Hive formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.
   * **ULOŽEN jako textový soubor umístění**: říká Hive, ve kterém se data ukládají (do adresáře příkladu/dat) a, která je uložená jako text
   * **Vyberte**: Vyberte počet všech řádků ve kterém sloupci **t4** obsahovat hodnotu **[Chyba]**. To by měl vrátit hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.
   * **INPUT__FILE__NAME jako "%.log"** -říká Hive, který jsme by měl vrátit pouze data ze souborů s koncovkou. log. To omezuje vyhledávání na souboru sample.log, který obsahuje data a udržuje ho z vracet data z jiných příklad datové soubory, které neodpovídají schématu, které jsme definovali.
3. Klikněte na tlačítko **odeslat**. **Relaci úloh** v dolní části stránky, by měl zobrazit podrobnosti pro konkrétní úlohu.
4. Když **stav** změny pole s **dokončeno**vyberte **zobrazit podrobnosti o** pro úlohu. Na stránce podrobností **výstup úlohy** obsahuje `[ERROR]    3`. Můžete použít **Stáhnout** tlačítko v tomto poli se stáhnout soubor, který obsahuje výstup úlohy.

## <a id="summary"></a>Shrnutí
Jak je vidět, konzoly pro dotazy poskytuje snadný způsob, jak spouštět dotazy Hive v clusteru služby HDInsight, sledovat stav úlohy a načtení výstupu.

Další informace o použití konzoly pro dotazy Hive můžete spouštět úlohy Hive, vyberte **Začínáme** v horní části konzoly pro dotazy, pak použijte ukázky, které jsou k dispozici. Každá ukázka vás provede procesem pomocí Hivu analyzovat data, včetně vysvětlení o příkazy HiveQL použili v ukázce.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Hivu ve službě HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte pomocí Hive Tez, naleznete v následujících dokumentech pro informace o ladění:

* [Použití uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight založených na Linuxu](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
