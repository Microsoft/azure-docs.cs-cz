---
title: "Migrovat existující databáze chcete škálovat. | Microsoft Docs"
description: "Převést horizontálně dělené databáze používejte nástroje elastické databáze tak, že vytvoříte horizontálního oddílu správce mapy"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: d82994f3ab925fa3ace0d0dbe1631a01dd1df586
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrovat existující databáze chcete škálovat.
Snadno spravovat existující upraveným horizontálně dělené databáze pomocí nástrojů databáze Azure SQL Database (například [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md)). Nejdřív převést stávající sadu databází, které chcete použít [správce mapy horizontálního oddílu](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Přehled
Pokud chcete migrovat existující databázi horizontálně dělené: 

1. Příprava [horizontálního oddílu mapa správce databáze](sql-database-elastic-scale-shard-map-management.md).
2. Vytvoření mapy horizontálního oddílu.
3. Příprava jednotlivých horizontálních oddílů.  
4. Přidání mapování horizontálních mapy.

Tyto postupy můžou být implementovaná pomocí buď [Klientská knihovna pro rozhraní .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), nebo skripty prostředí PowerShell nalezený na [Azure SQL DB - skripty nástroje elastické databáze](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Zde uvedené příklady použití skriptů prostředí PowerShell.

Další informace o ShardMapManager najdete v tématu [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md). Přehled nástroje elastické databáze najdete v tématu [přehled funkcí elastické databáze](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Příprava databáze manager horizontálního oddílu mapy
Správce mapy horizontálního oddílu je speciální databázi, která obsahuje data, která mají spravovat upraveným databáze. Můžete použít existující databázi nebo vytvořte novou databázi. Databáze, který funguje jako správce mapy horizontálního oddílu by neměl být stejné databázi jako horizontálního oddílu. Skript prostředí PowerShell nevytváří databáze za vás. 

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: vytvoření horizontálního oddílu správce mapy
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Načtení správce horizontálního oddílu mapy
Po vytvoření můžete načíst správce mapy horizontálního oddílu pomocí této rutiny. Tento krok je nutný pokaždé, když potřebujete použít objekt ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Krok 2: vytvoření mapy horizontálního oddílu
Vyberte typ mapy horizontálního oddílu k vytvoření. Výběr závisí na architektuře databáze: 

1. Jednoho klienta na databázi (podmínky, najdete v článku [Glosář](sql-database-elastic-scale-glossary.md).) 
2. Několik klientů na databázi (dva typy):
   1. Seznam mapování
   2. Mapování rozsahu

Pro jednoho klienta modelu, vytvořit **seznamu mapování** horizontálního oddílu mapy. Model jednoho klienta přiřadí jednu databázi na každého klienta. Jedná se efektivní model pro vývojáře SaaS, protože ho zjednodušuje správu.

![Seznam mapování][1]

Víceklientského modelu přiřadí několik klientů jedné databáze (a skupin klientů, které můžete distribuovat mezi více databází). Pomocí tohoto modelu, pokud očekáváte, že každý klient k potřebami malá data. V tomto modelu, přiřaďte rozsah klientů k databázi pomocí **rozsah mapování**. 

![Mapování rozsahu][2]

Nebo můžete implementovat s použitím modelu víceklientské databáze *seznamu mapování* přiřadit více klientů pro jednu databázi. Například DB1 se používá k ukládání informací o klientovi ID 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10. 

![Několik klientů na jednom DB][3] 

**Podle své volby, vyberte jednu z těchto možností:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Možnost 1: vytvoření mapy horizontálního oddílu pro seznam mapování
Vytvoření mapy horizontálního oddílu pomocí ShardMapManager objektu. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Možnost 2: vytvoření mapy horizontálního oddílu pro mapování rozsahu
Chcete využít tento vzor mapování, hodnoty ID klienta musí být průběžné rozsahy a je přijatelná mezera v rozsazích přeskočením rozsahu při vytváření databáze.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Možnost 3: Seznam mapování na jedné databáze
Nastavení tohoto vzoru také vyžaduje vytvoření seznamu mapování, jak je znázorněno v kroku 2, možnost 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Příprava jednotlivých horizontálních oddílů
Přidejte každou horizontálního oddílu (databáze) pro správce mapy horizontálního oddílu. To připraví jednotlivé databáze pro ukládání informací o mapování. Tato metoda spusťte na každém horizontálního oddílu.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Krok 4: Přidejte mapování
Přidání mapování závisí na druhu mapy horizontálního oddílu, který jste vytvořili. Pokud jste vytvořili seznam mapy, můžete přidat mapování seznamu. Pokud jste vytvořili mapu rozsahu, můžete přidat mapování rozsahu.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Možnost 1: mapování dat pro mapování seznamu
Mapování dat přidáním seznamu mapování pro každého klienta.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Možnost 2: mapování dat pro mapování rozsahu
Přidáte rozsah mapování pro všechny klientské ID rozsah - přidružení databáze:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Krok 4 možnost 3: mapování dat pro více klientů v jedné databáze
Pro každého klienta spusťte ListMapping přidat (možnost 1). 

## <a name="checking-the-mappings"></a>Kontrola mapování
Informace o existující horizontálních oddílů a s nimi spojených mapování lze dotazovat pomocí následujících příkazů:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Souhrn
Po dokončení instalace můžete začít používat klientské knihovny pro elastické databáze. Můžete také použít [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md) a [dotazu víc horizontálních](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Další kroky
Získat skriptů prostředí PowerShell z [Azure SQL DB Elastická databáze nástroje skripty](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Nástroje jsou také na Githubu: [/Elastická db nástroje Azure](https://github.com/Azure/elastic-db-tools).

Použijte nástroj pro rozdělení sloučení pro přesun dat do nebo z víceklientského modelu do jednoho klienta modelu. V tématu [nástroji pro sloučení rozdělení](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Další zdroje informací:
Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce
Otázky, použijte [fórum SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro žádosti o funkce, přidejte je do [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

