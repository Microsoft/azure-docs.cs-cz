---
title: Horizontální navýšení kapacity Azure SQL database | Dokumentace Microsoftu
description: Jak používat ShardMapManager Klientská knihovna elastic database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/16/2018
ms.openlocfilehash: 71496a11deff5236161931d572e75d4a84b75c5f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162062"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Horizontální navýšení kapacity databáze pomocí Správce mapování horizontálních oddílů
Chcete-li snadné horizontální navýšení kapacity databáze na SQL Azure, použijte Správce mapování horizontálních oddílů. Správce mapování horizontálních oddílů je speciální databáze, která udržuje globální mapování informace o všech horizontálních oddílů (databáze) v nastavení horizontálními oddíly. Metadata umožňuje aplikaci připojovat ke správné databázi na základě hodnoty **klíč horizontálního dělení**. Kromě toho každý horizontální oddíl v sadě obsahuje mapování, které sledují místní horizontálně dělit data (označované jako **shardletů**). 

![Správa mapování horizontálních oddílů](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Vysvětlení, jak jsou vytvořeny tyto mapy je nezbytné pro správy mapování horizontálních oddílů. To se provádí pomocí třídy ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)byl nalezen v [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md) ke správě mapy horizontálních oddílů.  

## <a name="shard-maps-and-shard-mappings"></a>Mapy horizontálních oddílů a mapování horizontálních oddílů
Pro každý horizontální oddíl musíte vybrat typ mapy horizontálních oddílů k vytvoření. Výběr závisí na architektuře databáze: 

1. Jednoho tenanta na databázi  
2. Více tenantů na databázi (dva typy):
   1. Seznam mapování
   2. Mapování oblasti

Pro jednoho tenanta modelu, vytvořit **mapování seznamu** mapy horizontálních oddílů. Model jednoho tenanta přiřadí jednu databázi tenanta. To je účinným modelem pro vývojáře SaaS, protože zjednodušuje správu.

![Seznam mapování][1]

Víceklientského modelu přiřadí několika klienty izolovanou databázi (a skupin klientů můžete distribuovat napříč několika databázemi). Tento model použijte, pokud očekáváte, že každý tenant má malé dat, které potřebujete. V tomto modelu, přiřaďte rozsah tenantů k databázi pomocí **rozsah mapování**. 

![Mapování oblasti][2]

Nebo můžete implementovat pomocí modelu databázi s více tenanty *mapování seznamu* přiřadit více tenantů v jedné databázi. Například DB1 se používá k ukládání informací o tenantovi ID 1 a 5 a DB2 ukládá data pro tenanta 7 a tenanta 10. 

![Více tenantů v jedné databáze][3] 

### <a name="supported-types-for-sharding-keys"></a>Podporované typy pro klíče horizontálního dělení
Elastické škálování podporuje následující typy jako klíče horizontálního dělení:

| .NET | Java |
| --- | --- |
| integer |integer |
| zem. šířka |zem. šířka |
| identifikátor GUID |identifikátor UUID |
| Byte  |Byte |
| datetime | časové razítko |
| Časový interval | doba trvání|
| DateTimeOffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapy horizontálních oddílů seznamu a rozsahu
Mapy horizontálních oddílů lze sestavit pomocí **seznam jednotlivých horizontálního dělení hodnoty klíče**, nebo se dá vytvořit pomocí **rozsahy horizontálního dělení hodnoty klíče**. 

### <a name="list-shard-maps"></a>Mapy seznamů horizontálních oddílů
**Horizontální oddíly** obsahovat **shardletů** a mapování shardletů do horizontálních oddílů se spravuje pomocí mapy horizontálních oddílů. A **mapy seznamů horizontálních oddílů** je přidružení mezi jednotlivé hodnoty klíče, které identifikují shardlety a databází, které bude sloužit jako horizontální oddíly.  **Seznam mapování** explicitní a také různé klíčové hodnoty můžou být mapované na stejné databáze. Například hodnota klíče 1 mapuje na databázi A a hodnoty klíče 3 a 6 obou mapách databáze b.

