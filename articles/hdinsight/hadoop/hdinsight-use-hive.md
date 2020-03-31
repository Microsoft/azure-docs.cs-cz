---
title: Co je Apache Hive a HiveQL - Azure HDInsight
description: Apache Hive je systém datového skladu pro Apache Hadoop. Můžete dotazovat data uložená v Hive pomocí HiveQL, které podobné Transact-SQL. V tomto dokumentu se dozvíte, jak používat Hive a HiveQL s Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471349"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Co je Apache Hive a HiveQL na Azure HDInsight?

[Apache Hive](https://hive.apache.org/) je systém datového skladu pro Apache Hadoop. Hive umožňuje sumarzování dat, dotazování a analýzu dat. Hive dotazy jsou zapsány v HiveQL, což je dotazovací jazyk podobný SQL.

Hive umožňuje promítat strukturu na převážně nestrukturovaná data. Po definování struktury můžete pomocí HiveQL dotazovat data bez znalosti Java nebo MapReduce.

HDInsight poskytuje několik typů clusterů, které jsou naladěny pro konkrétní úlohy. Pro dotazy Hive se nejčastěji používají následující typy clusterů:

|Typ clusteru |Popis|
|---|---|
|Interaktivní dotaz|Cluster Hadoop, který poskytuje funkce [analytického zpracování s nízkou latencí (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) pro zlepšení doby odezvy pro interaktivní dotazy. Další informace najdete v [dokumentu Start with Interactive Query in HDInsight.](../interactive-query/apache-interactive-query-get-started.md)|
|Hadoop|Cluster Hadoop, který je naladěn na úlohy dávkového zpracování. Další informace najdete v [dokumentu Start with Apache Hadoop in HDInsight.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)|
|Spark|Apache Spark má vestavěnou funkci onoa pro práci s Hive. Další informace najdete v [dokumentu Start with Apache Spark on HDInsight.](../spark/apache-spark-jupyter-spark-sql.md)|
|HBase|HiveQL lze použít k dotazování dat uložených v Apache HBase. Další informace najdete v [dokumentu Start with Apache HBase on HDInsight.](../hbase/apache-hbase-tutorial-get-started-linux.md)|

## <a name="how-to-use-hive"></a>Jak používat Hive

V následující tabulce můžete zjistit různé způsoby použití Hive s HDInsight:

| **Tuto metodu použijte,** pokud chcete... | ... **interaktivní** dotazy | ... **dávkové** zpracování | ... z tohoto **klientského operačního systému** |
|:--- |:---:|:---:|:--- |:--- |
| [Nástroje HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X nebo Windows |
| [Nástroje HDInsight pro Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Zobrazení Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Jakékoliv (založené na prohlížeči) |
| [Klient Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X nebo Windows |
| [ROZHRANÍ API PRO ODPOČINEK](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X nebo Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Referenční příručka jazyka HiveQL

Odkaz na jazyk HiveQL je k dispozici v [jazykové příručce](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Úl a datová struktura

Hive chápe, jak pracovat se strukturovanými a částečně strukturovanými daty. Například textové soubory, kde jsou pole oddělena určitými znaky. Následující příkaz HiveQL vytvoří tabulku nad daty oddělenými prostorem:

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

Hive také podporuje vlastní **serializátor/deserializers (SerDe)** pro komplexní nebo nepravidelně strukturovaná data. Další informace naleznete v tématu [Jak používat vlastní JSON SerDe s HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) dokumentu.

Další informace o formátech souborů podporovaných hive naleznete v [příručce jazyk (https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Podregistr interní tabulky vs externí tabulky

Existují dva typy tabulek, které můžete vytvořit pomocí Hive:

* __Interní__: Data jsou uložena v datovém skladu Hive. Datový sklad je `/hive/warehouse/` umístěn ve výchozím úložišti pro cluster.

    Interní tabulky použijte, pokud platí jedna z následujících podmínek:

    * Data jsou dočasná.
    * Chcete, aby Hive řídil životní cyklus tabulky a dat.

* __Externí__: Data jsou uložena mimo datový sklad. Data mohou být uložena v libovolném úložišti přístupném clusterem.

    Externí tabulky použijte, pokud platí jedna z následujících podmínek:

    * Data se používají také mimo Hive. Například datové soubory jsou aktualizovány jiným procesem (který neuzamkne soubory.)
    * Data musí zůstat v podkladovém umístění, a to i po uvolnění tabulky.
    * Potřebujete vlastní umístění, například účet úložiště, který není výchozí.
    * Formát dat, umístění a tak dále spravuje jiný program než podregistr.

Další informace najdete v příspěvku na blogu [Intro intro intro intro v oblasti intro hive.](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/)

## <a name="user-defined-functions-udf"></a>Uživatelem definované funkce (UDF)

Hive lze také rozšířit prostřednictvím **uživatelem definovaných funkcí (UDF)**. UDF umožňuje implementovat funkce nebo logiku, která není snadno modelována v HiveQL. Příklad použití udf s Hive, naleznete v následujících dokumentech:

* [Použití uživatelem definované funkce Java s Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Použití uživatelem definované funkce Pythonu s Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Použití uživatelem definované funkce Jazyka C# s Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Jak přidat vlastní uživatelem definovanou funkci Apache Hive do HDInsightu](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Příklad uživatelem definované funkce Apache Hive pro převod formátů data a času na časové razítko Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Příklad dat

Hive na HDInsight je předinstalovaný s `hivesampletable`interní tabulkou s názvem . HDInsight také poskytuje ukázkové datové sady, které lze použít s Hive. Tyto sady dat jsou `/example/data` `/HdiSamples` uloženy v adresářích a. Tyto adresáře existují ve výchozím úložišti pro váš cluster.

## <a name="example-hive-query"></a>Příklad dotazu Hive

Následující příkazy HiveQL promítají do souboru `/example/data/sample.log` sloupce:

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

V předchozím příkladu příkazy HiveQL provádět následující akce:

|Příkaz |Popis |
|---|---|
|DROP TABULKA|Pokud tabulka již existuje, odstraňte ji.|
|VYTVOŘIT EXTERNÍ TABULKU|Vytvoří novou **externí** tabulku v Podregistru. Externí tabulky ukládají pouze definici tabulky v Hive. Data jsou ponechána v původním umístění a v původním formátu.|
|FORMÁT ŘÁDKU|Řekne Hive, jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělena mezerou.|
|ULOŽENÉ JAKO UMÍSTĚNÍ TEXTOVÉHO SOUBORU|Sdělí Hive, kde jsou `example/data` data uložena (adresář) a že jsou uložena jako text. Data mohou být v jednom souboru nebo rozložena do více souborů v adresáři.|
|SELECT|Vybere počet všech řádků, ve kterých sloupec **t4** obsahuje hodnotu **[ERROR]**. Tento příkaz vrátí hodnotu **3,** protože existují tři řádky, které obsahují tuto hodnotu.|
|INPUT__FILE__NAME JAKO '%.log'|Hive se pokusí použít schéma pro všechny soubory v adresáři. V tomto případě adresář obsahuje soubory, které neodpovídají schématu. Chcete-li zabránit vrácení dat ve výsledcích, tento příkaz říká Hive, že bychom měli vracet data pouze ze souborů končících na .log.|

> [!NOTE]  
> Externí tabulky by měly být použity, pokud očekáváte, že podkladová data budou aktualizována externím zdrojem. Například automatizovaný proces odesílání dat nebo operace MapReduce.
>
> Uvolněním externí tabulky **data neodstraníte,** odstraní se pouze definice tabulky.

Chcete-li vytvořit **interní** tabulku místo externí, použijte následující HiveQL:

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

Tyto příkazy provádět následující akce:

|Příkaz |Popis |
|---|---|
|Vytvořit tabulku, pokud neexistuje|Pokud tabulka neexistuje, vytvořte ji. Vzhledem k tomu, že klíčové slovo **EXTERNAL** se nepoužívá, vytvoří tento příkaz vnitřní tabulku. Tabulka je uložena v datovém skladu Hive a je zcela spravována společností Hive.|
|ULOŽENO JAKO ORC|Ukládá data ve formátu Optimalizovaný řádek Sloupcový (ORC). ORC je vysoce optimalizovaný a efektivní formát pro ukládání dat Hive.|
|VLOŽIT PŘEPIŠTE ... Vyberte|Vybere řádky z tabulky **log4jLogs,** která obsahuje **[ERROR],** a potom vloží data do tabulky **errorLogs.**|

> [!NOTE]  
> Na rozdíl od externích tabulek odstranění maže ní také podkladová data.

## <a name="improve-hive-query-performance"></a>Vylepšení výkonu dotazů Hivu

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) je framework, který umožňuje aplikacím náročným na data, jako je Hive, běžet mnohem efektivněji ve velkém měřítku. Tez je ve výchozím nastavení povolen.  [Dokumenty návrhu Apache Hive na Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) obsahují podrobnosti o možnostech implementace a konfiguracích ladění.

### <a name="low-latency-analytical-processing-llap"></a>Analytické zpracování s nízkou latencí (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (někdy označované jako Live Long a Process) je nová funkce v Hive 2.0, která umožňuje ukládání dotazů do mezipaměti v paměti. LLAP umožňuje hive dotazy mnohem rychleji, až [26x rychlejší než Hive 1.x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight poskytuje LLAP v typu clusteru interaktivních dotazů. Další informace naleznete v dokumentu [Start with Interactive Query.](../interactive-query/apache-interactive-query-get-started.md)

## <a name="scheduling-hive-queries"></a>Plánování dotazů Hivu

Existuje několik služeb, které lze použít ke spuštění dotazů Hive jako součást naplánovaného pracovního postupu nebo pracovního postupu na vyžádání.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory umožňuje používat HDInsight jako součást kanálu datové továrny. Další informace o použití Hive z kanálu najdete v tématu [transformace dat pomocí aktivity Hive v](../../data-factory/transform-data-using-hadoop-hive.md) dokumentu Azure Data Factory.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Úlohy hive a integrační služby SQL Server

Ke spuštění úlohy Hive můžete použít službu SQL Server Integration Services (SSIS). Sada Funkcí Azure pro SSIS poskytuje následující součásti, které fungují s úlohami Hive na HDInsightu.

* [Úloha hive Azure HDInsight](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Správce připojení předplatného Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Další informace najdete v dokumentaci [k azure feature pack.](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

### <a name="apache-oozie"></a>Apač Oozie

Apache Oozie je workflow a koordinační systém, který spravuje hadoop pracovních míst. Další informace o použití Oozie s Hive, naleznete [v tématu Použití Apache Oozie definovat a spustit dokument pracovního postupu.](../hdinsight-use-oozie-linux-mac.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli, co je Hive a jak ho používat s Hadoopem v HDInsightu, použijte následující odkazy k prozkoumání dalších způsobů práce s Azure HDInsight.

* [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md)
* [Použití uživatelem definovaných funkcí Pythonu (UDF) s Apache Hive a Apache Pig v HDInsightu](./python-udf-hdinsight.md)
* [Použijte úlohy MapReduce s HDInsight](hdinsight-use-mapreduce.md)
