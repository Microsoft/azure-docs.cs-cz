---
title: "Škálování Azure SQL database | Microsoft Docs"
description: "Jak používat ShardMapManager, klientské knihovny pro elastické databáze"
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/28/2017
ms.author: sstein
ms.openlocfilehash: beddb3d9ac4a8c1ec5bd034c959c6b734c5b4403
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Horizontální navýšení kapacity databáze pomocí Správce horizontálního oddílu mapy
Chcete-li snadno škálovat databáze na SQL Azure, použijte správce mapy horizontálního oddílu. Mapa správce horizontálního oddílu je speciální databáze, která uchovává globální mapování informace o všech horizontálních oddílů (databáze) v sadě horizontálního oddílu. Metadata umožňuje aplikaci připojit ke správné databázi na základě hodnotu **horizontálního dělení klíč**. Kromě toho každých horizontálního oddílu v sadě obsahuje mapy, které sledují místní sdílení dat (označované jako **shardlets**). 

![Správa mapování horizontálních](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Pochopení, jak se vytvářejí tyto mapy je nezbytné pro správu mapy horizontálního oddílu. To se provádí pomocí třídy ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)), který se nachází v [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md) ke správě map horizontálního oddílu.  

## <a name="shard-maps-and-shard-mappings"></a>Mapování horizontálních a horizontálního oddílu mapy
Pro každý horizontálního oddílu je nutné vybrat typ mapy horizontálního oddílu k vytvoření. Výběr závisí na architektuře databáze: 

1. Jednoho klienta na databázi  
2. Několik klientů na databázi (dva typy):
   1. Seznam mapování
   2. Mapování rozsahu

Pro jednoho klienta modelu, vytvořit **seznamu mapování** horizontálního oddílu mapy. Model jednoho klienta přiřadí jednu databázi na každého klienta. Jedná se efektivní model pro vývojáře SaaS, protože ho zjednodušuje správu.

![Seznam mapování][1]

Víceklientského modelu přiřadí několik klientů jedné databáze (a skupin klientů, které můžete distribuovat mezi více databází). Pomocí tohoto modelu, pokud očekáváte, že každý klient k potřebami malá data. V tomto modelu, přiřaďte rozsah klientů k databázi pomocí **rozsah mapování**. 

![Mapování rozsahu][2]

Nebo můžete implementovat s použitím modelu víceklientské databáze *seznamu mapování* přiřadit více klientů pro jednu databázi. Například DB1 se používá k ukládání informací o klientovi ID 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10. 

![Několik klientů na jednom DB][3] 

### <a name="supported-types-for-sharding-keys"></a>Podporované typy pro klíče horizontálního dělení
Elastické škálování podporují následující typy jako horizontálního dělení klíče:

| .NET | Java |
| --- | --- |
| celé číslo |celé číslo |
| dlouhá |dlouhá |
| Identifikátor GUID |UUID |
| Byte  |Byte |
| datetime | časové razítko |
| Časový interval | Doba trvání|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Seznam a rozsah horizontálního oddílu mapy
Mapování horizontálních se dá vytvořit pomocí **seznam jednotlivých horizontálního dělení hodnoty klíče**, nebo se dá vytvořit pomocí **rozsahy horizontálního dělení hodnoty klíče**. 

### <a name="list-shard-maps"></a>Seznam horizontálních mapy
**Horizontálních oddílů** obsahovat **shardlets** a mapování shardlets k horizontálních oddílů je možný díky mapu horizontálního oddílu. A **seznam horizontálních mapy** je přidružení mezi jednotlivé hodnoty klíče, které identifikují shardlets a databázemi, které slouží jako horizontálních oddílů.  **Seznam mapování** jsou klíčem explicitní a jiné hodnoty lze mapovat do stejné databáze. Například klíč 1 se mapuje na databázi A a B. databáze odkazovat na klíčové hodnoty 3 a 6

| Klíč | Umístění horizontálního oddílu |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Rozsah horizontálního oddílu mapy
V **rozsah horizontálního oddílu mapy**, klíče rozsah je popsán pár **[hodnota nízká, vysoká hodnota)** kde *nízká hodnota* je minimální klíč v rozsahu a *vysoké hodnoty* první hodnota vyšší než je rozsah. 

