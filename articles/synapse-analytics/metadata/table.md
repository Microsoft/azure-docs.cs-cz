---
title: Sdílené tabulky metadat
description: Azure synapse Analytics poskytuje sdílený model metadat, ve kterém při vytváření tabulky v neserverovém fondu Apache Spark bude přístupný z fondu SQL bez serveru a vyhrazeného fondu SQL bez duplikování dat.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: a8080720480beaeb7bc8692f2dcddddad5da0e3c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548457"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Sdílené tabulky metadat Azure synapse Analytics


Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky založené na Parquet mezi jeho fondy Apache Spark a neserverovým fondem SQL.

Jakmile se databáze vytvoří pomocí úlohy Sparku, můžete v ní vytvářet tabulky pomocí Sparku, která jako formát úložiště používá Parquet. Názvy tabulek budou převedeny na malá písmena a musí být dotazovány pomocí malých názvů písmen. Tyto tabulky budou okamžitě k dispozici pro dotazování pomocí všech fondů Sparku v pracovním prostoru Azure synapse. Lze je také použít ze všech úloh Spark podléhajících oprávněním.

Vytvořené, spravované a externí tabulky Spark jsou také k dispozici jako externí tabulky se stejným názvem v odpovídající synchronizované databázi ve fondu SQL bez serveru. Vystavení [tabulky Spark v SQL](#expose-a-spark-table-in-sql) poskytuje další podrobnosti o synchronizaci tabulek.

Vzhledem k tomu, že jsou tabulky synchronizovány do fondu SQL bez serveru asynchronně, dojde k prodlevě, dokud nebudou zobrazeny.

## <a name="manage-a-spark-created-table"></a>Spravovat tabulku vytvořenou v Sparku

Pomocí Sparku můžete spravovat databáze Spark vytvořené. Můžete ho například odstranit pomocí úlohy fondu Apache Spark bez serveru a vytvořit v něm tabulky ze Sparku.

Při vytváření objektů v takové databázi z fondu SQL bez serveru nebo při pokusu o vyřazení databáze dojde k selhání operace. Původní databázi Spark nejde změnit přes server SQL bez serveru.

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

Objekt zabezpečení, který vytvoří spravovanou tabulku, se považuje za vlastníka tabulky a má všechna práva k tabulce i k základním složkám a souborům. Vlastník databáze se navíc automaticky stane spoluvlastníkem tabulky.

Pokud vytvoříte externí tabulku Sparku nebo SQL s předávacím ověřováním, zabezpečení dat probíhá pouze na úrovni složek a souborů. Pokud někdo odešle dotaz na tento typ externí tabulky, identita zabezpečení odesílatele dotazu se předá do systému souborů, který zkontroluje přístupová práva.

Další informace o tom, jak nastavit oprávnění pro složky a soubory, najdete v tématu [sdílená databáze Azure synapse Analytics](database.md).

## <a name="examples"></a>Příklady

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Vytvoření spravované tabulky s použitím Parquet ve Sparku a dotazem z fondu SQL bez serveru

V tomto scénáři máte databázi Spark s názvem `mytestdb` . Přečtěte si téma [Vytvoření a připojení k databázi Spark s fondem SQL bez serveru](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Spuštěním následujícího příkazu vytvořte spravovanou tabulku Spark pomocí SparkSQL:

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

Tento příkaz vytvoří tabulku `myparquettable` v databázi `mytestdb` . Názvy tabulek budou převedeny na malá písmena. Po krátké prodlevě uvidíte tabulku v neserveru SQL fondu. Například spusťte následující příkaz z fondu SQL bez serveru.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Ověřte, že `myparquettable` je součástí výsledků.

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
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

Nyní můžete číst data z fondu SQL bez serveru, a to následujícím způsobem:

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

Jako výsledek byste měli získat následující řádek:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Vytvoření externí tabulky s použitím Parquet ve Sparku a dotazem z fondu SQL bez serveru

V tomto příkladu vytvořte externí tabulku Spark přes Parquet datové soubory, které byly vytvořeny v předchozím příkladu pro spravovanou tabulku.

Například pomocí SparkSQL spusťte:

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Zástupný text nahraďte `<fs>` názvem systému souborů, který je výchozím systémem souborů v pracovním prostoru, a zástupným symbolem `<synapse_ws>` s názvem synapse pracovního prostoru, který používáte ke spuštění tohoto příkladu.

Předchozí příklad vytvoří tabulku `myextneralparquettable` v databázi `mytestdb` . Po krátké prodlevě uvidíte tabulku v neserveru SQL fondu. Například spusťte následující příkaz z fondu SQL bez serveru.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Ověřte, že `myexternalparquettable` je součástí výsledků.

Nyní můžete číst data z fondu SQL bez serveru, a to následujícím způsobem:

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
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


