---
title: Sdílená databáze Azure synapse Analytics
description: Azure synapse Analytics poskytuje sdílený model metadat, ve kterém se při vytváření databáze v Apache Spark zpřístupní z jeho modulů SQL na vyžádání (Preview) a fondů SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424143"
---
# <a name="azure-synapse-analytics-shared-database"></a>Sdílená databáze Azure synapse Analytics

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Spark (Preview), modulem SQL na vyžádání (Preview) a fondy SQL.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Databáze vytvořená pomocí úlohy Spark se bude zobrazovat se stejným názvem jako všechny aktuální a budoucí fondy Spark (Preview) v pracovním prostoru i v modulu SQL na vyžádání.

Pokud v pracovním prostoru existují fondy SQL, které mají povolenou synchronizaci metadat, nebo pokud vytvoříte nový fond SQL s povolenou synchronizací metadat, tyto databáze Spark vytvořené v databázi ve fondu SQL budou automaticky namapovány na speciální schémata. 

Každé schéma má název za názvem databáze Spark s další `$` předponou. Externí i spravované tabulky v databázi generované ve Sparku jsou zpřístupněné jako externí tabulky v odpovídajícím speciálním schématu.

Výchozí databáze `default`Spark se také zobrazí v kontextu SQL na vyžádání jako databáze s názvem `default`a v některé z databází fondu SQL se zapnutou synchronizací metadat jako se schématem. `$default`

Vzhledem k tomu, že jsou databáze synchronizovány do SQL na vyžádání a z asynchronních fondů SQL, dojde k prodlevě, dokud se nezobrazí.

## <a name="manage-a-spark-created-database"></a>Správa databáze Spark vytvořené

Pomocí Sparku můžete spravovat databáze Spark vytvořené. Můžete ho například odstranit pomocí úlohy fondu Spark a vytvořit v něm tabulky z Sparku.

Při vytváření objektů v databázi Spark vytvořené pomocí SQL na vyžádání nebo při pokusu o vyřazení databáze bude operace úspěšná. Původní databáze Spark se ale nemění.

Pokud se pokusíte odstranit synchronizované schéma ve fondu SQL nebo se v něm pokusíte vytvořit tabulku, Azure vrátí chybu.

## <a name="handling-of-name-conflicts"></a>Zpracování konfliktů názvů

Pokud název databáze Spark koliduje s názvem existující databáze SQL na vyžádání, připojí se k databázi Spark přípona v SQL na vyžádání. Přípona v SQL na vyžádání je `_<workspace name>-ondemand-DefaultSparkConnector`.

Pokud je například databáze Spark s názvem `mydb` vytvořena v pracovním prostoru `myws` Azure synapse a databáze SQL na vyžádání s tímto názvem již existuje, bude na vyžádání databáze Spark v SQL na vyžádání odkazována pomocí názvu. `mydb_myws-ondemand-DefaultSparkConnector`

> [!CAUTION]
> Upozornění: při tomto chování byste neměli mít závislost.

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark spolu s jejich synchronizovanými reprezentacemi v modulech SQL budou zabezpečeny na základní úrovni úložiště.

Objekt zabezpečení, který vytváří databázi, je považován za vlastníka této databáze a má všechna práva k databázi a jejím objektům.

Chcete-li objektu zabezpečení, například uživateli nebo skupině zabezpečení, získat přístup k databázi, zadejte příslušnou složku POSIX a oprávnění k souborům v `warehouse` adresáři. 

Například aby objekt zabezpečení mohl číst tabulku v databázi, musí mít `warehouse` `X` všechny složky, které začínají ve složce databáze v adresáři, oprávnění a `R` oprávnění přiřazená tomuto objektu zabezpečení. Kromě toho soubory (například podkladové datové soubory tabulky) vyžadují `R` oprávnění. 

Pokud objekt zabezpečení vyžaduje možnost vytvářet objekty nebo vyřadit objekty v databázi, jsou pro složky a `W` soubory ve `warehouse` složce vyžadovány další oprávnění.

## <a name="examples"></a>Příklady

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Vytvoření & připojení k databázi Spark – SQL na vyžádání

Nejdřív vytvořte novou databázi Spark s názvem `mytestdb` pomocí clusteru Spark, který jste už vytvořili v pracovním prostoru. Toho můžete dosáhnout například pomocí poznámkového bloku Spark C# s následujícím příkazem .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krátké prodlevě můžete zobrazit databázi z SQL na vyžádání. Například spusťte následující příkaz z SQL na vyžádání.

```sql
SELECT * FROM sys.databases;
```

Ověřte, `mytestdb` že je součástí výsledků.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Vystavení databáze Spark ve fondu SQL

Databáze vytvořená v předchozím příkladu teď vytvoří ve svém pracovním prostoru fond SQL s názvem `mysqlpool` , který umožňuje synchronizaci metadat.

Spusťte následující příkaz pro fond `mysqlpool` SQL:

```sql
SELECT * FROM sys.schema;
```

Ověřte schéma pro nově vytvořenou databázi ve výsledcích.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure synapse Analytics](overview.md)
- [Další informace o sdílených tabulkách metadat služby Azure synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
