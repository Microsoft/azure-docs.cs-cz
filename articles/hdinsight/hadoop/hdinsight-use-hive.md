---
title: Co je Apache Hive a HiveQL – Azure HDInsight
description: Apache Hive se systémem datového skladu pro Apache Hadoop. Můžete dotazovat data uložená v Hive s využitím HiveQL, který podobný příkazů jazyka Transact-SQL. V tomto dokumentu zjistěte, jak pomocí Azure HDInsight Hive a HiveQL.
keywords: hiveql, co je hive, hiveql s hadoop, použití hive, zjistěte, hive, co je hive
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.openlocfilehash: 03e15532d04a4c62796aad884c5e344688814672
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209230"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co je Apache Hive a HiveQL v Azure HDInsight?

[Apache Hive](https://hive.apache.org/) je systém datového skladu pro Apache Hadoop. Hive umožňuje souhrnů dat, dotazování a analýze dat. Dotazy Hive jsou napsané v HiveQL, což je dotazovací jazyk podobný SQL.

Struktura projektu do značné míry nestrukturovaných dat vám umožní Hive. Po definování struktury můžete zadávat dotazy na data bez znalosti Javy nebo MapReduce HiveQL.

HDInsight obsahuje několik typů clusteru, která je vyladěná pro konkrétní úlohy. Následující typy clusteru se nejčastěji používají pro dotazy Hive:

* __Interactive Query__: Cluster Hadoop, která poskytuje [s nízkou latencí analytické zpracování (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) funkce, které zkrátit dobu odezvy pro interaktivní dotazy. Další informace najdete v tématu [Začínáme s interaktivními dotazy v HDInsight](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

* __Hadoop__: Cluster Hadoop, která je vyladěná pro zpracování úloh služby batch. Další informace najdete v tématu [Začínáme s Apache Hadoop v HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) dokumentu.

* __Spark__: Apache Spark má integrované funkce pro práci s Hive. Další informace najdete v tématu [Začínáme s Apache Spark v HDInsight](../spark/apache-spark-jupyter-spark-sql.md) dokumentu.

* __HBase__: HiveQL slouží k dotazování dat uložených v Apache HBase. Další informace najdete v tématu [Začínáme s Apache HBase v HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) dokumentu.

## <a name="how-to-use-hive"></a>Použití Hivu

V následující tabulce použijte ke zjištění různých způsobech používání Hive s HDInsight:

| **Tuto metodu použijte** Pokud chcete... | ... **interaktivní** dotazy | ...**batch** zpracování | ...při to **clusteru operačního systému** | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [Nástroje HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ |Linux | Linux, Unix, Mac OS X a Windows |
| [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Linux nebo Windows * |Windows |
| [Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Žádné (využívajících prohlížeč) |
| [Klient beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X a Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux nebo Windows * |Linux, Unix, Mac OS X a Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux nebo Windows * |Windows |

> [!IMPORTANT]
> \* Linux se používá v HDInsight verze 3.4 výhradně operační systém. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="hiveql-language-reference"></a>Referenční informace k jazyku HiveQL

Referenční informace k jazyku HiveQL je k dispozici v [příručky jazyka (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive a datové struktury

Hive znalost práce s strukturovaných a částečně strukturovaná data. Například textové soubory kde pole jsou odděleny určitých znaků. Následující příkaz HiveQL vytvoří tabulku daty oddělených mezerami:

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

Hive podporuje taky vlastní **serializátor/deserializers (SerDe)** pro komplexní nebo nepravidelně strukturovaná data. Další informace najdete v tématu [použití vlastní SerDe JSON s HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx) dokumentu.

Další informace o formátech podporovaných Hive najdete v článku [ruční jazyka (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Interní tabulky vs externích tabulek Hive

Existují dva typy tabulek, které můžete vytvořit pomocí Hive:

* __Interní__: Data se ukládají v datovém skladu Hive. Datový sklad se nachází na `/hive/warehouse/` na výchozí úložiště pro cluster.

    Použijte interní tabulky při vztahovat jeden z následujících podmínek:

    * Data jsou dočasné.
    * Chcete, aby Hive ke správě životního cyklu tabulky a data.

* __Externí__: Data uložená mimo datového skladu. Data lze uložit na jakékoli úložiště dostupné v clusteru.

    Používejte externí tabulky, které při vztahovat jeden z následujících podmínek:

    * Data se také používá mimo Hive. Například datové soubory jsou aktualizovány jiným procesem (tj. Nepoužívejte zámky souborech.)
    * Data musí zůstat v podkladové umístění i po odstranění v tabulce.
    * Budete potřebovat vlastní umístění, jako je například účet služby storage jiné než výchozí.
    * Program než hive spravuje formát dat, umístění atd.

Další informace najdete v tématu [Hive interní a externí tabulky ÚVOD] [ cindygross-hive-tables] blogový příspěvek.

## <a name="user-defined-functions-udf"></a>Uživatelem definované funkce (UDF)

Hive se také dají rozšířit prostřednictvím **uživatelem definované funkce (UDF)**. Uživatelem definovanou FUNKCI umožňuje implementovat logiku, která se snadno modelována nebo funkce v HiveQL. Příklad použití funkce UDF pomocí Hive najdete v následujících dokumentech:

* [Uživatelem definované funkce jazyka Java pomocí Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Použití uživatelem definované funkce Pythonu s Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Použití C# uživatelem definovanou funkci s Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak přidat vlastní Apache Hive uživatelem definované funkce HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Příklad Apache Hive uživatelem definované funkce pro převod formátů data a času na časové razítko Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Ukázková data

Hive v HDInsight se dodává už načtené s interní tabulku s názvem `hivesampletable`. HDInsight také poskytuje příklad datové sady, které je možné pomocí Hive. Tyto datové sady jsou uloženy v `/example/data` a `/HdiSamples` adresáře. Tyto adresáře neexistují ve výchozím nastavení úložiště pro váš cluster.

## <a id="job"></a>Příklad dotazu Hive

Sloupce do projektu následující příkazy HiveQL `/example/data/sample.log` souboru:

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

* `set hive.execution.engine=tez;`: Nastavuje prováděcí modul použití Apache Tez. Pomocí Tez můžete poskytnout zvýšení výkonu dotazů. Další informace o Tez najdete v článku [použití rozhraní Apache Tez pro zlepšení výkonu](#usetez) oddílu.

    > [!NOTE]  
    > Tento příkaz je jenom nutné při použití clusteru HDInsight se systémem Windows. Tez je výchozí prováděcí modul pro HDInsight se systémem Linux.

* `DROP TABLE`: Pokud tabulka již existuje, odstraňte ho.

* `CREATE EXTERNAL TABLE`: Vytvoří novou **externí** tabulky v Hivu. Externí tabulky pouze uložte definici tabulky Hive. Data zůstane v původním umístění a v původním formátu.

* `ROW FORMAT`: Říká Hive formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

* `STORED AS TEXTFILE LOCATION`: Říká Hive, ve kterém jsou data uložená ( `example/data` adresáře) a, která je uložená jako text. Data mohou být v jednom souboru nebo rozdělené mezi více souborů v adresáři.

* `SELECT`: Vybere počet všech řádků ve kterém sloupci **t4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** vzhledem k tomu, že existují tři řádky, které obsahují tuto hodnotu.

* `INPUT__FILE__NAME LIKE '%.log'` -Hive se pokusí použít schéma pro všechny soubory v adresáři. V takovém případě adresář obsahuje soubory, které neodpovídají schématu. Chcete-li zabránit uvolňování paměti ve výsledcích, tento příkaz sděluje Hive, že jsme by měl vrátit pouze data ze souborů s koncovkou. log.

> [!NOTE]  
> Pokud očekáváte, že podkladová data aktualizovat externího zdroje je třeba použít externí tabulky. Například automatizovaných datových odesílat operaci MapReduce nebo procesu.
>
> Vyřazení externí tabulky neodpovídá **není** odstranit data, jenom odstraní definici tabulky.

Vytvoření **interní** místo externí tabulky, použijte následující HiveQL:

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

* `CREATE TABLE IF NOT EXISTS`: Pokud tabulka neexistuje, vytvořte ho. Vzhledem k tomu, **externí** – klíčové slovo se nepoužívá, tento příkaz vytvoří interní tabulku. Tabulka je uložena v datovém skladu Hive a spravuje úplně Hive.

* `STORED AS ORC`: Ukládá data ve formátu optimalizované řádek úložiště se sloupcovou strukturou (ORC). ORC je vysoce optimalizovaných a efektivní formát pro ukládání dat Hive.

* `INSERT OVERWRITE ... SELECT`: Vybere řádky z **log4jLogs** tabulku, která obsahuje **[Chyba]** a pak vloží data do **nepřenesl** tabulky.

> [!NOTE]  
> Na rozdíl od externích tabulek vyřadit interní tabulku se odstraní také podkladová data.

## <a name="improve-hive-query-performance"></a>Zlepšení výkonu dotazů Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) je architektura, která umožňuje aplikace náročné na data, jako je například Hive mnohem efektivněji spouštět škálovaně. Tez je povoleno standardně pro clustery HDInsight založené na Linuxu.

> [!NOTE]  
> Tez je aktuálně vypnuto ve výchozím nastavení pro clustery HDInsight se systémem Windows a musí být povolené. Abyste mohli využívat Tez, musí být nastavena následující hodnotu pro dotaz Hive:
>
> `set hive.execution.engine=tez;`
>
> Tez je výchozí modul pro clustery HDInsight založené na Linuxu.

[Apache Hive na dokumentech návrhu Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) obsahuje podrobné informace o možnosti implementace a ladění konfigurace.

Pro ladění úlohy proběhla za použití Tez, HDInsight poskytuje následující webové uživatelské rozhraní, které vám umožní zobrazit podrobnosti o úlohách Tez: [Použití zobrazení Apache Ambari Tez na Linuxovým systémem HDInsight](../hdinsight-debug-ambari-tez-view.md).

### <a name="low-latency-analytical-processing-llap"></a>Analytické zpracování s nízkou latencí (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (někdy označované jako Live Long and Process) je nová funkce ve verzi 2.0 Hive, umožňující ukládání v mezipaměti dotazů. LLAP díky dotazů Hive mnohem rychlejší, až [26 x rychlejší než Hive 1.x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight poskytuje LLAP v typu clusteru Interactive Query. Další informace najdete v tématu [Začínáme s interaktivními dotazy](../interactive-query/apache-interactive-query-get-started.md) dokumentu.

## <a name="scheduling-hive-queries"></a>Plánování dotazů Hive

Existuje několik služeb, které je možné ke spouštění dotazů Hive jako součást pracovního postupu ručně, nebo na vyžádání.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory umožňuje používat HDInsight jako součást kanálu služby Data Factory. Další informace o používání Hive v kanálu najdete v článku [transformovat data pomocí aktivity Hivu ve službě Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) dokumentu.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Úlohy Hive a SQL Server Integration Services

SQL Server Integration Services (SSIS) můžete použít ke spuštění úlohy Hive. Azure Feature Pack for SSIS obsahuje následující součásti, které pracují s úloh Hive v HDInsight.

* [Úloha Azure HDInsight Hive][hivetask]

* [Správce připojení Azure předplatného][connectionmanager]

Další informace najdete v tématu [Azure Feature Pack] [ ssispack] dokumentaci.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie je pracovní postup a koordinaci systém, který spravuje úlohy platformy Hadoop. Další informace o použití Oozie s Hive najdete v článku [Apache Oozie použijte k definování a spuštění pracovního postupu](../hdinsight-use-oozie-linux-mac.md) dokumentu.

## <a id="nextsteps"></a>Další kroky

Teď, když jste se naučili, co je Hive a jeho použití se systémem Hadoop v HDInsight, pomocí následujících odkazů a prozkoumejte další možnosti, jak pracovat s Azure HDInsight.

* [Nahrání dat do služby HDInsight][hdinsight-upload-data]
* [Použití Apache Pig s HDInsight][hdinsight-use-pig]
* [Použijte úlohy MapReduce s HDInsight][hdinsight-use-mapreduce]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: https://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
