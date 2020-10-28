---
title: Horizontální navýšení kapacity databáze
description: Jak používat knihovnu klienta elastické databáze ShardMapManager
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: e23b94c850c6ec326c2f4ad034e1fefc158087a5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793445"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Horizontální navýšení kapacity databází pomocí Správce map horizontálních oddílů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

K snadnému horizontálnímu navýšení kapacity databází v Azure SQL Database použijte Správce map horizontálních oddílů. Správce map horizontálních oddílů je speciální databáze, která uchovává globální informace o mapování všech horizontálních oddílů (databází) ve horizontálních oddílů sadě. Metadata umožňují aplikaci připojit se ke správné databázi na základě hodnoty **klíče horizontálního dělení** . Kromě toho všechny horizontálních oddílů v sadě obsahují mapy, které sledují místní data horizontálních oddílů (označovaná jako **shardlety** ).

![Správa mapování horizontálních oddílů](./media/elastic-scale-shard-map-management/glossary.png)

Porozumění způsobu, jakým jsou tato mapování vytvořená, je zásadní pro správu mapy horizontálních oddílů. K tomu je potřeba použít třídu ShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), která se nachází v [knihovně klienta elastic Database](elastic-database-client-library.md) pro správu map horizontálních oddílů.  

## <a name="shard-maps-and-shard-mappings"></a>Mapování horizontálních oddílů a mapování horizontálních oddílů

Pro každý horizontálních oddílů je nutné vybrat typ mapy horizontálních oddílů, kterou chcete vytvořit. Volba závisí na architektuře databáze:

1. Jeden tenant na databázi  
2. Více tenantů na databázi (dva typy):
   1. Mapování seznamu
   2. Mapování rozsahu

V případě modelu s jedním nájemcem vytvořte **mapování horizontálních oddílů mapování seznamu** . Model jednoho tenanta přiřadí jednu databázi na každého tenanta. Toto je efektivní model pro vývojáře v SaaS, který zjednodušuje správu.

![Mapování seznamu][1]

Model víceklientské aplikace přiřadí několik tenantů jednotlivým databázím (a můžete distribuovat skupiny klientů do více databází). Tento model použijte, pokud očekáváte, že každý tenant bude mít malé datové potřeby. V tomto modelu přiřaďte k databázi rozsah klientů pomocí **mapování rozsahu** .

![Mapování rozsahu][2]

Nebo můžete implementovat model víceklientské databáze pomocí *mapování seznamu* , aby bylo možné přiřadit více tenantů k individuální databázi. Například DB1 se používá k ukládání informací o klientech s ID 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10.

![Více tenantů v jedné databázi][3]

### <a name="supported-types-for-sharding-keys"></a>Podporované typy pro horizontálního dělení klíče

Elastické škálování podporuje následující typy jako horizontálního dělení klíče:

| .NET | Java |
| --- | --- |
| integer |integer |
| long |long |
| guid |uuid |
| Byte []  |Byte [] |
| datetime | časové razítko |
| timespan | doba trvání|
| DateTimeOffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Seznam a rozsah horizontálních oddílů Maps

Mapy horizontálních oddílů lze vytvořit pomocí **seznamů jednotlivých hodnot klíčů horizontálního dělení** nebo je lze vytvořit pomocí **rozsahů hodnot klíčů horizontálního dělení** .

### <a name="list-shard-maps"></a>Zobrazit mapy horizontálních oddílů

**Horizontálních oddílů** obsahují **shardlety** a mapování shardlety na horizontálních oddílů se udržuje pomocí mapy horizontálních oddílů. **Mapa horizontálních oddílů seznamu** je přidružení mezi hodnotami jednotlivých klíčů, které identifikují shardlety a databázemi, které slouží jako horizontálních oddílů.  **Mapování seznamu** jsou explicitní a jiné hodnoty klíče mohou být namapovány na stejnou databázi. Například hodnota klíče 1 mapuje na databázi a a hodnoty klíčů 3 a 6 se mapují na databázi B.

| Klíč | Umístění horizontálních oddílů |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Horizontálních oddílů mapy rozsahu

V **mapě horizontálních oddílů rozsahu** se rozsah klíčů popisuje pomocí páru **[nízká hodnota, vysoká hodnota)** , kde *Nízká hodnota* je minimální klíč v rozsahu a *Vysoká hodnota* je první hodnota vyšší než rozsah.

