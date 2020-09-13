---
title: Co je Apache Hive a HiveQL – Azure HDInsight
description: Apache Hive je systém datového skladu pro Apache Hadoop. Můžete zadávat dotazy na data uložená v podregistru pomocí HiveQL, který se podobá jazyku Transact-SQL. V tomto dokumentu se dozvíte, jak používat podregistr a HiveQL v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 37fdf863d29015bba7015fcff1ae49a34aebd785
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462271"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co je Apache Hive a HiveQL ve službě Azure HDInsight?

[Apache Hive](https://hive.apache.org/) je systém datového skladu pro Apache Hadoop. Podregistr umožňuje sumarizaci dat, dotazování a analýzu dat. Dotazy na podregistr se napíší v HiveQL, což je dotazovací jazyk podobný SQL.

Podregistr umožňuje strukturování struktury na základě nestrukturovaných dat. Po definování struktury můžete použít HiveQL k dotazování dat bez znalosti jazyka Java nebo MapReduce.

HDInsight nabízí několik typů clusterů, které jsou vyladěné pro konkrétní úlohy. Následující typy clusterů se nejčastěji používají pro dotazy na podregistry:

|Typ clusteru |Popis|
|---|---|
|Interaktivní dotaz|Cluster Hadoop, který poskytuje funkci [LLAP (nízká latence Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) pro zlepšení dob odezvy pro interaktivní dotazy. Další informace najdete v dokumentu [Začínáme s interaktivním dotazem v HDInsight](../interactive-query/apache-interactive-query-get-started.md) .|
|Hadoop|Cluster Hadoop, který je vyladěn pro úlohy dávkového zpracování. Další informace najdete v dokumentu [Začínáme s Apache Hadoop v HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .|
|Spark|Apache Spark má integrovanou funkcionalitu pro práci s podregistru. Další informace najdete v dokumentu [Začínáme s Apache Spark v HDInsight](../spark/apache-spark-jupyter-spark-sql.md) .|
|HBase|HiveQL se dá použít k dotazování na data uložená v Apache HBA. Další informace najdete v článku [Začínáme s Apache HBA v dokumentu HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) .|

## <a name="how-to-use-hive"></a>Jak používat podregistr

Pro zjištění různých způsobů použití podregistru se službou HDInsight použijte následující tabulku:

| **Tuto metodu použijte** , pokud chcete... | ... **interaktivní** dotazy | ... **dávkové** zpracování | ... z tohoto **klientského operačního systému** |
|:--- |:---:|:---:|:--- |:--- |
| [Nástroje HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X nebo Windows |
| [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Jakékoli (založené na prohlížeči) |
| [Klient Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X nebo Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X nebo Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Reference k jazyku HiveQL

Reference k jazyku HiveQL je k dispozici v [příručce jazyka](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Struktura podregistru a dat

Podregistr chápe, jak pracovat se strukturovanými a částečně strukturovanými daty. Například textové soubory, kde jsou pole oddělená konkrétními znaky. Následující příkaz HiveQL vytvoří tabulku pro data oddělená mezerou:

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

Podregistr také podporuje vlastní **serializátor nebo deserializaci (SerDe)** pro složitá nebo nepravidelná strukturovaná data. Další informace najdete v tématu [Jak používat vlastní SERDE JSON s](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) dokumentem HDInsight.

Další informace o formátech souborů podporovaných podregistru najdete v tématu [Ruční verze jazyka ( https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Vnitřní tabulky a externí tabulky podregistru

Existují dva typy tabulek, které lze vytvořit s podregistru:

* __Interní__: data jsou uložená v datovém skladu podregistru. Datový sklad se nachází ve `/hive/warehouse/` výchozím úložišti pro daný cluster.

    Použijte interní tabulky, pokud platí jedna z následujících podmínek:

    * Data jsou dočasná.
    * Chcete, aby podregistr spravoval životní cyklus tabulky a dat.

* __Externí__: data se ukládají mimo datový sklad. Data je možné ukládat do libovolného úložiště přístupného clusterem.

    Externí tabulky použijte v případě, že platí jedna z následujících podmínek:

    * Data se také používají mimo podregistr. Například datové soubory jsou aktualizovány jiným procesem (nezamkne soubory.)
    * Data musí zůstat v základním umístění, a to i po vyřazení tabulky.
    * Potřebujete vlastní umístění, jako je například účet úložiště, který není výchozí.
    * Jiný program než podregistr spravuje formát dat, umístění atd.

Další informace najdete v blogovém příspěvku v [interních a externích tabulkách pro podregistr](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) .

## <a name="user-defined-functions-udf"></a>Uživatelsky definované funkce (UDF)

Podregistr se dá taky rozšířit prostřednictvím **uživatelsky definovaných funkcí (UDF)**. Systém souborů UDF umožňuje implementovat funkce nebo logiku, které nejsou v HiveQL snadno modelované. Příklad použití UDF s podregistrem najdete v následujících dokumentech:

* [Použití uživatelsky definované funkce Java s Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Použití uživatelsky definované funkce Pythonu s Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Použití uživatelsky definované funkce jazyka C# s Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Postup přidání vlastní Apache Hive uživatelsky definované funkce do HDInsight](https://docs.microsoft.com/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Příklad Apache Hive uživatelsky definované funkce pro převod formátů data a času na časové razítko podregistru](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Příklad dat

Podregistr v HDInsight je předem načtený pomocí interní tabulky s názvem `hivesampletable` . HDInsight také poskytuje příklady datových sad, které se dají použít s podregistru. Tyto sady dat jsou uloženy v `/example/data` `/HdiSamples` adresářích a. Tyto adresáře existují ve výchozím úložišti pro váš cluster.

## <a name="example-hive-query"></a>Ukázkový dotaz na podregistr

Následující příkazy HiveQL vystaví sloupce projektu do `/example/data/sample.log` souboru:

```hiveql
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

V předchozím příkladu příkazy HiveQL provádějí následující akce:

|Příkaz |Popis |
|---|---|
|ODKLÁDACÍ TABULKA|Pokud tabulka již existuje, odstraňte ji.|
|VYTVOŘIT EXTERNÍ TABULKU|Vytvoří novou **externí** tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění a v původním formátu.|
|FORMÁT ŘÁDKU|Instruuje podregistr, jak jsou data formátovaná. V tomto případě jsou pole v každém protokolu oddělená mezerou.|
|ULOŽENO JAKO UMÍSTĚNÍ TEXTFILE|Říká podregistru, ve kterém jsou data uložená ( `example/data` adresář) a je uložený jako text. Data mohou být v jednom souboru nebo rozložena mezi více souborů v rámci adresáře.|
|SELECT|Vybere počet všech řádků, ve kterých sloupec **T4** obsahuje hodnotu **[Chyba]**. Tento příkaz vrátí hodnotu **3** , protože existují tři řádky, které obsahují tuto hodnotu.|
|INPUT__FILE__NAME jako je%. log|Podregistr se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Aby se zabránilo uvolňování dat ve výsledcích, tento příkaz oznamuje podregistru, že by mělo vracet pouze data ze souborů končících log. log.|

> [!NOTE]  
> Externí tabulky by měly být použity, pokud očekáváte, že budou zdrojová data aktualizována externím zdrojem. Například proces automatizovaného nahrávání dat nebo operace MapReduce.
>
> Při vyřazení externí tabulky **se data neodstraňují** , jenom definice tabulky se odstraní.

Chcete-li vytvořit **interní** tabulku místo External, použijte následující HiveQL:

```hiveql
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

Tyto příkazy provádějí následující akce:

|Příkaz |Popis |
|---|---|
|CREATE TABLE, POKUD NEEXISTUJE|Pokud tabulka neexistuje, vytvořte ji. Vzhledem k tomu, že se klíčové slovo **External** nepoužívá, vytvoří tento příkaz interní tabulku. Tabulka je uložená v datovém skladu podregistru a je plně spravovaná podregistrem.|
|ULOŽENO JAKO ORC|Ukládá data ve formátu optimalizovaného řádku (ORC). ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat z podregistru.|
|VLOŽIT PŘEPSÁNÍ... VYBRALI|Vybere řádky z tabulky **log4jLogs** , která obsahuje **[Error]**, a pak data vloží **do tabulky chyb** .|

> [!NOTE]  
> Vyřazení interní tabulky na rozdíl od externích tabulek odstraní také podkladová data.

## <a name="improve-hive-query-performance"></a>Vylepšení výkonu dotazů Hivu

### <a name="apache-tez"></a>Apache Tez

[Apache tez](https://tez.apache.org) je rozhraní, které umožňuje aplikacím náročným na data, jako je například podregistr, fungovat mnohem efektivněji ve velkém měřítku. Tez je ve výchozím nastavení povolená.  [Apache Hive v dokumentech návrhu tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) obsahuje podrobnosti o volbách implementace a ladění konfigurací.

### <a name="low-latency-analytical-processing-llap"></a>Analytické zpracování s nízkou latencí (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (někdy označované jako Live Long and Process) je nová funkce v podregistru 2,0, která umožňuje ukládání dotazů do mezipaměti v paměti. LLAP vydává dotazy na podregistr mnohem rychleji, až do [26x rychleji než v registru 1. x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight poskytuje LLAP v typu clusteru interaktivních dotazů. Další informace najdete v dokumentu [Začínáme s interaktivním dotazem](../interactive-query/apache-interactive-query-get-started.md) .

## <a name="scheduling-hive-queries"></a>Plánování dotazů Hivu

K dispozici je několik služeb, které se dají použít ke spouštění dotazů na podregistr v rámci plánu nebo pracovního postupu na vyžádání.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory umožňuje používat HDInsight jako součást kanálu Data Factory. Další informace o použití podregistru z kanálu naleznete [v tématu transformace dat pomocí aktivity podregistru v dokumentu Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) .

### <a name="hive-jobs-and-sql-server-integration-services"></a>Úlohy podregistru a služba SSIS (SQL Server Integration Services)

Ke spuštění úlohy podregistru můžete použít služba SSIS (SQL Server Integration Services) (SSIS). Sada Azure Feature Pack pro SSIS poskytuje následující komponenty, které pracují s úlohami podregistru v HDInsight.

* [Úloha pro podregistr Azure HDInsight](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Správce připojení předplatného Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Další informace najdete v dokumentaci k [Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis) .

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie je pracovní postup a systém koordinace, který spravuje úlohy systému Hadoop. Další informace o použití Oozie s podregistrem najdete v tématu věnovaném [Definování a spuštění dokumentu pracovního postupu pomocí Apache Oozie](../hdinsight-use-oozie-linux-mac.md) .

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jaký podregistr je a jak ho používat se systémem Hadoop ve službě HDInsight, můžete pomocí následujících odkazů prozkoumat další způsoby práce s Azure HDInsight.

* [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md)
* [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache prasetem v HDInsight](./python-udf-hdinsight.md)
* [Použijte úlohy MapReduce s HDInsight](hdinsight-use-mapreduce.md)
