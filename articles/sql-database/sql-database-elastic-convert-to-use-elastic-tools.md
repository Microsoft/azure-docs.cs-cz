---
title: Migrace existujících databází pro horizontální navýšení kapacity | Dokumentace Microsoftu
description: Převést na používejte nástroje elastické databáze správce mapování vytvořením horizontálního oddílu horizontálně dělené databáze
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 8449462f144590e4fe7048366a21090c95a303cb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455588"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrace existujících databází pro horizontální navýšení kapacity
Snadno spravovat stávající horizontálně dělené databáze horizontálním navýšením kapacity pomocí nástroje pro databáze Azure SQL Database (například [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md)). Nejprve převést stávající sadu databází pro použití [správce mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Přehled
Migrace stávající horizontálně dělené databáze: 

1. Příprava [databáze správce mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md).
2. Vytvoření mapy horizontálních oddílů.
3. Příprava jednotlivých horizontálních oddílů.  
4. Přidáte mapování na mapy horizontálních oddílů.

Tyto postupy, je možné implementovat pomocí buď [Klientská knihovna pro .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), nebo skripty prostředí PowerShell najdete na [databáze Azure SQL – skripty nástrojů Elastic Database](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Příklady v tomto článku pomocí skriptů prostředí PowerShell.

Další informace o ShardMapManager najdete v tématu [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md). Přehled nástroje pro elastické databáze najdete v tématu [přehled funkcí Elastic Database](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Příprava databáze správce mapování horizontálních oddílů
Správce mapování horizontálních oddílů je speciální databáze, která obsahuje data, která mají Správa databází s horizontálním navýšením kapacity. Můžete použít stávající databázi nebo vytvořte novou databázi. Databáze, který funguje jako správce mapování horizontálních oddílů nesmí být stejné databázi jako horizontálního oddílu. Skript prostředí PowerShell nevytváří databáze za vás. 

## <a name="step-1-create-a-shard-map-manager"></a>Krok 1: vytvoření horizontálního oddílu správce mapování
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Načíst správce mapování horizontálních oddílů
Po vytvoření můžete načíst správce mapování horizontálních oddílů pomocí této rutiny. Tento krok je nutný pokaždé, když se budete muset použít ShardMapManager objektu.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Krok 2: vytvoření mapy horizontálních oddílů
Vyberte typ mapy horizontálních oddílů k vytvoření. Výběr závisí na architektuře databáze: 

1. Jednoho tenanta na databázi (podmínky, najdete v článku [Glosář](sql-database-elastic-scale-glossary.md).) 
2. Více tenantů na databázi (dva typy):
   1. Seznam mapování
   2. Mapování oblasti

Pro jednoho tenanta modelu, vytvořit **mapování seznamu** mapy horizontálních oddílů. Model jednoho tenanta přiřadí jednu databázi tenanta. To je účinným modelem pro vývojáře SaaS, protože zjednodušuje správu.

![Seznam mapování][1]

Víceklientského modelu přiřadí několik tenantů pro jednotlivé databáze (a skupin klientů můžete distribuovat napříč několika databázemi). Tento model použijte, pokud očekáváte, že každý tenant má malé dat, které potřebujete. V tomto modelu, přiřaďte rozsah tenantů k databázi pomocí **rozsah mapování**. 

![Mapování oblasti][2]

Nebo můžete implementovat pomocí modelu databázi s více tenanty *mapování seznamu* přiřadit více tenantů pro jednotlivé databáze. Například DB1 se používá k ukládání informací o tenantovi ID 1 a 5 a DB2 ukládá data pro tenanta 7 a tenanta 10. 

![Více tenantů v jedné databáze][3] 

**Na základě vaší volby, vyberte jednu z těchto možností:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Možnost 1: vytvoření mapy horizontálních oddílů pro mapování seznamu
Vytvoření mapy horizontálních oddílů pomocí ShardMapManager objektu. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Možnost 2: vytvoření mapy horizontálních oddílů pro mapování rozsahu
Chcete-li využívat tento model mapování, tenant ID hodnoty musí být průběžné rozsahy a je přijatelná mezeru v oblasti přeskočením rozsahu při vytváření databáze.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>Možnost 3: Seznam mapování na jednotlivé databáze
Nastavení tento vzor vyžaduje vytvoření mapy seznamů také, jak je znázorněno v kroku 2, možnost 1.

## <a name="step-3-prepare-individual-shards"></a>Krok 3: Příprava jednotlivých horizontálních oddílů
Každý horizontální oddíl (databáze) přidáte do správce mapování horizontálních oddílů. To připraví jednotlivé databáze pro ukládání informací o mapování. Proveďte tuto metodu pro každý horizontální oddíl.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Krok 4: Přidat mapování
Přidání mapování závisí na druhu mapy horizontálních oddílů, který jste vytvořili. Pokud jste vytvořili mapy seznamů, přidejte seznam mapování. Pokud jste vytvořili mapy rozsahů, přidejte rozsah mapování.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Možnost 1: mapování dat pro mapování seznamu
Mapování dat tak, že přidáte seznam mapování pro každého tenanta.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Možnost 2: mapování dat pro mapování rozsahu
Přidáte rozsah mapování pro všechny tenant ID rozsahu - přidružení databáze:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Krok 4 – možnost 3: mapování dat pro více tenantů v jednotlivých databází
Pro každého klienta spouštění ListMapping přidat (možnost 1). 

## <a name="checking-the-mappings"></a>Kontroluje se mapování
Informace o mapování k nim má přiřazené a existující horizontálních oddílů může být dotázán pomocí následujících příkazů:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Souhrn
Po dokončení instalace můžete začít používat Klientská knihovna Elastic Database. Můžete také použít [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) a [dotazování více horizontálních oddílů](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Další postup
Získání skriptů prostředí PowerShell z [Azure SQL DB Elastic Database nástroje skriptů](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Nástroje jsou také na Githubu: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Použijte nástroj split-merge pro přesun dat do nebo z modelu s více tenanty do modelu jednoho tenanta. Zobrazit [dělení a slučování](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Další materiály
Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce
Dotazy, můžete využít [fórum SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) a pro žádosti o funkce, přidejte je do [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