| Klíč | Umístění horizontálních oddílů |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapy horizontálních oddílů rozsahu
V **mapy rozsahů horizontálních oddílů**, rozsahu klíčů je popsán pár **[nízkou hodnotu, vysoká hodnota)** kde *nízká hodnota* je minimální klíč v rozsahu a *vysoká Hodnota* první hodnota vyšší než rozsahu. 

Například **[0, 100)** zahrnuje všechny celá čísla větší než nebo rovna 0 a menší než 100. Všimněte si, že více oblastí může odkazovat na stejnou databázi, a jsou podporovány nesouvislý oblasti (například [100,200) a [400,600), jak odkazovat na C databáze v následujícím příkladu.)

| Klíč | Umístění horizontálních oddílů |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Každý z výše uvedené tabulky je koncepční příkladem **ShardMap** objektu. Každý řádek je zjednodušený příklad konkrétního **PointMapping** (pro mapy seznamů horizontálních oddílů) nebo **RangeMapping** (pro mapy rozsahů horizontálních oddílů) objektu.

## <a name="shard-map-manager"></a>Správce mapování horizontálních oddílů
V klientské knihovně správce mapování horizontálních oddílů je kolekce mapování horizontálních oddílů. Data spravovaná přes **ShardMapManager** instance je udržováno na třech místech: 

1. **Globální mapa horizontálních oddílů (GSM)**: Zadejte databázi, která bude sloužit jako úložiště pro všechny jeho mapy horizontálních oddílů a mapování. Ke správě informací se automaticky vytvoří zvláštní tabulkám a uloženým procedurám. To je obvykle s malou databází a lehce přistupovat, a se nesmí používat pro jiné potřebám vaší aplikace. Tabulky jsou v speciální schéma s názvem **__ShardManagement**. 
2. **Místní horizontálního dělení mapu (LSM)**: všechny databáze, který zadáte, bude do horizontálního oddílu je změněn, aby obsahoval několik malé tabulky a speciální uložené procedury, které obsahují a spravovat informace mapy horizontálních oddílů, které jsou specifické pro tohoto horizontálního oddílu. Tyto informace je redundantní pomocí informací v GSM a umožní aplikaci ověřit informace mapy horizontálních oddílů v mezipaměti bez uvedení vyžadovanou zátěž na GSM; aplikace používá LSM k určení, zda mapování uložené v mezipaměti je stále platný. Tabulky odpovídající LSM v jednotlivých horizontálních oddílech jsou také ve schématu **__ShardManagement**.
3. **Mezipaměť aplikace**: každý přístup instance aplikace **ShardMapManager** objektu udržuje místní mezipaměť v paměti její mapování. Ukládají se informace o směrování, která nedávno byla načtena. 

## <a name="constructing-a-shardmapmanager"></a>Vytváření ShardMapManager
A **ShardMapManager** objekt je vytvořen pomocí objekt pro vytváření ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) vzor. **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoda přijímá přihlašovací údaje (včetně názvu serveru a název databáze, která uchovává GSM) forma **ConnectionString** a vrátí instanci **ShardMapManager**.  

**Důležitá poznámka:** **ShardMapManager** by měl vytvořit pouze jednou v každé doméně aplikace v rámci inicializační kód pro aplikace. Vytvoření další instance ShardMapManager ve stejné doméně aplikace za následek vyšší využití paměti a procesoru aplikace. A **ShardMapManager** může obsahovat libovolný počet mapy horizontálních oddílů. Mapa jeden horizontální oddíl může být dostačující pro mnoho aplikací, existují situace, při použití jinou sadu databází pro jiné schéma nebo pro jedinečné účely; v těchto případech může být vhodnější několika map horizontálních oddílů. 

V tomto kódu se aplikace pokusí otevřít existující **ShardMapManager** s TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metody. Pokud objekty, které představují globální **ShardMapManager** (GSM) nepodporují, ale existují v databázi, vytvoří klientské knihovny, je pomocí CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) Metoda.

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it. 
ShardMapManager shardMapManager; 
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

if (shardMapManagerExists) 
{ 
    Console.WriteLine("Shard Map Manager already exists");
} 
else
{
    // Create the Shard Map Manager. 
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager"); 

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
} 
```

Pro verzi rozhraní .NET můžete použít PowerShell k vytvoření nového správce mapování horizontálních oddílů. Příkladem je k dispozici [tady](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Získat RangeShardMap nebo ListShardMap
Po vytvoření správce mapování horizontálního oddílu, můžete získat RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) nebo ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) pomocí TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), nebo GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metody.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
} 
```

