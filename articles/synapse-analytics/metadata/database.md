---
title: Sdílená databáze
description: Azure synapse Analytics poskytuje sdílený model metadat, ve kterém se vytváří databáze ve fondu Apache Spark bez serveru, takže bude přístupný z fondu SQL bez serveru a z fondů SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 14ae8b8670db53761d085bc019711e829633601b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451810"
---
# <a name="azure-synapse-analytics-shared-database"></a>Sdílená databáze Azure synapse Analytics

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Apache Spark serverů bez serveru a neserverovým fondem SQL.

Databáze vytvořená pomocí úlohy Spark se bude zobrazovat se stejným názvem jako všechny aktuální a budoucí fondy Sparku v pracovním prostoru, včetně neserverového fondu SQL.

Výchozí databáze Spark, která je volána `default` , bude také viditelná v kontextu fondu SQL bez serveru jako databáze s názvem `default` .

Vzhledem k tomu, že jsou databáze synchronizovány do fondu SQL bez serveru asynchronně, dojde k prodlevě, dokud nebudou zobrazeny.

## <a name="manage-a-spark-created-database"></a>Správa databáze Spark vytvořené

Pomocí Sparku můžete spravovat databáze Spark vytvořené. Můžete ho například odstranit pomocí úlohy fondu Spark a vytvořit v něm tabulky z Sparku.

Pokud vytvoříte objekty v databázi Spark vytvořené pomocí SQL fondu bez serveru, nebo se pokusíte databázi odpojit, operace bude úspěšná. Původní databáze Spark se ale nemění.

## <a name="how-name-conflicts-are-handled"></a>Jak se zpracovávají konflikty názvů

Pokud název databáze Spark koliduje s názvem existující databáze fondu SQL bez serveru, je přípona připojena do fondu SQL bez serveru do databáze Spark. Přípona v neserverovém fondu SQL je `_<workspace name>-ondemand-DefaultSparkConnector` .

Pokud se například `mydb` v pracovním prostoru Azure synapse vytvoří databáze Spark `myws` a databáze fondu SQL bez serveru s tímto názvem už existuje, pak se na databázi Sparku ve fondu SQL bez serveru bude odkazovat pomocí názvu `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Upozornění: při tomto chování byste neměli mít závislost.

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark spolu s jejich synchronizovanými reprezentacemi v modulu SQL budou zabezpečeny na základní úrovni úložiště.

Objekt zabezpečení, který vytvoří databázi, se považuje za vlastníka databáze a má všechna práva k databázi a jejím objektům.

Chcete-li objektu zabezpečení, například uživateli nebo skupině zabezpečení, získat přístup k databázi, zadejte příslušnou složku POSIX a oprávnění k souborům v `warehouse` adresáři. 

Například aby objekt zabezpečení mohl číst tabulku v databázi, musí mít všechny složky, které začínají ve složce databáze v `warehouse` adresáři, `X` `R` oprávnění a oprávnění přiřazená tomuto objektu zabezpečení. Kromě toho soubory (například podkladové datové soubory tabulky) vyžadují `R` oprávnění. 

Pokud objekt zabezpečení vyžaduje možnost vytvářet objekty nebo vyřadit objekty v databázi, `W` jsou pro složky a soubory ve složce vyžadovány další oprávnění `warehouse` .

## <a name="examples"></a>Příklady

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Vytvoření a připojení k databázi Spark s neserverovým fondem SQL

Nejdřív vytvořte novou databázi Spark s názvem `mytestdb` pomocí clusteru Spark, který jste už vytvořili v pracovním prostoru. Toho můžete dosáhnout například pomocí poznámkového bloku Spark C# s následujícím příkazem .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krátké prodlevě můžete zobrazit databázi z fondu SQL bez serveru. Například spusťte následující příkaz z fondu SQL bez serveru.

```sql
SELECT * FROM sys.databases;
```

Ověřte, že `mytestdb` je součástí výsledků.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure synapse Analytics](overview.md)
- [Další informace o sdílených tabulkách metadat služby Azure synapse Analytics](table.md)