Například **[0, 100)** zahrnuje všechna celá čísla větší než nebo rovna 0 a menší než 100. Všimněte si, že více rozsahů může ukazovat na stejnou databázi a jsou podporovány nesouvislé rozsahy (například [100 200) a [400 600) obě odkazují na Database C v následujícím příkladu.)

| Klíč | Umístění horizontálních oddílů |
| --- | --- |
| [1, 50) |Database_A |
| [50 100) |Database_B |
| [100 200) |Database_C |
| [400 600) |Database_C |
| ... |... |

Každá z výše uvedených tabulek je koncepční příklad objektu **ShardMap** . Každý řádek je zjednodušeným příkladem jednotlivých **PointMapping** (pro mapu seznamu horizontálních oddílů) nebo **RangeMapping** (pro objekt Range horizontálních oddílů map).

## <a name="shard-map-manager"></a>Správce mapování horizontálních oddílů

V klientské knihovně je správce map horizontálních oddílů kolekcí map horizontálních oddílů. Data spravovaná instancí **ShardMapManager** se uchovávají na třech místech:

1. **Globální mapa horizontálních oddílů (GSM)** : určíte databázi, která bude sloužit jako úložiště pro všechna mapování horizontálních oddílů a mapování. Speciální tabulky a uložené procedury jsou automaticky vytvořeny pro správu informací. Většinou je to malá databáze a lehce se k němu používá a neměla by se používat pro jiné potřeby aplikace. Tabulky jsou ve speciálním schématu s názvem **__ShardManagement** .
2. **Místní Mapa horizontálních oddílů (LSM)** : Každá databáze, kterou určíte jako horizontálních oddílů, se upraví tak, aby obsahovala několik malých tabulek, a speciální uložené procedury, které obsahují a spravují informace o mapě horizontálních oddílů, které jsou specifické pro daný horizontálních oddílů. Tyto informace jsou redundantní s informacemi v systému GSM a umožňují aplikaci ověřovat horizontálních oddílů informace map uložených v mezipaměti bez nutnosti jakéhokoli zatížení pro GSM. aplikace používá LSM k určení, zda je mapování v mezipaměti stále platné. Tabulky, které odpovídají LSM na jednotlivých horizontálních oddílů, jsou také ve schématu **__ShardManagement** .
3. **Mezipaměť aplikace** : každá instance aplikace přistupující k objektu **ShardMapManager** udržuje místní mezipaměť v paměti svých mapování. Ukládá informace o směrování, které byly nedávno načteny.

## <a name="constructing-a-shardmapmanager"></a>Sestavování ShardMapManager

Objekt **ShardMapManager** je vytvořen pomocí vzoru factory ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). Metoda **ShardMapManagerFactory. GetSqlShardMapManager** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) přebírá přihlašovací údaje (včetně názvu serveru a názvu databáze, který uchovává software GSM) ve formě **ConnectionString** a vrací instanci **ShardMapManager** .  

**Všimněte si prosím:** **ShardMapManager** by mělo být vytvořena instance pouze jednou pro každou doménu aplikace v rámci inicializačního kódu aplikace. Vytvoření dalších instancí ShardMapManager ve stejné doméně aplikace má za následek větší využití paměti a procesoru v aplikaci. **ShardMapManager** může obsahovat libovolný počet map horizontálních oddílů. I když může být jedna mapa horizontálních oddílů dostačující pro mnoho aplikací, existují situace, kdy se různé sady databází používají pro různé schéma nebo pro jedinečné účely. v takovém případě může být vhodnější více map horizontálních oddílů.

V tomto kódu se aplikace pokusí otevřít existující **ShardMapManager** s TryGetSqlShardMapManager (metodou [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) . Pokud objekty představující globální **ShardMapManager** (GSM) ještě v databázi neexistují, klientské knihovny je vytvoří pomocí metody CreateSqlShardMapManager ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

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

Pro verzi rozhraní .NET můžete použít PowerShell k vytvoření nového správce map horizontálních oddílů. Příklad je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Získat RangeShardMap nebo ListShardMap

Po vytvoření správce mapy horizontálních oddílů můžete získat RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) nebo ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap) [, .NET) pomocí](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)metody TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) nebo GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)).

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

### <a name="shard-map-administration-credentials"></a>Přihlašovací údaje pro správu mapy horizontálních oddílů

