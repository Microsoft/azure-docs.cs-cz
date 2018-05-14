---
title: Co je Apache Hive a HiveQL - Azure HDInsight | Microsoft Docs
description: Apache Hive je systém datového skladu pro Hadoop. Můžete dát dotaz na data uložená v Hive pomocí HiveQL, který podobná Transact-SQL. V tomto dokumentu další informace o použití Hive a HiveQL s Azure HDInsight.
keywords: hiveql, co je hive, hadoop hiveql, postup používání hive, přečtěte si, hive, co je hive
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: e418411cc6b681e304cc1ba66f0c815ad0d4db64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co je Apache Hive a HiveQL v Azure HDInsight?

[Apache Hive](http://hive.apache.org/) je systém datového skladu pro Hadoop. Hive umožňuje shrnutí dat, dotazy a analýzy dat. Dotazy Hive jsou zapsány ve HiveQL, což je podobná SQL dotazovací jazyk.

Hive umožňuje strukturu projektu na do značné míry Nestrukturovaná data. Po definování strukturu, můžete zadávat dotazy na data bez vědomí Java nebo MapReduce HiveQL.

HDInsight poskytuje několik typů clusteru, které jsou přizpůsobená pro konkrétní úlohy. Pro dotazy Hive se nejčastěji používají následující typy clusteru:

* __Interaktivní dotazu__: cluster A Hadoop, který poskytuje [nízká latence analytického zpracování (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkce zlepšit dobu odezvy na interaktivních dotazů. Další informace najdete v tématu [začínat interaktivní dotazu v HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

* __Hadoop__: cluster A Hadoop, který je přizpůsobená pro dávkové zpracování úlohy. Další informace najdete v tématu [začněte s Hadoop v HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentu.

* __Spark__: Apache Spark obsahuje integrovanou funkci pro práci s Hive. Další informace najdete v tématu [začínat Spark v HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentu.

* __HBase__: HiveQL lze použít k dotazování na data uložená v HBase. Další informace najdete v tématu [začínat HBase v HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentu.

## <a name="how-to-use-hive"></a>Postup použití Hive

Následující tabulku použijte ke zjištění různé způsoby, jak použijte Hive s HDInsight:

| **Tuto metodu použijte** Pokud chcete... | ... **interaktivní** dotazy | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [Nástroje HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X nebo systému Windows |
| [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux nebo Windows * |Windows |
| [Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Žádné (prohlížeč na základě) |
| [Beeline klienta](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X nebo systému Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux nebo Windows * |Linux, Unix, Mac OS X nebo systému Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux nebo Windows * |Windows |

> [!IMPORTANT]
> \* Linux je jenom operační systém používaný v HDInsight verze 3.4 nebo novější. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>Referenční dokumentace jazyka HiveQL

Referenční dokumentace jazyka HiveQL je k dispozici v [jazyk ručně (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive a datové struktury

Jak pracovat s strukturovaných a částečně strukturovaných dat plně chápe, Hive. Například textové soubory kde pole jsou oddělená konkrétní znaků. Následující příkaz HiveQL vytvoří tabulku nad daty oddělených mezerami:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive podporuje i vlastní **serializátor/deserializers (SerDe)** složitý nebo nepravidelně strukturovaných dat. Další informace najdete v tématu [použití vlastní SerDe JSON s HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentu.

Další informace o souboru formátů podporovaných Hive naleznete v tématu [ruční (jazykhttps://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Interní tabulky vs externích tabulek Hive

Existují dva typy tabulek, které můžete vytvořit pomocí Hive:

* __Interní__: Data jsou uložena v datovém skladu Hive. Datový sklad je umístěn v `/hive/warehouse/` na výchozí úložiště pro cluster.

    Interní tabulky použijte, když jeden z následujících podmínek:

    * Data jsou dočasné.
    * Chcete Hive ke správě životního cyklu tabulky a data.

* __Externí__: Data jsou uložena mimo datového skladu. Data můžete uložit na jakékoli úložiště dostupné v clusteru.

    Externí tabulky použijte, když jeden z následujících podmínek:

    * Data se také používají mimo Hive. Například soubory, data se aktualizují jiným procesem (který není uzamčení souborů.)
    * Data musí zůstat v základní umístění i po vyřazení v tabulce.
    * Je nutné do vlastního umístění, jako je například účet jiné než výchozí úložiště.
    * Program než hive spravuje formát dat, umístění atd.

Další informace najdete v tématu [Hive interní a externí tabulky ÚVOD] [ cindygross-hive-tables] příspěvku na blogu.

## <a name="user-defined-functions-udf"></a>Uživatelem definované funkce (UDF)

Hive lze také prostřednictvím rozšířit **uživatelsky definované funkce (UDF)**. Uživatelem definovanou FUNKCI umožňuje implementovat logiku, která není modelován snadno nebo funkce v HiveQL. Příklad použití UDF s Hive najdete v následujících dokumentech:

* [Uživatelem definované funkce Java pomocí Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Uživatelem definované funkce Python pomocí Hive](../hadoop/python-udf-hdinsight.md)

* [C# uživatelem definované funkce pomocí Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Postup přidání vlastní uživatelem definované funkce Hive HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Příklad Hive uživatelsky definované funkce pro převod formátů data a času na časové razítko Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Příklad dat

Hive v HDInsight obsahuje předem načtený vnitřní tabulku s názvem `hivesampletable`. HDInsight také poskytuje příklad datových sad, které lze použít s Hive. Tyto sady dat se ukládají do `/example/data` a `/HdiSamples` adresáře. Tyto adresáře neexistují ve výchozím nastavení úložiště pro cluster.

## <a id="job"></a>Příklad dotazu Hive

Následující příkazy HiveQL projektu sloupce na `/example/data/sample.log` souboru:

```hiveql
set hive.execution.engine=tez;
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
    GROUP BY t4;
```

Příkazy HiveQL v předchozím příkladu, proveďte následující akce:

* `set hive.execution.engine=tez;`: Nastaví použít Tez modulu pro spouštění. Pomocí Tez můžete poskytnout zvýšení výkonu dotazů. Další informace o Tez naleznete v tématu [použití rozhraní Apache Tez pro zlepšení výkonu](#usetez) části.

    > [!NOTE]
    > Tento příkaz je jenom když používáte cluster HDInsight se systémem Windows je vyžadováno. Tez je výchozí modul provádění HDInsight se systémem Linux.

* `DROP TABLE`: Pokud v tabulce již existuje, odstraňte jej.

* `CREATE EXTERNAL TABLE`: Vytvoří novou **externí** tabulky v Hive. Externí tabulky pouze uložit definici tabulky Hive. Data je ponechán v původním umístění a v původním formátu.

* `ROW FORMAT`: Určuje způsob formátování data Hive. V takovém případě polí v každém protokolu jsou oddělené mezerou.

* `STORED AS TEXTFILE LOCATION`: Informuje Hive se uloží data ( `example/data` adresáře) a která je uložena jako text. Data mohou být v jednom souboru nebo rozloženy více souborů v adresáři.

* `SELECT`: Počet všech řádků vybere kde sloupec **t4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive se pokusí použít schéma pro všechny soubory v adresáři. V takovém případě adresáře obsahuje soubory, které neodpovídají žádné schéma. Aby paměti data ve výsledcích tento příkaz informuje Hive, jsme by měl vrátit pouze data ze souborů končící na. log.

> [!NOTE]
> Externí tabulky by měl být použit při očekáváte, že v základních datech aktualizovat externího zdroje. Například procesu nahrávání automatizované dat, nebo operaci MapReduce.
>
> Vyřazení externí tabulku nemá **není** odstranit data, odstraní pouze definici tabulky.

Chcete-li vytvořit **interní** místo externí tabulky, použijte následující HiveQL:

```hiveql
set hive.execution.engine=tez;
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Tyto příkazy provádět následující akce:

* `CREATE TABLE IF NOT EXISTS`: Pokud tabulka neexistuje, vytvořte ho. Protože **externí** – klíčové slovo není, tento příkaz vytvoří interní tabulku. V tabulce je uložená v datovém skladu Hive a úplně spravuje Hive.

* `STORED AS ORC`: Ukládá data ve formátu optimalizované řádek sloupcovém (ORC). ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.

* `INSERT OVERWRITE ... SELECT`: Vybere řádky z **log4jLogs** tabulku, která obsahuje **[Chyba]** a potom vkládá data do **errorLogs** tabulky.

> [!NOTE]
> Na rozdíl od externích tabulek se odstranit interní tabulku taky odstraní základní data.

## <a name="improve-hive-query-performance"></a>Zlepšení výkonu dotazů Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) je rozhraní, které umožňuje data náročné aplikace, například Hive, spusťte mnohem efektivněji ve velkém měřítku. Ve výchozím nastavení pro clustery HDInsight se systémem Linux je povolené tez.

> [!NOTE]
> Tez není momentálně vypnuto ve výchozím nastavení pro clustery HDInsight se systémem Windows a musí být povolena. Pokud chcete využít výhod Tez, nutné nastavit následující hodnotu pro dotaz Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez je výchozí modul pro clustery HDInsight se systémem Linux.

[Hive na dokumentech návrhu Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) obsahuje podrobnosti o volby implementace a vyladění konfigurace.

Ladění úloh proběhla za použití Tez, HDInsight poskytuje následující webové uživatelská rozhraní, které vám umožní zobrazit podrobnosti o úlohách Tez:

* [Použití zobrazení Ambari Tez na HDInsight se systémem Linux](../hdinsight-debug-ambari-tez-view.md)

* [Pomocí uživatelského rozhraní Tez na HDInsight se systémem Windows](../hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Nízká latence analytického zpracování (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (někdy označované jako Live dlouhé a proces) je nová funkce v podregistru 2.0, který umožňuje ukládání do mezipaměti v paměti dotazů. LLAP umožňuje mnohem rychlejší než dotazů Hive [26 x rychlejší než Hive 1.x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight poskytuje LLAP v clusteru typu interaktivní dotazu. Další informace najdete v tématu [začínat interaktivní dotazu](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

## <a name="scheduling-hive-queries"></a>Plánování dotazů Hive

Existuje několik služeb, které lze použít ke spouštění dotazů Hive jako součást pracovního postupu na vyžádání nebo naplánované.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory můžete použít jako součást pro vytváření dat kanál HDInsight. Další informace o používání Hive od kanálu najdete v tématu [transformace dat pomocí aktivity Hive v Azure Data Factory](/data-factory/transform-data-using-hadoop-hive.md) dokumentu.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Úlohy Hive a SQL Server Integration Services

Integrace služby SSIS (SQL Server) můžete použít ke spuštění úlohy Hive. Azure Feature Pack pro službu SSIS poskytuje následující součásti, které pracují se úlohy Hive v HDInsight.

* [Úloha Azure HDInsight Hive][hivetask]

* [Správce připojení k Azure předplatného][connectionmanager]

Další informace najdete v tématu [Azure Feature Pack] [ ssispack] dokumentaci.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie je systém koordinace a pracovního postupu, který spravuje úloh Hadoop. Další informace o používání Oozie s Hive najdete v tématu [Oozie použijte k definování a spuštění pracovního postupu](../hdinsight-use-oozie-linux-mac.md) dokumentu.

## <a id="nextsteps"></a>Další kroky

Teď, když jste se naučili novinky Hive a způsobu jeho použití s Hadoop v HDInsight, pomocí následujících odkazů a prozkoumejte další způsoby, jak pracovat s Azure HDInsight.

* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Použijte úlohy MapReduce s HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