### <a name="shard-map-administration-credentials"></a>Pověření správce mapování horizontálních oddílů
Aplikace, které spravují a manipulaci s horizontálními oddíly mapy se liší od těch, které používají mapy horizontálních oddílů do směrování připojení. 

Správa mapování horizontálních oddílů (Přidat nebo změnit horizontálními oddíly mapy horizontálních oddílů, mapování horizontálních oddílů, atd.) musíte vytvořit instanci **ShardMapManager** pomocí **čtení/zápis přihlašovací údaje, které mají oprávnění v databázi GSM a v každém databáze, která slouží jako horizontální oddíl**. Přihlašovací údaje musí umožnit zápisy na tabulky v GSM a LSM, jak zadat nebo změnit, stejně jako u vytváření tabulek LSM na nových horizontálních oddílů informace mapy horizontálních oddílů.  

Zobrazit [přihlašovací údaje pro přístup k Klientská knihovna Elastic Database](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Vliv pouze metadata.
Metody použité pro naplnění nebo změna **ShardMapManager** data nemění data uživatele uložená v horizontálních oddílech, sami. Například metody jako **CreateShard**, **DeleteShard**, **UpdateMapping**, ovlivňují jenom metadata mapy horizontálních oddílů. Nelze odebrat, přidat nebo změnit uživatelská data obsažená v horizontální oddíly. Místo toho tyto metody jsou navrženy pro použití ve spojení s samostatných operací, které můžete provést při vytvoření nebo odebrat skutečné databáze, nebo že přesunutí řádků z jeden horizontální oddíl do jiného k obnovení rovnováhy horizontálně dělené prostředí.  ( **Dělení a slučování** nástroje dodávaná s nástroji elastic database využívá tato rozhraní API spolu s Orchestrace přesunu skutečná data mezi horizontálními oddíly.) Zobrazit [škálování s využitím nástroje Elastic Database dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Směrování závislé na datech
Správce mapování horizontálních oddílů je použít v aplikacích, které vyžadují připojení k databázi k provádění operací data specifická pro aplikaci. Tato připojení musí být přidružen správné databázi. To se označuje jako **směrování závislé na datech**. Tyto aplikace vytvořte instanci objektu správce mapování horizontálních oddílů z objekt pro vytváření pomocí přihlašovacích údajů, které mají přístup jen pro čtení na databázi GSM. Jednotlivých požadavků pro pozdější připojení zadat přihlašovací údaje potřebné pro připojení k databázi odpovídajících horizontálních oddílů.

Všimněte si, že tyto aplikace (pomocí **ShardMapManager** otevřít pomocí přihlašovacích údajů jen pro čtení) nemůže provádět změny mapování nebo mapy. Pro tyto požadavky vytvořte pro správu konkrétní aplikace nebo skripty prostředí PowerShell, které poskytují vyšší úrovní oprávnění přihlašovací údaje, jak je uvedeno výše. Zobrazit [přihlašovací údaje pro přístup k Klientská knihovna Elastic Database](sql-database-elastic-scale-manage-credentials.md).

Další informace najdete v tématu [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Úprava mapy horizontálních oddílů
Mapy horizontálních oddílů můžete změnit různými způsoby. Všechny z následujících metod upravit metadata popisující horizontální oddíly a jejich mapování, ale fyzicky nemění data v rámci horizontální oddíly, ani proveďte jejich vytvoření nebo odstranění skutečné databáze.  Některé operace na mapy horizontálních oddílů je popsáno níže může třeba zajistí koordinaci se akce správy, který fyzicky přesouváte data nebo který přidáváním a odebíráním databází, které slouží jako horizontální oddíly.

Tyto metody společně fungovat jako stavební bloky, které jsou k dispozici pro úpravu celkovém rozdělení dat ve vašem prostředí horizontálně dělené databáze.  

* Přidání nebo odebrání horizontálních oddílů: použijte **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) a **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)) z shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) třídy. 
  
    Server a databázi představující horizontálních oddílů cíl už musí existovat pro tyto operace provést. Tyto metody nemají žádný vliv na databáze, sami, pouze na metadata do mapy horizontálních oddílů.