Například **[0, 100)** zahrnuje všechny celá čísla větší než nebo rovna 0 a menší než 100. Všimněte si, že více rozsahům může ukazovat na stejnou databázi a nesouvislý rozsahy jsou podporovány (například [100,200) a [400,600) i bodu c databáze v následujícím příkladu.)

| Klíč | Umístění horizontálního oddílu |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Každá z tabulky uvedené výše je koncepční příklad **ShardMap** objektu. Každý řádek je zjednodušená příklad jednotlivce **PointMapping** (pro mapu horizontálního oddílu seznamu) nebo **RangeMapping** (pro mapu horizontálního oddílu rozsah) objektu.

## <a name="shard-map-manager"></a>Správce horizontálního oddílu mapy
V knihovně klienta správce mapy horizontálního oddílu je kolekce horizontálního oddílu map. Data spravovaná přes **ShardMapManager** instance je udržováno na třech místech: 

1. **Globální horizontálního oddílu mapy (GSM)**: Zadejte databázi, která bude sloužit jako úložiště pro všechna její mapování horizontálních a mapování. Speciální tabulek a uložených procedur se automaticky vytvoří ke správě informací. To je obvykle s malou databází a lehkým získat přístup, a nesmí být použita pro ostatní požadavky aplikace. V tabulkách jsou ve speciální schématu s názvem **__ShardManagement**. 
2. **Místní ID horizontálního oddílu mapy (LSM)**: každou databázi, která zadáte jako horizontálního oddílu se mění tak, aby obsahovala několik malých tabulky a speciální uložené procedury, které obsahují a spravovat informace o mapování horizontálních specifické pro tento horizontálního oddílu. Tyto informace jsou redundantní s informacemi v GSM a umožní aplikaci ověřit informace uložené v mezipaměti horizontálních mapy bez uvedení žádné zatížení na GSM; aplikace používá LSM k určení, pokud je v mezipaměti mapování stále platný. V tabulkách odpovídající LSM na každý horizontálního oddílu jsou i ve schématu **__ShardManagement**.
3. **Mezipaměť aplikací**: každý přístup instance aplikace **ShardMapManager** objekt udržuje místní mezipaměť v paměti její mapování. Uloží informace o směrování, která nedávno byla načtena. 

## <a name="constructing-a-shardmapmanager"></a>Vytváření ShardMapManager
A **ShardMapManager** objektu je vytvořený objekt pro vytváření ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) vzor. **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoda přebírá přihlašovací údaje (včetně názvu serveru a název databáze, která uchovává GSM) formu **ConnectionString** a vrací instanci třídy **ShardMapManager**.  

**Poznámka:** **ShardMapManager** by měla být vytvořena instance pouze jednou v každé doméně aplikace v rámci kód inicializace pro aplikaci. Vytvoření další instance ShardMapManager ve stejné doméně aplikace má za následek vyšší paměť a využití CPU aplikace. A **ShardMapManager** může obsahovat libovolný počet horizontálních mapy. Mapu jeden horizontálního oddílu může být dostatečné pro mnoho aplikací, jsou časy při použití různé sady databází pro jiné schéma nebo pro jedinečné účely; v těchto případech může být vhodnější než více mapami horizontálního oddílu. 

V tomto kódu aplikace pokusí otevřít existující **ShardMapManager** s TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)) metoda.  Pokud objekty, které představují globální **ShardMapManager** (GSM) není dosud neexistuje v databázi, klientské knihovny vytvoří je existuje pomocí CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [rozhraní .NET ](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metoda.

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

Pro verzi rozhraní .NET můžete použít PowerShell k vytvoření nového správce mapy horizontálního oddílu. Příkladem je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Získání RangeShardMap nebo ListShardMap
Po vytvoření horizontálního oddílu mapa správce, můžete získat RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) nebo ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) pomocí TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), nebo GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metoda.

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

