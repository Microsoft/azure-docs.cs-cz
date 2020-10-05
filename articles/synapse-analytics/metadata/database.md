---
title: Sdílená databáze
description: Azure synapse Analytics poskytuje sdílený model metadat, ve kterém se při vytváření databáze v Apache Spark zpřístupní z jeho modulů SQL na vyžádání (Preview) a fondů SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 58c1aea944d89872a79d0672a925b1696791c1a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260848"
---
# <a name="azure-synapse-analytics-shared-database"></a>Sdílená databáze Azure synapse Analytics

Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi jeho fondy Spark (Preview) a modulem SQL na vyžádání (Preview).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Databáze vytvořená pomocí úlohy Spark se bude zobrazovat se stejným názvem jako všechny aktuální a budoucí fondy Spark (Preview) v pracovním prostoru, včetně modulu SQL na vyžádání.

Výchozí databáze Spark, která je volána `default` , bude v kontextu SQL na vyžádání zobrazena také jako databáze s názvem `default` .

Vzhledem k tomu, že se databáze synchronizují na vyžádání SQL asynchronně, dojde k prodlevě, dokud se nezobrazí.

## <a name="manage-a-spark-created-database"></a>Správa databáze Spark vytvořené

Pomocí Sparku můžete spravovat databáze Spark vytvořené. Můžete ho například odstranit pomocí úlohy fondu Spark a vytvořit v něm tabulky z Sparku.

Při vytváření objektů v databázi Spark vytvořené pomocí SQL na vyžádání nebo při pokusu o vyřazení databáze bude operace úspěšná. Původní databáze Spark se ale nemění.

## <a name="how-name-conflicts-are-handled"></a>Jak se zpracovávají konflikty názvů

Pokud název databáze Spark koliduje s názvem existující databáze SQL na vyžádání, připojí se k databázi Spark přípona v SQL na vyžádání. Přípona v SQL na vyžádání je `_<workspace name>-ondemand-DefaultSparkConnector` .

Pokud je například databáze Spark `mydb` s názvem vytvořena v pracovním prostoru Azure synapse `myws` a databáze SQL na vyžádání s tímto názvem již existuje, bude na vyžádání databáze Spark v SQL na vyžádání odkazována pomocí názvu `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Upozornění: při tomto chování byste neměli mít závislost.

## <a name="security-model"></a>Model zabezpečení

Databáze a tabulky Spark spolu s jejich synchronizovanými reprezentacemi v modulu SQL budou zabezpečeny na základní úrovni úložiště.

Objekt zabezpečení, který vytváří databázi, je považován za vlastníka této databáze a má všechna práva k databázi a jejím objektům.

Chcete-li objektu zabezpečení, například uživateli nebo skupině zabezpečení, získat přístup k databázi, zadejte příslušnou složku POSIX a oprávnění k souborům v `warehouse` adresáři. 

Například aby objekt zabezpečení mohl číst tabulku v databázi, musí mít všechny složky, které začínají ve složce databáze v `warehouse` adresáři, `X` `R` oprávnění a oprávnění přiřazená tomuto objektu zabezpečení. Kromě toho soubory (například podkladové datové soubory tabulky) vyžadují `R` oprávnění. 

Pokud objekt zabezpečení vyžaduje možnost vytvářet objekty nebo vyřadit objekty v databázi, `W` jsou pro složky a soubory ve složce vyžadovány další oprávnění `warehouse` .

## <a name="examples"></a>Příklady

### <a name="create-and-connect-to-spark-database-with-sql-on-demand"></a>Vytvoření a připojení k databázi Spark pomocí SQL na vyžádání

Nejdřív vytvořte novou databázi Spark s názvem `mytestdb` pomocí clusteru Spark, který jste už vytvořili v pracovním prostoru. Toho můžete dosáhnout například pomocí poznámkového bloku Spark C# s následujícím příkazem .NET for Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Po krátké prodlevě můžete zobrazit databázi z SQL na vyžádání. Například spusťte následující příkaz z SQL na vyžádání.

```sql
SELECT * FROM sys.databases;
```

Ověřte, že `mytestdb` je součástí výsledků.

## <a name="next-steps"></a>Další kroky

- [Další informace o sdílených metadatech Azure synapse Analytics](overview.md)
- [Další informace o sdílených tabulkách metadat služby Azure synapse Analytics](table.md)
