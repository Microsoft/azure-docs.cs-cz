---
title: Tabulky sdílených metadat Azure Synapse Analytics
description: Azure Synapse Analytics poskytuje model sdílených metadat, kde vytvoření tabulky v Apache Spark zpřístupní z jeho SQL na vyžádání (preview) a SQL pool motory bez duplikování dat.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424577"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics sdílené tabulky metadat

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure Synapse Analytics umožňuje různým výpočetním modulům pracovního prostoru sdílet databáze a tabulky podporované parketami mezi fondy Apache Spark (preview), sql modulem na vyžádání (preview) a fondy SQL.

Jakmile je databáze vytvořena úlohou Spark, můžete v ní vytvořit tabulky se Sparkem, které používají parkety jako formát úložiště. Tyto tabulky budou okamžitě k dispozici pro dotazování podle některého z fondů Azure Synapse pracovního prostoru Spark. Lze je také použít z libovolné úlohy Spark, která podléhá oprávněním.

Vytvořené, spravované a externí tabulky Spark jsou také k dispozici jako externí tabulky se stejným názvem v `$`odpovídající synchronizované databázi v SQL na vyžádání a v odpovídajících schématech s předponou ve fondech SQL, které mají povolenou synchronizaci metadat. [Vystavení tabulky Spark v SQL](#exposing-a-spark-table-in-sql) poskytuje další podrobnosti o synchronizaci tabulky.

Vzhledem k tomu, že tabulky jsou synchronizovány s SQL na vyžádání a fondy SQL asynchronně, bude zpoždění, dokud se zobrazí.

Mapování tabulek na externí tabulky, zdroje dat a formáty souborů.

## <a name="manage-a-spark-created-table"></a>Správa tabulky vytvořené Sparkem

Pomocí Spark umíte spravovat vytvořené databáze Spark. Odstraňte ji například prostřednictvím úlohy fondu Spark a vytvořte v ní tabulky ze Sparku.

Pokud vytvoříte objekty v takové databázi z SQL na vyžádání nebo se pokusíte přetažení databáze, operace bude úspěšná, ale původní databáze Spark se nezmění.

Pokud se pokusíte vynechat synchronizované schéma ve fondu SQL nebo se pokusíte vytvořit tabulku v něm, Azure vrátí chybu.

## <a name="exposing-a-spark-table-in-sql"></a>Vystavení tabulky Spark v SQL

### <a name="which-spark-tables-are-shared"></a>Které stoly Spark jsou sdíleny

Spark poskytuje dva typy tabulek, které Azure Synapse automaticky zveřejňuje v SQL:

- Spravované tabulky

  Spark poskytuje mnoho možností pro ukládání dat ve spravovaných tabulkách, jako je TEXT, CSV, JSON, JDBC, PARKETY, ORC, HIVE, DELTA a LIBSVM. Tyto soubory jsou obvykle `warehouse` uloženy v adresáři, kde jsou uložena data spravované tabulky.

- Externí tabulky

  Spark také poskytuje způsoby, jak vytvořit externí `LOCATION` tabulky nad existujícími daty, a to buď poskytnutím možnosti, nebo pomocí formátu Hive. Tyto externí tabulky mohou být v různých datových formátech, včetně parket.

Azure Synapse aktuálně sdílí jenom spravované a externí tabulky Spark, které ukládají svá data ve formátu Parkety s moduly SQL. Tabulky podporované jinými formáty nejsou automaticky synchronizovány. Tyto tabulky můžete explicitně synchronizovat sami jako externí tabulku ve vlastní databázi SQL, pokud modul SQL podporuje základní formát tabulky.

### <a name="how-are-spark-tables-shared"></a>Jak se svitami Spark sdílejí

Sdílené spravované a externí sparkuovací tabulky vystavené v sql engines jako externí tabulky s následujícími vlastnostmi:

- Zdroj dat externí tabulky SQL je zdroj dat představující složku umístění tabulky Spark.
- Formát souboru externí tabulky SQL je Parketa.
- Přístupové pověření externí tabulky SQL je předávací.

Vzhledem k tomu, že všechny názvy tabulek Spark jsou platné názvy tabulek SQL a všechny názvy sloupců Spark jsou platné názvy sloupců SQL, budou pro externí tabulku SQL použity názvy tabulek A Spark.

Spark tabulky poskytují různé typy dat než synapse SQL motory. Následující tabulka mapuje datové typy tabulek Spark na typy SQL:

| Datový typ Jiskry | Datový typ SQL | Komentáře |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Se řazením`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializuje do JSON s řazení`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serializuje do JSON s řazení`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serializuje do JSON s řazení`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark, stejně jako jejich synchronizované reprezentace v sql engines budou zabezpečeny na základní úrovni úložiště. Vzhledem k tomu, že aktuálně nemají oprávnění k samotným objektům, objekty lze zobrazit v průzkumníku objektů.

Objekt zabezpečení, který vytvoří spravovanou tabulku, je považován za vlastníka této tabulky a má všechna práva k tabulce i k podkladovým složkám a souborům. Kromě toho se vlastník databáze automaticky stane spoluvlastníkem tabulky.

Pokud vytvoříte externí tabulku Spark nebo SQL s předávací maškarní, data jsou zabezpečena pouze na úrovni složky a souboru. Pokud se někdo dotazuje na tento typ externí tabulky, identita zabezpečení předkladatele dotazu je předána systému souborů, který zkontroluje přístupová práva.

Další informace o nastavení oprávnění pro složky a soubory naleznete v [tématu Sdílená databáze Azure Synapse Analytics](database.md).

## <a name="examples"></a>Příklady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Vytvoření spravované tabulky podpořené parketami v Sparku a dotazování z SQL na vyžádání

V tomto scénáři máte databázi Spark s názvem `mytestdb`. Viz [Vytvoření & připojení k databázi Spark – SQL na vyžádání](database.md#create--connect-to-spark-database---sql-on-demand).

Vytvořte spravovanou tabulku Spark se SparkSQL spuštěním následujícího příkazu:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Tím se `myParquetTable` vytvoří tabulka `mytestdb`v databázi . Po krátké prodlevě můžete vidět tabulku v SQL na vyžádání. Spusťte například následující příkaz z jazyka SQL na vyžádání.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Ověřte, že `myParquetTable` je součástí výsledků.

>[!NOTE]
>Tabulka, která nepoužívá parkety jako formát úložiště, nebude synchronizována.

Dále vložte některé hodnoty do tabulky ze Sparku, například s následujícími příkazy C# Spark v poznámkovém bloku C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Nyní můžete číst data z SQL na vyžádání takto:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Měli byste získat následující řádek jako výsledek:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Vytvoření externí tabulky podpořené parketami v Sparku a dotazování z SQL na vyžádání

V tomto příkladu vytvořte externí tabulku Spark nad datovými soubory parket, které byly vytvořeny v předchozím příkladu pro spravovanou tabulku.

Například s Spuštění SparkSQL:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Nahraďte `<fs>` zástupný symbol názvem systému souborů, který je `<synapse_ws>` výchozím systémem souborů pracovního prostoru, a zástupným symbolem název synapse pracovního prostoru, který používáte ke spuštění tohoto příkladu.

Předchozí příklad vytvoří `myExtneralParquetTable` tabulku v `mytestdb`databázi . Po krátké prodlevě můžete vidět tabulku v SQL na vyžádání. Spusťte například následující příkaz z jazyka SQL na vyžádání.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Ověřte, že `myExternalParquetTable` je součástí výsledků.

Nyní můžete číst data z SQL na vyžádání takto:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Měli byste získat následující řádek jako výsledek:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Dotazování tabulek Spark ve fondu SQL

S tabulkami vytvořenými v předchozích příkladech teď vytvořte `mysqlpool` fond SQL ve vašem pracovním prostoru s názvem, který umožňuje synchronizaci metadat (nebo použijte již vytvořený fond z [vystavení databázi Spark ve fondu SQL](database.md#exposing-a-spark-database-in-a-sql-pool).

Spusťte následující `mysqlpool` příkaz proti fondu SQL:

```sql
SELECT * FROM sys.tables;
```

Ověřte, `myParquetTable` `myExternalParquetTable` zda jsou tabulky a `$mytestdb`jsou viditelné ve schématu .

Nyní můžete číst data z SQL na vyžádání takto:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

Měli byste získat stejné výsledky jako u SQL na vyžádání výše.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure Synapse Analytics](overview.md)
- [Další informace o sdílených tabulkách metadat Azure Synapse Analytics](table.md)