Aplikace, které spravují mapy horizontálních oddílů a manipulují s nimi, se liší od těch, které používají mapy horizontálních oddílů ke směrování připojení.

Chcete-li spravovat mapy horizontálních oddílů (Přidat nebo změnit horizontálních oddílů, mapování horizontálních oddílů, mapování horizontálních oddílů atd.), je nutné vytvořit instanci **ShardMapManager** pomocí **pověření, která mají oprávnění ke čtení a zápisu v databázi GSM i v každé databázi, která slouží jako horizontálních oddílů** . Pověření musí umožňovat zápisy do tabulek v systémech GSM a LSM, jak jsou zadány nebo změněny informace o mapě horizontálních oddílů, a také pro vytváření tabulek LSM pro nové horizontálních oddílů.  

Viz [pověření používaná pro přístup k klientské knihovně elastic Database](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Pouze metadata ovlivněna

Metody použité pro naplnění nebo změnu dat **ShardMapManager** nezmění data uživatelů uložená v horizontálních oddílů. Například metody jako **CreateShard** , **DeleteShard** , **UpdateMapping** atd. mají vliv pouze na metadata mapy horizontálních oddílů. Neodstraňují, přidávají ani nemění uživatelská data obsažená v horizontálních oddílů. Místo toho jsou tyto metody navrženy pro použití ve spojení s oddělenými operacemi, které provádíte při vytváření nebo odebírání skutečných databází nebo které přesouváte řádky z jednoho horizontálních oddílů do druhé k opětovnému vyvážení prostředí horizontálně dělené.  (Nástroj pro **dělení na sloučení** , který je součástí nástrojů elastické databáze, využívá tato rozhraní API spolu s orchestrací skutečného přesunu dat mezi horizontálních oddílů.) Viz část [škálování pomocí nástroje elastic Database pro dělení a slučování](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Směrování závislé na datech

Správce map horizontálních oddílů se používá v aplikacích, které vyžadují databázová připojení k provádění datových operací specifických pro aplikaci. Tato připojení musí být přidružená ke správné databázi. Tento postup se označuje jako **Směrování závislé na datech** . Pro tyto aplikace vytvořte instanci objektu Správce map horizontálních oddílů z továrny pomocí přihlašovacích údajů, které mají v databázi GSM přístup jen pro čtení. Jednotlivé požadavky na pozdější připojení poskytují přihlašovací údaje potřebné pro připojení k příslušné databázi horizontálních oddílů.

Všimněte si, že tyto aplikace (pomocí **ShardMapManager** otevřených s přihlašovacími údaji jen pro čtení) nemůžou dělat změny v mapách nebo mapováních. Pro tyto potřeby vytvořte aplikace specifické pro správu nebo skripty PowerShellu, které poskytují vyšší privilegované přihlašovací údaje, jak je popsáno výše. Viz [pověření používaná pro přístup k klientské knihovně elastic Database](elastic-scale-manage-credentials.md).

Další informace najdete v tématu [Směrování závislé na datech](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Úprava mapy horizontálních oddílů

Mapu horizontálních oddílů lze změnit různými způsoby. Všechny následující metody upravují metadata popisující horizontálních oddílů a jejich mapování, ale nemění fyzicky data v rámci horizontálních oddílů, ani nevytvářejí ani neodstraňují skutečné databáze.  Některé operace na mapě horizontálních oddílů popsané níže může být potřeba koordinovat s akcemi správy, které fyzicky přesouvá data nebo přidávají a odstraňují databáze obsluhující jako horizontálních oddílů.

Tyto metody společně fungují jako stavební bloky dostupné pro úpravu celkové distribuce dat ve vašem prostředí databáze horizontálně dělené.  

* Chcete-li přidat nebo odebrat horizontálních oddílů: použijte **CreateShard** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) a **DeleteShard** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), .NET) třídy shardmap [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap) [).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)
  
    Server a databáze představující cílový horizontálních oddílů musí pro provedení těchto operací již existovat. Tyto metody nemají žádný vliv na samotné databáze, a to pouze na metadata v mapě horizontálních oddílů.