### <a name="shard-map-administration-credentials"></a>Pověření správce horizontálního oddílu mapy
Aplikace, které Správa a zpracování mapy horizontálního oddílu se liší od těch, které pomocí map horizontálního oddílu k připojení trasy. 

Ke správě horizontálního oddílu mapy (Přidat nebo změnit horizontálních oddílů, map horizontálního oddílu, mapování horizontálních, atd.) je třeba vytvořit instanci **ShardMapManager** pomocí **přihlašovací údaje, které mají pro čtení a zápis oprávnění v databázi GSM a na každou databázi, která slouží jako horizontálního oddílu**. Přihlašovací údaje musí povolovat zápisu tabulky v GSM i LSM informace ID horizontálního oddílu mapy zadat nebo změnit, stejně jako u vytváření tabulek LSM na nové horizontálních oddílů.  

V tématu [používá přihlašovací údaje pro přístup k klientské knihovny pro elastické databáze](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Vliv pouze metadata.
Metody používané pro vyplnění nebo změna **ShardMapManager** data nijak nemění data uživatele uložená v horizontálních oddílů sami. Například metody, jako **CreateShard**, **DeleteShard**, **UpdateMapping**atd vliv pouze metadata mapy horizontálního oddílu. Není odebrat, přidat nebo změnit uživatele data obsažená v horizontálních oddílů. Místo toho tyto metody jsou určeny k použití ve spojení s samostatné operace, které provedete na vytvoření nebo odebrat skutečná databáze, nebo že přesunout řádky z jednoho horizontálního oddílu do jiného znovu vyvážit horizontálně dělené prostředí.  ( **Rozdělení sloučení** nástroje dodávaná s nástroje elastické databáze využívá tato rozhraní API společně s Orchestrace vlastní přesun dat. mezi horizontálních oddílů.) V tématu [škálování pomocí nástroje elastické databáze rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Směrování závislé na datech
Mapa správce horizontálního oddílu se používá v aplikacích, které vyžadují připojení databáze k provádění operací data specifická pro aplikaci. Tato připojení musí být přidruženy k databázi správné. To se označuje jako **závislé směrování dat**. Pro tyto aplikace vytvořte instanci objektu manager mapy horizontálního oddílu z objektu pro vytváření pomocí přihlašovacích údajů, které mají oprávnění jen pro čtení na databázi GSM. Jednotlivých požadavků pro pozdější připojení zadat přihlašovací údaje potřebné pro připojení k databázi odpovídající horizontálního oddílu.

Všimněte si, že tyto aplikace (pomocí **ShardMapManager** otevřít s přihlašovacími údaji jen pro čtení) nelze provádět změny pro mapování nebo mapy. Pro tyto potřeby vytvořte správu konkrétní aplikace nebo skripty prostředí PowerShell, které zadat přihlašovací údaje pro vyšší úrovní oprávnění, jak je popsáno výše. V tématu [používá přihlašovací údaje pro přístup k klientské knihovny pro elastické databáze](sql-database-elastic-scale-manage-credentials.md).

Další informace najdete v tématu [závislé směrování dat](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Úprava horizontálního oddílu mapy
Mapování horizontálních lze změnit různými způsoby. Všechny z následujících metod upravit metadat, která popisují horizontálních oddílů a jejich mapování, ale nemění fyzicky dat v rámci horizontálních oddílů, ani se jejich vytvoření nebo odstranění skutečné databáze.  Některé operace, na mapě horizontálního oddílu popsané dál může musí být zajistí koordinaci se akce správy, který fyzicky přesunout data nebo který přidávat a odebírat databáze slouží jako horizontálních oddílů.

Tyto metody spolupracovat jako stavební bloky, které jsou k dispozici pro úpravy celkové distribuci dat ve vašem prostředí horizontálně dělené databáze.  

* Přidat nebo odebrat horizontálních oddílů: použijte **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) a **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)) z Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) třída. 
  
    Server a databázi představující horizontálního oddílu cíl již musí existovat pro tyto operace provést. Tyto metody nemají žádný vliv na databáze, sami, jenom na metadata v mapě horizontálního oddílu.
