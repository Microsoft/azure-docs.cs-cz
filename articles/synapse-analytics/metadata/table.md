---
title: Sdílené tabulky metadat
description: Azure synapse Analytics poskytuje sdílený model metadat, ve kterém se vytvoří tabulka v Apache Spark bude přístupná z jeho modulů SQL na vyžádání (Preview) a fondů SQL serveru, aniž by bylo třeba duplikovat data.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: d19376d21081d899d8ff7226c6d7c5b76267fabf
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280464"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Sdílené tabulky metadat Azure synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky založené na Parquet mezi svými Apache Spark fondy (Preview) a SQL na vyžádání (Preview).

Jakmile se databáze vytvoří pomocí úlohy Sparku, můžete v ní vytvářet tabulky pomocí Sparku, která jako formát úložiště používá Parquet. Tyto tabulky budou okamžitě k dispozici pro dotazování pomocí všech fondů Sparku v pracovním prostoru Azure synapse. Lze je také použít ze všech úloh Spark podléhajících oprávněním.

Vytvořené, spravované a externí tabulky Spark jsou také k dispozici jako externí tabulky se stejným názvem v odpovídající synchronizované databázi v SQL na vyžádání. Vystavení [tabulky Spark v SQL](#expose-a-spark-table-in-sql) poskytuje další podrobnosti o synchronizaci tabulek.

Vzhledem k tomu, že se tabulky synchronizují na vyžádání SQL asynchronně, dojde k prodlevě, dokud se nezobrazí.

## <a name="manage-a-spark-created-table"></a>Spravovat tabulku vytvořenou v Sparku

Pomocí Sparku můžete spravovat databáze Spark vytvořené. Můžete ho například odstranit pomocí úlohy fondu Spark a vytvořit v něm tabulky z Sparku.

Pokud vytvoříte objekty v takové databázi z SQL na vyžádání nebo zkusíte databázi odpojit, operace bude úspěšná, ale původní databáze Spark se nemění.

## <a name="expose-a-spark-table-in-sql"></a>Vystavení tabulky Spark v SQL

### <a name="shared-spark-tables"></a>Sdílené tabulky Spark

Spark nabízí dva typy tabulek, které Azure synapse zveřejňuje v SQL automaticky:

- Spravované tabulky

  Spark nabízí mnoho možností, jak ukládat data ve spravovaných tabulkách, jako je TEXT, CSV, JSON, JDBC, PARQUET, ORC, PODREGISTR, DELTA a LIBSVM. Tyto soubory jsou obvykle uloženy v `warehouse` adresáři, ve kterém jsou uložena data spravované tabulky.

- Externí tabulky

  Spark také nabízí způsoby vytváření externích tabulek přes existující data, a to buď zadáním `LOCATION` Možnosti, nebo použitím formátu podregistru. Tyto externí tabulky můžou být v nejrůznějších formátech dat, včetně Parquet.

Azure synapse aktuálně sdílí jenom spravované a externí tabulky Sparku, které ukládají svá data ve formátu Parquet pomocí modulů SQL. Tabulky s jinými formáty se automaticky nesynchronizují. Pokud modul SQL podporuje základní formát tabulky, je možné, že tyto tabulky budou explicitně synchronizovány jako externí tabulky ve vlastní databázi SQL.

### <a name="share-spark-tables"></a>Sdílení tabulek Spark

Spravované a externí tabulky Spark vystavené v modulu SQL jako externí tabulky s následujícími vlastnostmi:

- Zdroj dat externí tabulky SQL je zdroj dat reprezentující složku umístění tabulky Spark.
- Formát souboru v externí tabulce SQL je Parquet.
- Přístupové pověření k externí tabulce SQL je průchozí.

Vzhledem k tomu, že všechny názvy tabulek Spark jsou platné názvy tabulek SQL a všechny názvy sloupců Sparku jsou platné názvy sloupců SQL, použijí se názvy tabulek Spark a sloupců pro externí tabulku SQL.

Tabulky Spark poskytují různé datové typy než synapse moduly SQL. V následující tabulce jsou namapovány datové typy tabulky Spark na typy SQL:

| Typ dat Spark | Datový typ SQL | Komentáře |
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
| `string`    |    `varchar(max)`   | S kolací `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializace do formátu JSON pomocí kolace `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serializace do formátu JSON pomocí kolace `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serializace do formátu JSON pomocí kolace `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark i jejich synchronizované reprezentace v modulu SQL budou zabezpečeny na základní úrovni úložiště. Vzhledem k tomu, že aktuálně nemají oprávnění k samotným objektům, lze objekty zobrazit v Průzkumníku objektů.

Objekt zabezpečení, který vytváří spravovanou tabulku, se považuje za vlastníka této tabulky a má všechna práva k tabulce a také základní složky a soubory. Kromě toho se vlastník databáze automaticky stane spoluvlastníkem tabulky.

Pokud vytvoříte externí tabulku Spark nebo SQL s předávacím ověřováním, data budou zabezpečena pouze na úrovni složky a souboru. Pokud někdo zadá dotaz na tento typ externí tabulky, identita zabezpečení odesílatele dotazu se předává do systému souborů, ve kterém se budou kontrolovat přístupová práva.

Další informace o tom, jak nastavit oprávnění pro složky a soubory, najdete v tématu [sdílená databáze Azure synapse Analytics](database.md).

## <a name="examples"></a>Příklady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Vytvoření spravované tabulky založené na Parquet ve Sparku a dotazování z SQL na vyžádání

V tomto scénáři máte databázi Spark s názvem `mytestdb` . Přečtěte si téma [Vytvoření a připojení k databázi Spark pomocí SQL na vyžádání](database.md#create-and-connect-to-spark-database-with-sql-on-demand).

Spuštěním následujícího příkazu vytvořte spravovanou tabulku Spark pomocí SparkSQL:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Tento příkaz vytvoří tabulku `myParquetTable` v databázi `mytestdb` . Po krátké prodlevě uvidíte tabulku v SQL na vyžádání. Například spusťte následující příkaz z SQL na vyžádání.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Ověřte, že `myParquetTable` je součástí výsledků.

>[!NOTE]
>Tabulka, která nepoužívá Parquet jako formát úložiště, nebude synchronizována.

Dále vložte do tabulky některé hodnoty ze Sparku, například s následujícími příkazy C# Spark v poznámkovém bloku C#:

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

Teď můžete data z SQL na vyžádání přečíst následujícím způsobem:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Jako výsledek byste měli získat následující řádek:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Vytvoření externí tabulky založené na Parquet ve Sparku a dotazování z SQL na vyžádání

V tomto příkladu vytvořte externí tabulku Spark přes Parquet datové soubory, které byly vytvořeny v předchozím příkladu pro spravovanou tabulku.

Například pomocí SparkSQL spusťte:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Zástupný text nahraďte `<fs>` názvem systému souborů, který je výchozím systémem souborů v pracovním prostoru, a zástupným symbolem `<synapse_ws>` s názvem synapse pracovního prostoru, který používáte ke spuštění tohoto příkladu.

Předchozí příklad vytvoří tabulku `myExtneralParquetTable` v databázi `mytestdb` . Po krátké prodlevě uvidíte tabulku v SQL na vyžádání. Například spusťte následující příkaz z SQL na vyžádání.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Ověřte, že `myExternalParquetTable` je součástí výsledků.

Teď můžete data z SQL na vyžádání přečíst následujícím způsobem:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Jako výsledek byste měli získat následující řádek:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure synapse Analytics](overview.md)
- [Další informace o sdílené databázi metadat služby Azure synapse Analytics](database.md)


