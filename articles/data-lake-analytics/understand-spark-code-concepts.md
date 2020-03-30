---
title: Seznamte se s koncepty kódu Apache Spark pro vývojáře U-SQL Azure Data Lake Analytics.
description: Tento článek popisuje koncepty Apache Spark, které vývojářům U-SQL pomáhají porozumět konceptům kódu Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423999"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Principy kódu Apache Spark pro vývojáře U-SQL

Tato část obsahuje podrobné pokyny k transformaci U-SQL skriptů na Apache Spark.

- Začíná [srovnáním paradigmat zpracování obou jazyků](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Obsahuje tipy, jak:
   - [Transformace skriptů](#transform-u-sql-scripts) včetně [výrazů sady řádků](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) U-SQL
   - [Kód .NET](#transform-net-code)
   - [Typy dat](#transform-typed-values)
   - [Objekty katalogu](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Porozumění jazyku U-SQL a Spark a paradigmatům zpracování

Než začnete migrovat u-SQL skripty Azure Data Lake Analytics do Sparku, je užitečné porozumět obecnému jazyku a zpracovávat filozofie obou systémů.

U-SQL je deklarativní dotazovací jazyk podobný SQL, který používá paradigma toku dat a umožňuje snadno vložit a škálovat uživatelský kód napsaný v rozhraní .NET (například C#), Python a R. Rozšíření uživatele můžete implementovat jednoduché výrazy nebo uživatelem definované funkce, ale může také poskytnout uživateli možnost implementovat takzvané uživatelem definované operátory, které implementují vlastní operátory k provádění transformace na úrovni sady řádků, extrakce a zápis výstupu.

Spark je škálovatenový framework nabízející několik jazykových vazeb v Scala, Java, Pythonu, .NET atd., kde primárně píšete svůj kód v jednom z těchto jazyků, vytváříte abstrakce dat nazývané odolné distribuované datové sady (RDD), datové rámce a datové sady a pak použijte linq jako jazyk specifický pro doménu (DSL) k jejich transformaci. Poskytuje také SparkSQL jako deklarativní podjazyk na datovém rámci a abstrakce datové sady. DSL poskytuje dvě kategorie operací, transformace a akce. Použití transformace na odběry dat nebude provádět transformace, ale místo toho vybudovat plán spuštění, který bude odeslán k vyhodnocení s akcí (například zápis výsledku do dočasné tabulky nebo souboru nebo tisk výsledek).

Při překladu skriptu U-SQL do programu Spark se tedy budete muset rozhodnout, který jazyk chcete použít alespoň ke generování abstrakce datového rámce (což je v současné době nejčastěji používaná abstrakce dat) a zda chcete napsat deklarativní transformace toku dat pomocí DSL nebo SparkSQL. V některých složitějších případech možná budete muset rozdělit skript U-SQL do posloupnosti Spark a dalších kroků implementovaných pomocí Azure Batch nebo Azure Functions.

Azure Data Lake Analytics navíc nabízí U-SQL v prostředí pracovních služeb bez serveru, zatímco Azure Databricks i Azure HDInsight nabízejí Spark ve formě clusterové služby. Při transformaci aplikace budete muset vzít v úvahu důsledky nyní vytváření, změny velikosti, škálování a vyřazení clusterů z provozu.

## <a name="transform-u-sql-scripts"></a>Transformace skriptů U-SQL

Skripty U-SQL postupujte podle následujícího vzoru zpracování:

1. Data se čtou buď z `EXTRACT` nestrukturovaných souborů pomocí příkazu, specifikace umístění nebo sady souborů a vestavěného nebo uživatelem definovaného extraktoru a požadovaného schématu, nebo z tabulek U-SQL (spravovaných nebo externích tabulek). Je reprezentován jako sada řádků.
2. Sady řádků získat transformována ve více příkazech U-SQL, které platí U-SQL výrazy na sady řádků a vytvářet nové sady řádků.
3. Nakonec výsledné sady řádků jsou výstup do `OUTPUT` buď soubory pomocí příkazu, který určuje umístění a vestavěný nebo uživatelem definované výstupní nebo do tabulky U-SQL.

Skript je vyhodnocován líně, což znamená, že každý krok extrakce a transformace je složen do stromu výrazů a globálně vyhodnocen (tok dat).

Spark programy jsou podobné v tom, že byste použít Spark konektory pro čtení dat a vytvořit datové rámce, pak použít transformace na datových rámcích pomocí buď LINQ-jako DSL nebo SparkSQL, a pak zapsat výsledek do souborů, dočasné Spark tabulky, některé typy programovacích jazyků nebo konzolu.

## <a name="transform-net-code"></a>Transformace kódu .NET

Jazyk výrazu U-SQL je C# a nabízí řadu způsobů, jak škálovat vlastní kód .NET.

Vzhledem k tomu, že Spark momentálně nepodporuje provádění kódu .NET, budete muset buď přepsat výrazy do ekvivalentního výrazu Spark, Scala, Java nebo Pythonu, nebo najít způsob, jak volat do kódu .NET. Pokud skript používá knihovny .NET, máte následující možnosti:

- Přeložte kód .NET do Scala nebo Pythonu.
- Rozdělit u-SQL skript do několika kroků, kde použijete Azure Batch procesy použít .NET transformace (pokud můžete získat přijatelné škálování)
- Použijte jazykovou vazbu .NET dostupnou v Open Source s názvem Moebius. Tento projekt není v podporovaném stavu.

V každém případě, pokud máte velké množství logiky .NET ve skriptech U-SQL, kontaktujte nás prostřednictvím zástupce účtu Microsoft pro další pokyny.

Následující podrobnosti jsou pro různé případy použití .NET a C# ve skriptech U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transformace skalárních výrazů U-SQL C#

Jazyk výrazu U-SQL je C#. Mnoho skalárních vložkových výrazů U-SQL je nativně implementováno pro zlepšení výkonu, zatímco složitější výrazy mohou být spuštěny prostřednictvím volání do rozhraní .NET Framework.

Spark má svůj vlastní skalární jazyk výrazu (buď jako součást DSL nebo v SparkSQL) a umožňuje volání do uživatelem definovaných funkcí napsaných v hostitelském jazyce.

Pokud máte skalární výrazy v U-SQL, měli byste nejprve najít nejvhodnější nativně srozumitelný skalární výraz Spark, abyste získali co největší výkon, a pak namapovat ostatní výrazy do uživatelem definované funkce hostitelského jazyka Spark podle vašeho výběru.

Uvědomte si, že .NET a C# mají jiný typ sémantiky než Spark hostování jazyků a Dsl Spark. Další podrobnosti o rozdílech v systému typů naleznete [níže.](#transform-typed-values)

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transformace uživatelem definovaných skalárních funkcí .NET a uživatelem definovaných agregátorů

U-SQL poskytuje způsoby, jak volat libovolné skalární funkce .NET a volat uživatelem definované agregátory napsané v rozhraní .NET.

Spark také nabízí podporu pro uživatelem definované funkce a uživatelem definované agregátory napsané ve většině svých hostitelských jazyků, které lze volat z DSL a SparkSQL SparkSQL.

### <a name="transform-user-defined-operators-udos"></a>Transformace uživatelem definovaných operátorů (UDO)

U-SQL poskytuje několik kategorií uživatelem definovaných operátorů (UDO), jako jsou extraktory, výstupy, reduktory, procesory, appliery a slučovače, které mohou být napsány v .NET (a - do jisté míry - v Pythonu a R).

Spark nenabízí stejný model rozšiřitelnosti pro operátory, ale má ekvivalentní funkce pro některé.

Spark ekvivalent extraktory a outputters je Spark konektory. Pro mnoho U-SQL extraktory můžete najít ekvivalentní konektor v komunitě Spark. Pro ostatní budete muset napsat vlastní konektor. Pokud je extraktor U-SQL složitý a využívá několik knihoven .NET, může být vhodnější vytvořit konektor v systému Scala, který používá interop k volání do knihovny .NET, která provádí skutečné zpracování dat. V takovém případě budete muset nasadit runtime jádra .NET do clusteru Spark a ujistěte se, že odkazované knihovny .NET jsou kompatibilní se standardem .NET Standard 2.0.

Ostatní typy U-SQL UDO bude nutné přepsat pomocí uživatelem definované funkce a agregátory a sémanticky vhodné Spark DLS nebo SparkSQL výraz. Například procesor může být mapován na SELECT různých vyvolání UDF, zabaleny jako funkce, která bere datový rámec jako argument a vrátí datový rámec.

### <a name="transform-u-sqls-optional-libraries"></a>Transformace volitelných knihoven U-SQL

U-SQL poskytuje sadu volitelných a ukázkových knihoven, které nabízejí [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, AVRO podporu](https://github.com/Azure/usql/tree/master/Examples/DataFormats)a některé [funkce kognitivních služeb](data-lake-analytics-u-sql-cognitive.md).

Spark nabízí vlastní integraci Pythonu a R, pySpark a SparkR a poskytuje konektory pro čtení a zápis JSON, XML a AVRO.

Pokud potřebujete změnit skript odkazující na knihovny kognitivních služeb, doporučujeme nás kontaktovat prostřednictvím zástupce účtu Microsoft.

## <a name="transform-typed-values"></a>Transformovat zadané hodnoty

Vzhledem k tomu, že typový systém U-SQL je založen na systému typu .NET a Spark má svůj vlastní typový systém, který je ovlivněn vazbou hostitelského jazyka, budete se muset ujistit, že typy, na kterých pracujete, jsou blízko a pro určité typy se rozsahy typů, přesnost a/nebo měřítko mohou mírně lišit. Kromě toho U-SQL a `null` Spark zacházet s hodnotami odlišně.

### <a name="data-types"></a>Typy dat

Následující tabulka obsahuje ekvivalentní typy v Spark, Scala a PySpark pro dané typy U-SQL.

| U-SQL | Spark |  Scala | PySpark (PySka |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Další informace naleznete v tématu:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Typy spark SQL a datových rámců](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Typy hodnot Scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Léčba NULL

Ve Spark, typy na výchozí povolit hodnoty NULL zatímco v U-SQL, explicitně označit skalární, non-objekt jako nullable. Zatímco Spark umožňuje definovat sloupec jako nelze stanovit hodnotitelné, nebude vynucovat omezení a [může vést k nesprávnému výsledku](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

V Spark, NULL označuje, že hodnota není známa. Hodnota NULL Spark se liší od jakékoli hodnoty, včetně samotné. Porovnání mezi dvěma hodnotami Spark NULL nebo mezi hodnotou NULL a jakoukoli jinou hodnotou vrátí neznámý, protože hodnota každé hodnoty NULL není známa.  

Toto chování se liší od U-SQL, který následuje `null` C# sémantiku, kde se liší od jakékoli hodnoty, ale rovná se sám sobě.  

Proto SparkSQL `SELECT` příkaz, `WHERE column_name = NULL` který používá vrátí nula `column_name`řádků i v případě, že jsou `column_name` hodnoty NULL `null`v , zatímco v U-SQL, vrátí řádky, kde je nastavena na . Podobně Spark `SELECT` příkaz, který `WHERE column_name != NULL` používá vrátí nula řádků i `column_name`v případě, že jsou hodnoty bez null v aplikaci , zatímco v U SQL, vrátí řádky, které mají non-null. Proto pokud chcete U-SQL null-check sémantiku, měli byste použít [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) a [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) v uvedeném pořadí (nebo jejich ekvivalent DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transformace objektů katalogu U-SQL

Jeden hlavní rozdíl je, že U-SQL skripty můžete využít jeho katalogu objektů, z nichž mnohé nemají žádný přímý ekvivalent Spark.

Spark poskytuje podporu pro koncepty úložiště Hive Meta, hlavně databáze a tabulky, takže můžete mapovat U-SQL databáze a schémata na databáze Hive a tabulky U-SQL na tabulky Spark (viz [Přesouvání dat uložených v tabulkách U-SQL),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)ale nemá žádnou podporu pro zobrazení, funkce s hodnotou tabulky (TVF), uložené procedury, u-SQL sestavení, externí zdroje dat atd.

Objekty kódu U-SQL, jako jsou zobrazení, TVF, uložené procedury a sestavení, mohou být modelovány pomocí funkcí kódu a knihoven v aplikaci Spark a odkazovány pomocí funkcí hostitelského jazyka a mechanismů procedurální abstrakce (například prostřednictvím importu Pythonu nebo odkazování na funkce Scala).

Pokud katalog U-SQL byl použit ke sdílení dat a objektů kódu mezi projekty a týmy, pak ekvivalentní mechanismy pro sdílení musí být použity (například Maven pro sdílení objektů kódu).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transformace výrazů sady řádků U-SQL a skalárních výrazů založených na SQL

Základní jazyk U-SQL transformuje sady řádků a je založen na SQL. Následuje nevyčerpávající seznam nejběžnějších výrazů sady řádků nabízených v u-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Agregáty+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` výrazy
- `CROSS`/`OUTER``APPLY` výrazy
- `PIVOT`/`UNPIVOT`Výrazy
- `VALUES`konstruktor sady řádků

- Nastavení výrazů`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Kromě toho U-SQL poskytuje řadu skalárních výrazů založených na SQL, jako jsou

- `OVER`výrazy okna
- různé vestavěné agregátory a funkce hodnocení`SUM` `FIRST` ( , atd.)
- Některé z nejznámějších skalárních výrazů `CASE`SQL: `AND`, `OR` `LIKE`, (`NOT`) `IN`, atd.

Spark nabízí ekvivalentní výrazy ve formuláři DSL i SparkSQL pro většinu těchto výrazů. Některé výrazy, které nejsou v Sparku nativně podporovány, budou muset být přepsány pomocí kombinace nativních výrazů Spark a sémanticky ekvivalentních vzorů. Například `OUTER UNION` bude muset být přeložendo ekvivalentní kombinace projekcí a odborů.

Z důvodu různých zpracování hodnot NULL spojení U-SQL bude vždy odpovídat řádek, pokud oba sloupce, které jsou porovnávány obsahují hodnotu NULL, zatímco spojení v Spark nebude odpovídat tyto sloupce, pokud jsou přidány explicitní null kontroly.

## <a name="transform-other-u-sql-concepts"></a>Transformace dalších konceptů U-SQL

U-SQL také nabízí řadu dalších funkcí a konceptů, jako jsou federované dotazy proti sql server databází, parametry, skalární `OPTION` a lambda výraz proměnné, systémové proměnné, rady.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Federované dotazy na databáze/externí tabulky serveru SQL Server

U-SQL poskytuje zdroj dat a externí tabulky, stejně jako přímé dotazy na Azure SQL Database. Zatímco Spark nenabízí stejné abstrakce objektů, poskytuje [konektor Spark pro Azure SQL Database,](../sql-database/sql-database-spark-connector.md) který se dá použít k dotazování databází SQL.

### <a name="u-sql-parameters-and-variables"></a>Parametry a proměnné U-SQL

Parametry a uživatelské proměnné mají ekvivalentní koncepty ve Sparku a jejich hostitelských jazycích.

Například v Scala, můžete definovat `var` proměnnou s klíčovým slovem:

```
var x = 2 * 3;
println(x)
```

Systémové proměnné U-SQL (proměnné začínající) `@@`lze rozdělit do dvou kategorií:

- Nastavitelné systémové proměnné, které lze nastavit na určité hodnoty, které mají vliv na chování skriptů
- Proměnné informačního systému, které zjišnou informace o systému a úrovni práce

Většina nastavitelných systémových proměnných nemá v Sparku žádný přímý ekvivalent. Některé proměnné informačního systému lze modelovat předáním informací jako argumentů během provádění úloh, jiné mohou mít ekvivalentní funkci v hostitelském jazyce Spark.

### <a name="u-sql-hints"></a>Rady pro U-SQL

U-SQL nabízí několik syntaktických způsobů, jak poskytnout rady pro optimalizaci dotazů a spuštění motoru:  

- Nastavení systémové proměnné U-SQL
- klauzule přidružená `OPTION` k výrazu sady řádků pro poskytnutí dat nebo nápovědy plánu
- nápověda spojení v syntaxi výrazu spojení `BROADCASTLEFT`(například)

Optimalizátor dotazů založený na nákladech spark má své vlastní možnosti, které poskytují rady a vyladí výkon dotazu. Přečtěte si prosím příslušnou dokumentaci.

## <a name="next-steps"></a>Další kroky

- [Principy datových formátů Spark pro vývojáře U-SQL](understand-spark-data-formats.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Upgradujte svá řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Transformace dat pomocí aktivity Spark v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformace dat pomocí aktivity Hadoop Hive v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
