---
title: Pochopení Apache Spark koncepcí kódu pro Azure Data Lake Analytics vývojářům U-SQL.
description: Tento článek popisuje Apache Spark koncepty, které vývojářům U-SQL porozumět konceptům kódu Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 2abd5882e310b17c633a82009f44624fad156f14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221124"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Pochopení kódu Apache Spark pro vývojáře U-SQL

V této části najdete pokyny vysoké úrovně pro transformaci skriptů U-SQL na Apache Spark.

- Začíná [porovnáním paradigmat pro zpracování dvou jazyků](#understand-the-u-sql-and-spark-language-and-processing-paradigms) .
- Poskytuje tipy, jak:
   - [Transformace skriptů](#transform-u-sql-scripts) včetně [výrazů sady řádků](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) U-SQL
   - [Kód .NET](#transform-net-code)
   - [Datové typy](#transform-typed-values)
   - [Objekty katalogu](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Pochopení jazyka U-SQL a Spark a zpracování paradigmat

Než začnete migrovat Azure Data Lake Analytics skripty U-SQL do Sparku, je užitečné pochopit obecný jazyk a zpracování filozofiemi těchto dvou systémů.

U-SQL je deklarativní dotazovací jazyk, který používá paradigma toku dat a umožňuje snadno vkládat a škálovat uživatelský kód napsaný v rozhraní .NET (například C#), Python a R. Rozšíření User-Extensions může implementovat jednoduché výrazy nebo uživatelsky definované funkce, ale může také poskytnout uživateli možnost implementovat tak, že se říká uživatelsky definované operátory, které implementují vlastní operátory pro provádění transformací na úrovni řádků, extrakcí a psaní výstupu.

Spark je rámec škálování na více systémů, který nabízí několik vazeb jazyka v Scala, Java, Pythonu, .NET atd. kde primárně napíšete kód v jednom z těchto jazyků, vytvořte abstrakce dat označované jako odolné distribuované datové sady (RDD), datové rámečky a datové sady a potom použijte k jejich transformaci výraz LINQ podobný jazyku (DSL). Poskytuje také SparkSQL jako deklarativní podjazyku pro rozhraní dataframe a abstrakce datových sad. DSL nabízí dvě kategorie operací, transformací a akcí. Použití transformací na abstrakce dat neprovede transformaci, ale místo toho sestaví plán spuštění, který bude odeslán k vyhodnocení akcí (například zápis výsledku do dočasné tabulky nebo souboru nebo vytištění výsledku).

Proto při překladu skriptu U-SQL do programu Spark budete muset rozhodnout, který jazyk chcete použít alespoň k vygenerování abstrakce datových rámců (aktuálně se jedná o nejčastěji používaná abstrakci dat) a zda chcete zapsat deklarativní transformace toku dat pomocí DSL nebo SparkSQL. V některých složitějších případech může být nutné rozdělit skript U-SQL do sekvence Spark a dalších kroků implementovaných pomocí Azure Batch nebo Azure Functions.

Kromě toho Azure Data Lake Analytics nabízí U-SQL v prostředí služby bez serveru, zatímco Azure Databricks a Azure HDInsight nabízí ve formě Clusterové služby Spark. Při transformaci vaší aplikace budete muset vzít v úvahu důsledky pro vytvoření, změnu velikosti, škálování a vyřazení clusterů.

## <a name="transform-u-sql-scripts"></a>Transformovat skripty U-SQL

Skripty U-SQL dodržují následující vzor zpracování:

1. Data se čtou z nestrukturovaných souborů, pomocí `EXTRACT` příkazu, specifikace umístění nebo sady souborů a integrovaného nebo uživatelsky definovaného extraktoru a požadovaného schématu nebo z tabulek U-SQL (spravované nebo externí tabulky). Je reprezentován jako sada řádků.
2. Sady řádků jsou transformovány v několika příkazech U-SQL, které pro sady řádků používají výrazy U-SQL a vytvářejí nové sady řádků.
3. Výsledné sady řádků jsou nakonec výstupem do buď souborů pomocí `OUTPUT` příkazu, který určuje umístění a vestavěný nebo uživatelsky definovaného výstupu, nebo do tabulky U-SQL.

Skript je vyhodnocen jako laxně vytvářená, což znamená, že každý krok extrakce a transformace se skládá do stromu výrazů a globálně vyhodnocen (tok dat).

Programy Spark jsou podobné jako v tom, že byste ke čtení dat a vytváření datových snímků použili konektory Spark, a pak použijete transformace na datových snímcích buď pomocí technologie LINQ, jako je například DSL, nebo SparkSQL, a pak výsledek zapíšete do souborů, dočasných tabulek Spark, některých typů programovacích jazyků nebo konzoly.

## <a name="transform-net-code"></a>Transformace kódu .NET

Jazyk výrazu U-SQL je C# a nabízí různé způsoby horizontálního navýšení kapacity vlastního kódu .NET.

Vzhledem k tomu, že Spark aktuálně neumožňuje nativně podporovat spouštění kódu .NET, budete muset své výrazy přepsat do ekvivalentního výrazu Spark, Scala, Java nebo Pythonu nebo vyhledat způsob volání do kódu .NET. Pokud váš skript používá knihovny .NET, máte následující možnosti:

- Přeložte kód .NET do Scala nebo Pythonu.
- Rozdělte skript U-SQL do několika kroků, kde použijete Azure Batch procesy pro aplikování transformací .NET (pokud můžete dosáhnout přijatelného měřítka)
- Použijte vazbu jazyka .NET dostupnou v open source s názvem Moebius. Tento projekt není v podporovaném stavu.

Pokud máte ve svých skriptech U-SQL velké množství logiky .NET, kontaktujte nás prostřednictvím zástupce účtu Microsoft a požádejte ho o další pokyny.

Následující podrobnosti jsou pro různé případy použití .NET a C# ve skriptech U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transformovat skalární vložené výrazy U-SQL C#

Jazyk výrazu U-SQL je C#. Mnohé z skalárních vložených výrazů U-SQL jsou implementovány nativně pro zlepšení výkonu, zatímco složitější výrazy mohou být provedeny prostřednictvím volání do rozhraní .NET Framework.

Spark má svůj vlastní jazyk skalárního výrazu (buď jako součást DSL nebo v SparkSQL) a umožňuje volání do uživatelsky definovaných funkcí napsaných v jeho hostujícím jazyce.

Pokud máte skalární výrazy v U-SQL, měli byste nejprve najít nejvhodnější nasrozumitelný skalární výraz Spark, který získá nejvyšší výkon a pak namapovat ostatní výrazy na uživatelsky definovanou funkci jazyka Spark dle vašeho výběru.

Počítejte s tím, že .NET a C# mají odlišnou sémantiku typu než jazyky pro hostování Sparku a DSL pro Spark. Další podrobnosti o systémových rozdílech typů najdete [níže](#transform-typed-values) .

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transformace uživatelem definovaných skalárních funkcí .NET a uživatelsky definovaných agregátorů

U-SQL poskytuje možnosti pro volání libovolných skalárních funkcí .NET a volání uživatelsky definovaných agregátorů napsaných v .NET.

Spark také nabízí podporu uživatelsky definovaných funkcí a uživatelsky definovaných agregátorů napsaných ve většině svých hostitelských jazyků, které se dají volat z DSL a SparkSQL Spark.

### <a name="transform-user-defined-operators-udos"></a>Transformace uživatelsky definovaných operátorů (Udo)

U-SQL poskytuje několik kategorií uživatelsky definovaných operátorů (Udo), jako jsou extraktory, špičkové, reduktorů, procesory, appliers a spoje, které se dají zapisovat do .NET (a-do nějakého rozsahu v Pythonu a R).

Spark nenabízí stejný model rozšiřitelnosti pro operátory, ale má ekvivalentní možnosti pro některé.

Ekvivalent Sparku pro extraktory a modul pro výstupy jsou konektory Sparku. Pro mnoho extraktorů U-SQL můžete najít ekvivalentní konektor v komunitě Spark. Pro jiné budete muset napsat vlastní konektor. Pokud je extraktor U-SQL složitý a využívá několik knihoven .NET, může být vhodnější vytvořit konektor v Scala, který používá zprostředkovatele komunikace k volání do knihovny .NET, která provádí vlastní zpracování dat. V takovém případě budete muset nasadit modul runtime .NET Core do clusteru Spark a ujistit se, že odkazované knihovny .NET jsou .NET Standard 2,0 kompatibilní.

Ostatní typy U-SQL Udo bude nutné přepsány pomocí uživatelsky definovaných funkcí a agregátorů a sémanticky vhodného výrazu Spark DLS nebo SparkSQL. Například procesor lze namapovat na výběr nejrůznějších volání UDF, zabalených jako funkci, která přebírá datový rámec jako argument a vrací datový rámec.

### <a name="transform-u-sqls-optional-libraries"></a>Transformace volitelných knihoven U-SQL

U-SQL poskytuje sadu volitelných a ukázkových knihoven, které nabízejí podporu [Pythonu](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, Avro](https://github.com/Azure/usql/tree/master/Examples/DataFormats)a některé [funkce služeb rozpoznávání](data-lake-analytics-u-sql-cognitive.md).

Spark nabízí vlastní integraci Pythonu a R, pySpark a Spark a poskytuje konektory pro čtení a zápis JSON, XML a AVRO.

Pokud potřebujete transformovat skript odkazující na knihovny služby pro rozpoznávání, doporučujeme kontaktovat nás prostřednictvím zástupce účtu Microsoft.

## <a name="transform-typed-values"></a>Transformace typových hodnot

Vzhledem k tomu, že systém typů U-SQL je založen na systému typů .NET a Spark má svůj vlastní systém typů, který je ovlivněn vazbou jazyka hostitele, bude nutné zajistit, aby typy, na kterých pracujete, byly blízko a pro určité typy, rozsahy typů, přesnost a/nebo měřítko mohou být mírně odlišné. Kromě toho jsou U-SQL a Spark zpracovávány `null` hodnoty odlišně.

### <a name="data-types"></a>Typy dat

Následující tabulka poskytuje ekvivalentní typy v Spark, Scala a PySpark pro daný typ U-SQL.

| U-SQL | Spark |  Scala | PySpark |
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

- [org. Apache. spark. SQL. Types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Typy Spark SQL a dataframes](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Typy hodnot Scala](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. SQL. typy](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Zpracování hodnoty NULL

Ve Sparku typy na výchozí povoluje hodnoty NULL při použití v U-SQL explicitně označíte skalární, neobjektový jako Nullable. Zatímco Spark umožňuje definovat sloupec, který nemůže mít hodnotu null, nevynutil omezení a [může vést k nesprávnému výsledku](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Ve Sparku hodnota NULL označuje, že hodnota je neznámá. Hodnota NULL pro Spark se liší od jakékoli hodnoty, včetně sebe samé. Porovnání mezi dvěma hodnotami NULL Spark nebo mezi hodnotou NULL a jakoukoli jinou hodnotou vrátí neznámou hodnotu, protože hodnota každé hodnoty NULL je neznámá.  

Toto chování se liší od jazyka U-SQL, který následuje sémantika jazyka C#, kde `null` se liší od jakékoli hodnoty, ale je rovna sobě.  

Proto příkaz SparkSQL `SELECT` , který používá, `WHERE column_name = NULL` vrátí nulové řádky, i když v U-SQL jsou hodnoty null, `column_name` zatímco v u-SQL by to vrátilo řádky, `column_name` na kterých je nastavena hodnota `null` . Podobně `SELECT` příkaz Spark, který používá, `WHERE column_name != NULL` vrátí nulové řádky i v případě, že v u-SQL jsou hodnoty, které nejsou null, `column_name` zatímco v u-SQL by vracely řádky, které mají jinou hodnotu než null. Proto pokud chcete, aby sémantika kontroly hodnoty U-SQL byla zaručena, měli byste použít [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) a [IsNotNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) (nebo jejich ekvivalent DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transformace objektů katalogu U-SQL

Jedním z hlavních rozdílů je, že skripty U-SQL mohou využívat své objekty katalogu, z nichž mnoho nemá přímý ekvivalent.

Spark zajišťuje podporu konceptů meta Storu v podregistru, hlavně databáze a tabulky, takže můžete mapovat databáze U-SQL a schémata na databáze podregistru a tabulky U-SQL na tabulky Spark (viz [přesun dat uložených v tabulkách u-SQL](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), ale nemá žádnou podporu pro zobrazení, funkce vracející tabulku (TVF), uložené procedury, sestavení U-SQL, externí zdroje dat atd.

Objekty kódu U-SQL, jako jsou zobrazení, TVF, uložené procedury a sestavení, mohou být modelovány prostřednictvím funkcí kódu a knihoven v Sparku a odkazovány pomocí funkcí jazyka hostitele a mechanismů procesických abstrakcí (například prostřednictvím importu modulů Pythonu nebo odkazování na funkce Scala).

Pokud byl katalog U-SQL použit ke sdílení dat a objektů kódu napříč projekty a týmy, je nutné použít ekvivalentní mechanismy pro sdílení (například Maven pro sdílení objektů kódu).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transformace výrazů sady řádků U-SQL a skalárních výrazů založených na jazyce SQL

Základní jazyk U-SQL transformuje sady řádků a je založen na SQL. Následuje nevyčerpávající seznam nejběžnějších výrazů sady řádků nabízených v U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ Agregace +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`výrazy
- `CROSS`/`OUTER``APPLY`výrazy
- `PIVOT`/`UNPIVOT` Expression
- `VALUES` konstruktor sady řádků

- Nastavit výrazy `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

U-SQL navíc poskytuje nejrůznější skalární výrazy založené na SQL, jako je například

- `OVER` výrazy okna
- celá řada předdefinovaných agregačních funkcí a funkcí řazení ( `SUM` `FIRST` atd.)
- Některé z nejoblíbenějších skalárních výrazů SQL: `CASE` , `LIKE` , ( `NOT` ) `IN` , `AND` `OR` atd.

Spark nabízí ekvivalentní výrazy ve formátu DSL i SparkSQL pro většinu těchto výrazů. Některé výrazy, které nejsou v Sparku nativně podporované, se musí přepsat pomocí kombinace nativních výrazů Spark a sémanticky ekvivalentních vzorů. Například `OUTER UNION` bude nutné přeložit na ekvivalentní kombinaci projekce a sjednocení.

Z důvodu odlišného zpracování hodnot NULL bude spojení U-SQL vždy odpovídat řádku, pokud oba sloupce jsou porovnávány, zatímco spojení ve Sparku se neshoduje s takovými sloupci, pokud nejsou přidány explicitní kontroly hodnoty null.

## <a name="transform-other-u-sql-concepts"></a>Transformace dalších konceptů U-SQL

U-SQL také nabízí celou řadu dalších funkcí a konceptů, jako jsou federované dotazy na SQL Server databáze, parametry, skalární funkce a proměnné výrazů lambda, systémové proměnné a `OPTION` Rady.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Federované dotazy na databáze SQL Server/externí tabulky

U-SQL poskytuje zdroje dat a externí tabulky a přímé dotazy na Azure SQL Database. I když Spark nenabízí stejné abstrakce objektů, poskytuje [konektor Spark pro Azure SQL Database](../azure-sql/database/spark-connector.md) , který se dá použít k dotazování databází SQL.

### <a name="u-sql-parameters-and-variables"></a>Parametry a proměnné U-SQL

Parametry a uživatelské proměnné mají ekvivalentní koncepty ve Sparku a v jejich hostujících jazycích.

Například v Scala můžete definovat proměnnou s `var` klíčovým slovem:

```
var x = 2 * 3;
println(x)
```

Systémové proměnné U-SQL (začínající `@@` na) se dají rozdělit do dvou kategorií:

- Nastavitelné systémové proměnné, které mohou být nastaveny na konkrétní hodnoty, aby ovlivnily chování skriptů
- Informativní systémové proměnné, které se dotazují na informace o úrovni systému a úlohy

Většina nastavitelných systémových proměnných nemá přímý ekvivalent ve Sparku. Některé informativní systémové proměnné lze modelovat předáním informací jako argumentů během provádění úlohy, jiné mohou mít ekvivalentní funkci v jazyku hostování Spark.

### <a name="u-sql-hints"></a>Hinty U-SQL

U-SQL nabízí několik syntaktických způsobů poskytnutí rad pro Optimalizátor dotazů a prováděcí modul:  

- Nastavení systémové proměnné U-SQL
- `OPTION`klauzule přidružená ke výrazu sady řádků pro zadání pomocného parametru data nebo plánu
- pomocný parametr Join v syntaxi výrazu Join (například `BROADCASTLEFT` )

Nástroj pro optimalizaci dotazů založený na ceně Sparku má své vlastní funkce k poskytování pomocných parametrů a ladění výkonu dotazů. Informace najdete v příslušné dokumentaci.

## <a name="next-steps"></a>Další kroky

- [Principy formátů dat Spark pro vývojáře U-SQL](understand-spark-data-formats.md)
- [.NET pro Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Upgradujte řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Transformuje data pomocí aktivity Sparku v Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformuje data pomocí aktivity podregistru Hadoop v Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co je Apache Spark ve službě Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)