* Chcete-li vytvořit nebo odebrat body nebo rozsahy, které jsou namapovány na horizontálních oddílů: použijte **CreateRangeMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) třídy RangeShardMapping ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) a **CreatePointMapping** [(Java, .NET](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping) [) třídy](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)ListShardMap [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap)(Java [, .NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1).
  
    Ke stejnému horizontálních oddílů může být mapováno mnoho různých bodů nebo rozsahů. Tyto metody ovlivňují pouze metadata – neovlivňují žádná data, která již mohou být přítomna v horizontálních oddílů. Pokud je potřeba data z databáze odebrat, aby byla konzistentní s operacemi **DeleteMapping** , provedete tyto operace samostatně, ale ve spojení s použitím těchto metod.  
* Chcete-li rozdělit stávající rozsahy na dvě nebo sloučit sousední rozsahy do jedné: použijte **SplitMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) a **MergeMappings** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Všimněte si, že operace rozdělení a sloučení **nemění horizontálních oddílů, na které jsou mapované hodnoty klíče** . Rozdělení rozdělí stávající rozsah na dvě části, ale obě jsou namapovány na stejný horizontálních oddílů. Sloučení funguje na dvou sousedních rozsazích, které jsou již namapovány na stejný horizontálních oddílů, jejich sloučení do jednoho rozsahu.  Pohyb bodů nebo rozsahů mezi horizontálních oddílů je potřeba koordinovat pomocí **UpdateMapping** ve spojení s skutečným pohybem dat.  Můžete použít službu **rozdělit/sloučit** , která je součástí nástrojů elastické databáze k koordinaci změn mapy horizontálních oddílů s přesunem dat, když je potřeba přesun.
* Chcete-li znovu namapovat (nebo přesunout) jednotlivé body nebo rozsahy na různé horizontálních oddílů: použijte **UpdateMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Vzhledem k tomu, že může být nutné přesunout data z jednoho horizontálních oddílů do druhé, aby bylo možné je v souladu s operacemi **UpdateMapping** , je třeba provést toto přesun odděleně, ale ve spojení s použitím těchto metod.

* Postup při mapování online a offline: k řízení online stavu mapování použijte **MarkMappingOffline** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) a **MarkMappingOnline** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).
  
    Určité operace mapování horizontálních oddílů jsou povolené jenom v případě, že mapování je ve stavu offline, včetně **UpdateMapping** a **DeleteMapping** . Pokud je mapování offline, požadavek závislý na datech na základě klíče obsaženého v tomto mapování vrátí chybu. Kromě toho, když je rozsah nejprve přepnut do režimu offline, všechna připojení k ovlivněným horizontálních oddílů se automaticky odeberou, aby se předešlo nekonzistenci nebo neúplným výsledkům dotazů směrovaných na změněné rozsahy.

Mapování jsou v rozhraní .NET neměnné objekty.  Všechny metody výše popsané v mapování změn také neověřují všechny odkazy na ně ve vašem kódu. Aby bylo snazší provádět sekvence operací, které mění stav mapování, všechny metody, které mění mapování, vrátí nový odkaz mapování, takže operace mohou být zřetězeny. Pokud například chcete odstranit existující mapování v shardmap SM obsahující klíč 25, můžete spustit následující:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Přidání horizontálních oddílů

Aplikace často potřebují přidat novou horizontálních oddílů pro zpracování dat očekávaných z nových klíčů nebo rozsahů klíčů pro mapu horizontálních oddílů, která už existuje. Například aplikace horizontálně dělené podle ID tenanta může potřebovat zřídit nové horizontálních oddílů pro nového tenanta, jinak bude horizontálně dělené data měsíčně vyžadovat novou horizontálních oddílů zřízenou před začátkem každého nového měsíce.

Pokud nový rozsah hodnot klíče již není součástí stávajícího mapování a není nutné přesun dat, je jednoduché přidat nový horizontálních oddílů a přidružit nový klíč nebo rozsah k danému horizontálních oddílů. Podrobnosti o přidávání nových horizontálních oddílů najdete v tématu [Přidání nového horizontálních oddílů](elastic-scale-add-a-shard.md).

V případě scénářů, které vyžadují přesun dat, je však k orchestraci přesunu dat mezi horizontálních oddílů v kombinaci s potřebnými aktualizacemi map horizontálních oddílů potřeba Nástroj pro dělení na data. Podrobnosti o používání nástroje pro dělení a slučování najdete v tématu [Přehled dělení a slučování](elastic-scale-overview-split-and-merge.md) .

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png