---
title: Používání Hadoop Hive v konzole dotazu v HDInsight - Azure | Microsoft Docs
description: Naučte se používat webovou konzolu dotazu ke spouštění dotazů Hive v clusteru HDInsight Hadoop z prohlížeče.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 04a6ad67fec4145d8f9164743b08f9e105778091
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405941"
---
# <a name="run-hive-queries-using-the-query-console"></a>Spouštění dotazů Hive pomocí konzole dotazu
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

V tomto článku se dozvíte, jak používat konzolu dotazu HDInsight ke spouštění dotazů Hive v clusteru HDInsight Hadoop z prohlížeče.

> [!IMPORTANT]
> Konzole dotazu HDInsight je dostupná pouze na clustery HDInsight se systémem Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, projděte si téma [spouštění dotazů Hive v zobrazení Ambari Hive](apache-hadoop-use-hive-ambari-view.md) informace o spouštění dotazů Hive z webového prohlížeče.

## <a id="prereq"></a>Požadavky
Pokud chcete provést kroky v tomto článku, budete potřebovat.

* Cluster HDInsight Hadoop založené na systému Windows
* Moderní webový prohlížeč

## <a id="run"></a> Spouštění dotazů Hive pomocí konzole dotazu
1. Otevřete webový prohlížeč a přejděte do **https://CLUSTERNAME.azurehdinsight.net**, kde **CLUSTERNAME** je název clusteru HDInsight. Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo, které jste použili při vytvoření clusteru.
2. Odkazy v horní části stránky, vyberte **Hive Editor**. Zobrazí formulář, který slouží k zadání HiveQL příkazy, které chcete spustit v clusteru HDInsight.

    ![hive editor](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Nahraďte text `Select * from hivesampletable` s následující příkazy HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Tyto příkazy provádět následující akce:

   * **VYŘAĎTE tabulku**: Odstraní tabulku a datový soubor, pokud tabulka již existuje.
   * **Vytvoření externí tabulky**: vytvoří novou tabulku "externí" v Hive. Externí tabulky uložit pouze definici tabulky Hive; data je ponechán v původním umístění.

     > [!NOTE]
     > Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje (například procesu nahrávání automatizované dat), nebo jiná operace MapReduce, ale chcete, aby dotazy Hive používat nejnovější data.
     >
     > Vyřazení externí tabulku nemá **není** odstranit data, jenom definici tabulky.
     >
     >
   * **Řádek formátu**: informuje Hive formátování data. V takovém případě polí v každém protokolu jsou oddělené mezerou.
   * **ULOŽENÉ umístění textový soubor AS**: informuje Hive, kde je data uložená (adresář, příklad nebo data) a která je uložena jako text
   * **Vyberte**: Vyberte počet všech řádků kde sloupec **t4** obsahovat hodnotu **[Chyba]**. To by měla vrátit hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.
   * **INPUT__FILE__NAME jako '%.log'** -informuje Hive, který jsme by měl vrátit pouze data ze souborů končící na. log. To brání hledání k sample.log souboru, který obsahuje data a udržuje ho z vrací data z jiných příkladu datové soubory, které neodpovídají schématu, které jsme definovali.
3. Klikněte na tlačítko **odeslání**. **Úlohy relace** v dolní části stránky by měl zobrazit podrobnosti pro úlohu.
4. Když **stav** pole změny **dokončeno**, vyberte **zobrazit podrobnosti** pro úlohu. Na stránce podrobností **výstup úlohy** obsahuje `[ERROR]    3`. Můžete použít **Stáhnout** tlačítko v tomto poli ke stažení souboru, který obsahuje výstup úlohy.

## <a id="summary"></a>Shrnutí
Jak vidíte, konzola dotazu poskytuje snadný způsob, jak spouštět dotazy Hive v clusteru služby HDInsight, monitorovat stav úlohy a načíst výstup.

Další informace o použití konzoly dotaz Hive ke spuštění úloh Hive, vyberte **Začínáme** v horní části konzoly dotazu, pak použít vzorků, které jsou k dispozici. Každá ukázka vás provede procesem použití Hive k analýze dat, včetně vysvětlení o příkazy HiveQL použít ve vzorku.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Hive v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pokud používáte s Hive Tez, najdete v následujících dokumentech pro ladění informace:

* [Pomocí uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)
* [Použití zobrazení Ambari Tez na HDInsight se systémem Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

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

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