* K vytvoření nebo odebrání bodů nebo rozsahy, které jsou mapovány na horizontální oddíly: použijte **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)) z RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) třídy, a **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)) z ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) třídy.
  
    Mnoho různých bodů nebo rozsahy, lze mapovat na stejném horizontálním oddílu. Tyto metody ovlivní pouze metadata - neovlivňují všechna data, která může být již přítomny v horizontálních oddílech. Pokud data, musí se odebrat z databáze bylo v souladu s **DeleteMapping** operace, můžete tyto operace provádět samostatně, ale ve spojení s využitím těchto metod.  
* Rozdělení na dva existující oblasti nebo sousedních rozsazích sloučit do větve: použijte **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) a **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Všimněte si, že rozdělit a sloučit operace **neměňte horizontálních oddílů, ke které jsou mapovány hodnoty klíče**. Rozdělení přeruší stávající rozsahu do dvou částí, ale ponechá i jako mapované na stejném horizontálním oddílu. Sloučení pracuje ve dvou sousedních rozsazích, již namapované na stejném horizontálním oddílu, slučování do jedné oblasti.  Přesun body nebo rozsahy, sami mezi horizontálních oddílů musí být koordinované pomocí **UpdateMapping** ve spojení s vlastní přesun dat.  Můžete použít **dělení a slučování** službu, která je součástí nástrojů pro elastické databáze ke koordinaci změn provedených v mapování horizontálních oddílů s přesun dat v případě potřeby pohyb. 
* Přemapovat (nebo přesunutí) jednotlivé body rozsahy dat na různých horizontálních oddílech: použijte **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Protože data možná bude nutné přesunout z jednoho horizontálního oddílu do jiného-li být konzistentní s **UpdateMapping** operace, je třeba provést tento přesun samostatně, ale ve spojení s využitím těchto metod.
* Provést mapování online a offline: použijte **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) a **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) pro řízení online stavu mapování. 
  
    Určité operace mapování horizontálních oddílů jsou povoleny pouze při mapování je ve stavu "offline", včetně **UpdateMapping** a **DeleteMapping**. Při mapování je offline, závislé na datech žádosti na základě klíče součástí mapování vrátí chybu. Kromě toho při rozsah je nejprve do režimu offline, všechna připojení k ovlivněné horizontálního oddílu jsou automaticky ukončeny prevence nekonzistentní nebo neúplné výsledky dotazů namířená proti rozsahy mění. 

Mapování jsou neměnné objektů v rozhraní .net.  Všechny výše uvedené metody, které mění mapování také zneplatnit všechny odkazy na ně ve vašem kódu. Aby bylo snazší provádět pořadí operace, které změní stav mapování, všechny metody, které mění mapování vrátí odkaz na nové mapování, tak možné zřetězit operace. Například pokud chcete odstranit existující mapování v aplikaci sm shardmap, který obsahuje klíč 25, můžete spustit následující: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Přidání horizontálního oddílu
Aplikace často potřebují pro přidání nových horizontálních oddílů pro zpracování dat, která se očekává z nové klíče nebo klíče oblastí mapy horizontálních oddílů, který již existuje. Například horizontálně dělené aplikace podle ID Tenanta může být nutné poskytnout nový horizontální oddíl pro nového klienta, nebo měsíční horizontálně dělených dat může být nutné nový horizontální oddíl zřídit před zahájením nové měsíčně. 

Pokud nový rozsah hodnot klíčů již není součástí mapování stávající a bez přesouvání dat je nezbytné, je jednoduchý a přidejte nový horizontální oddíl přidružit nový klíč a rozsah na daném horizontálním oddílu. Podrobnosti o přidání nových horizontálních oddílů najdete v tématu [přidávání nových horizontálních oddílů](sql-database-elastic-scale-add-a-shard.md).

Pro scénáře, které vyžadují přesouvání dat ale nástroj split-merge je potřeba k orchestraci přesouvání dat mezi horizontálními oddíly v kombinaci s aktualizacemi mapy horizontálních oddílů nezbytné. Podrobnosti o použití dělení a slučování najdete v tématu [Přehled dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
