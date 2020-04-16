---
title: Sdílená databáze Azure Synapse Analytics
description: Azure Synapse Analytics poskytuje model sdílených metadat, kde vytvoření databáze v Apache Spark zpřístupní z jeho SQL na vyžádání (preview) a SQL pool motory.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424143"
---
# <a name="azure-synapse-analytics-shared-database"></a>Sdílená databáze Azure Synapse Analytics

Azure Synapse Analytics umožňuje různým výpočetním modulům pracovního prostoru sdílet databáze a tabulky mezi fondy Spark (preview), sql on-demand (preview) engine a SQL fondy.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Databáze vytvořená pomocí úlohy Spark se stane viditelnou se stejným názvem pro všechny aktuální a budoucí fondy Spark (náhled) v pracovním prostoru i v modulu SQL na vyžádání.

Pokud jsou v pracovním prostoru fondy SQL, které mají povolenou synchronizaci metadat, nebo pokud vytvoříte nový fond SQL s povolenou synchronizací metadat, tyto databáze vytvořené sparkum se automaticky namapují na speciální schémata v databázi fondu SQL. 

Každé schéma je pojmenováno po názvu databáze `$` Spark s další předponou. Externí i spravované tabulky v databázi generované spark jsou vystaveny jako externí tabulky v odpovídající speciální schéma.

Výchozí databáze Spark, `default`nazývaná , bude také viditelná v `default`kontextu NA vyžádání SQL jako databáze s názvem a v libovolné `$default`databázi fondu SQL se synchronizací metadat zapnutou jako schéma .

Vzhledem k tomu, že databáze jsou synchronizovány s SQL na vyžádání a fondy SQL asynchronně, bude zpoždění, dokud se nezobrazí.

## <a name="manage-a-spark-created-database"></a>Správa vytvořené databáze Spark

Pomocí Spark umíte spravovat vytvořené databáze Spark. Odstraňte ji například prostřednictvím úlohy fondu Spark a vytvořte v ní tabulky ze Sparku.

Pokud vytvoříte objekty v databázi vytvořené Spark pomocí SQL na vyžádání, nebo zkuste přetažení databáze, operace bude úspěšná. Původní databáze Spark se však nezmění.

Pokud se pokusíte vynechat synchronizované schéma ve fondu SQL nebo se pokusíte vytvořit tabulku v něm, Azure vrátí chybu.

## <a name="handling-of-name-conflicts"></a>Zpracování konfliktů názvů

Pokud je název databáze Spark v konfliktu s názvem existující databáze SQL na vyžádání, přípona je připojena v SQL na vyžádání do databáze Spark. Přípona v SQL na `_<workspace name>-ondemand-DefaultSparkConnector`vyžádání je .

Například pokud spark databáze `mydb` s názvem vytvoří v pracovním `myws` prostoru Azure Synapse a databáze NA VYŽÁDÁNÍ SQL s tímto názvem již existuje, pak `mydb_myws-ondemand-DefaultSparkConnector`databáze Spark v SQL na vyžádání bude muset odkazovat pomocí názvu .

> [!CAUTION]
> Upozornění: Neměli byste mít závislost na toto chování.

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark spolu s jejich synchronizovanými reprezentacemi v sql engines budou zabezpečeny na základní úrovni úložiště.

Objekt zabezpečení, který vytvoří databázi, je považován za vlastníka této databáze a má všechna práva k databázi a jejím objektům.

Chcete-li poskytnout zaregistrovaný objekt zabezpečení, například uživateli nebo skupině zabezpečení, přístup k databázi, zadejte `warehouse` příslušná oprávnění ke složce A souborům POSIX podkladovým složkám a souborům v adresáři. 

Například aby zaregistrovaný objekt zabezpečení mohl číst tabulku v databázi, musí mít `warehouse` `X` všechny složky `R` začínající ve složce databáze v adresáři oprávnění a oprávnění přiřazená tomuto objektu zabezpečení. Soubory (například podkladové datové soubory tabulky) `R` navíc vyžadují oprávnění. 

Pokud objekt zabezpečení vyžaduje možnost vytvářet objekty nebo `W` přetažení objektů v databázi, `warehouse` jsou vyžadována další oprávnění pro složky a soubory ve složce.

## <a name="examples"></a>Příklady

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Vytvoření & připojení k databázi Spark – SQL na vyžádání

Nejprve vytvořte novou `mytestdb` databázi Spark s názvem pomocí clusteru Spark, který jste už vytvořili ve svém pracovním prostoru. Toho můžete dosáhnout například pomocí poznámkového bloku Spark C# s následujícím příkazem .NET pro Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krátké prodlevě můžete zobrazit databázi z SQL na vyžádání. Spusťte například následující příkaz z jazyka SQL na vyžádání.

```sql
SELECT * FROM sys.databases;
```

Ověřte, že `mytestdb` je součástí výsledků.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Vystavení databáze Spark ve fondu SQL

S databází vytvořenou v předchozím příkladu nyní vytvořte `mysqlpool` fond SQL ve vašem pracovním prostoru s názvem, který umožňuje synchronizaci metadat.

Spusťte následující `mysqlpool` příkaz proti fondu SQL:

```sql
SELECT * FROM sys.schema;
```

Ověřte schéma pro nově vytvořenou databázi ve výsledcích.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure Synapse Analytics](overview.md)
- [Další informace o sdílených tabulkách metadat Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