* Vytvořit nebo odstranit body nebo rozsahy, které jsou namapované horizontálních oddílů: použijte **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)) z RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) třídy, a **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)) z ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) třída.
  
    Mnoho různých bodů nebo rozsahy lze mapovat na stejné ID horizontálního oddílu. Tyto metody ovlivňují jenom metadata – neovlivňují všechna data, která může být již přítomny v horizontálních oddílů. Pokud data musí být odebrána z databáze, aby byl konzistentní s **DeleteMapping** operace, proveďte tyto operace samostatně, ale ve spojení s těmito metodami.  
* Pro existující rozsahy rozdělit na dvě nebo sloučení sousedící oblasti do jedné: použijte **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) a **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Všimněte si, že rozdělení a operace sloučení **neměňte horizontálního oddílu, na kterou jsou hodnoty klíče namapovaná**. Rozdělení stávající rozsah dělí na dvě části, ale ponechá i jako namapované na stejné ID horizontálního oddílu. Sloučení funguje na dva přiléhající rozsahy, které jsou už namapované stejné ID horizontálního oddílu, je slučování do jednoho rozsahu.  Přesun body nebo rozsahy, sami mezi horizontálních oddílů musí být koordinované pomocí **UpdateMapping** ve spojení s vlastní přesun dat.  Můžete použít **rozdělení či sloučení** službu, která je součástí nástrojů pro elastické databáze pro koordinaci změny mapování horizontálních s přesun dat potřeby pohyb. 
* Znovu mapy (nebo přesunout) jednotlivé body nebo rozsahy pro různé horizontálních oddílů: použijte **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Vzhledem k tomu potřeba může data přesouvat z jednoho horizontálního oddílu na jiný s **UpdateMapping** operace, je třeba provést tento přesun samostatně, ale ve spojení s těmito metodami.
* Provést mapování online a offline: použijte **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) a **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) k řízení stavu online mapování. 
  
    Mapování horizontálních určité operace jsou povoleny pouze při mapování je ve stavu "offline", včetně **UpdateMapping** a **DeleteMapping**. Při mapování je offline, žádost dat závisí na základě klíče, součástí mapování vrátí chybu. Kromě toho když rozsah je nejprve do režimu offline, všechna připojení k ovlivněných horizontálního oddílu jsou automaticky ukončená prevence nekonzistentní nebo neúplné výsledky dotazů namířená proti rozsahy se změnil. 

Mapování jsou neměnné objekty v rozhraní .net.  Všechny výše uvedené metody, které změní mapování zneplatnit také všechny odkazy na ně ve vašem kódu. Usnadnění provádění pořadí operace, které změní stav mapování všechny metody, které změní mapování vrátí nový odkaz mapování, tak možné zřetězit operace. Například pokud chcete odstranit existující mapování v shardmap sm, který obsahuje klíč 25, můžete spustit následující: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Přidání horizontálního oddílu
Aplikace často potřebují přidat nové horizontálních oddílů pro zpracování dat, která je očekávána z nových klíčů nebo rozsahy klíčů pro mapu horizontálního oddílu, který již existuje. Například aplikace horizontálně dělené podle ID klienta muset zřídit nové horizontálního oddílu pro nového klienta, nebo horizontálně dělené měsíční dat může být nutné nové horizontálních zřídit před zahájením každý nový měsíc. 

Pokud nový rozsah hodnot klíče již není součástí stávající mapování a žádné přesun dat je nezbytné, je jednoduchý a přidat nové horizontálního oddílu přidružit nový klíč nebo rozsahu do této horizontálního oddílu. Podrobnosti o přidání nové horizontálních oddílů najdete v tématu [přidání nové horizontálního oddílu](sql-database-elastic-scale-add-a-shard.md).

Pro scénáře, které vyžadují přesun dat ale nástroj rozdělení sloučení je nezbytná k orchestraci přesun dat mezi horizontálních oddílů v kombinaci s aktualizací map nezbytné horizontálního oddílu. Podrobnosti o použití nástroje rozdělení sloučení najdete v tématu [Přehled rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
