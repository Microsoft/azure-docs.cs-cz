---
title: Horizontální navýšení kapacity databáze
description: Jak používat shardMapManager, elastické databáze klienta knihovny
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256260"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Horizontální navýšení kapacity databází pomocí správce map horizontálních oddílů

Chcete-li snadno škálovat databáze v SQL Azure, použijte správce map horizontálního oddílu. Správce mapy síchu je speciální databáze, která udržuje globální mapování informace o všech oddílů (databází) v sadě šiřitek. Metadata umožňuje aplikaci připojit se ke správné databázi na základě hodnoty **klíče srážlivosti**. Kromě toho každý úlomek v sadě obsahuje mapy, které sledují místní data střepů (označované jako **shardlety).**

![Správa mapování horizontálních oddílů](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Pochopení, jak jsou tyto mapy konstruovány, je nezbytné pro správu mapy sršně. To se provádí pomocí třídy ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), která se nachází v [klientské knihovně elastická databáze](sql-database-elastic-database-client-library.md) pro správu map střepů.  

## <a name="shard-maps-and-shard-mappings"></a>Mapy úložných oddílů a mapování úlomků

Pro každý úlomek je nutné vybrat typ mapy šicího oddílu, který chcete vytvořit. Volba závisí na architektuře databáze:

1. Jeden klient na databázi  
2. Více klientů na databázi (dva typy):
   1. Mapování seznamu
   2. Mapování rozsahu

Pro model s jedním tenantem vytvořte mapu šiřidla **mapování seznamu.** Model s jedním tenantem přiřadí jednu databázi na klienta. Jedná se o efektivní model pro vývojáře SaaS, protože zjednodušuje správu.

![Mapování seznamu][1]

Víceklientský model přiřadí několik klientů k individuální databázi (a můžete distribuovat skupiny klientů napříč více databázemi). Tento model použijte, pokud očekáváte, že každý klient bude mít malé datové potřeby. V tomto modelu přiřaďte k databázi řadu klientů pomocí **mapování rozsahu**.

![Mapování rozsahu][2]

Nebo můžete implementovat model databáze s více klienty pomocí *mapování seznamu* přiřadit více klientů k jednotlivé databázi. Například DB1 se používá k ukládání informací o ID klienta 1 a 5 a DB2 ukládá data pro klienta 7 a klienta 10.

![Více klientů na jedné DB][3]

### <a name="supported-types-for-sharding-keys"></a>Podporované typy pro klíče pro řícení

Elastické měřítko podporuje následující typy jako klíče horizontálního horizontu:

| .NET | Java |
| --- | --- |
| celé číslo |celé číslo |
| long |long |
| Identifikátor guid |Uuid |
| bajt[]  |bajt[] |
| datetime | časové razítko |
| Timespan | doba trvání|
| Datetimeoffset |posundatetime |

### <a name="list-and-range-shard-maps"></a>Seznam a rozsah střep mapy

Mapy úlomků lze vytvořit pomocí **seznamů jednotlivých hodnot klíče s nástružnictvím**nebo mohou být vytvořeny pomocí **rozsahů hodnot klíče s návěstím**.

### <a name="list-shard-maps"></a>Seznam map úlomků

**Úlomky** obsahují **shardlety** a mapování shardlets na střepy je udržována mapy střepů. **Mapa úlomků seznamu** je přidružení mezi jednotlivými hodnotami klíče, které identifikují shardlety a databázemi, které slouží jako střepy.  **Seznam mapování** jsou explicitní a různé hodnoty klíče lze mapovat na stejnou databázi. Například hodnota klíče 1 se mapuje na databázi A a hodnoty klíče 3 a 6 obě mapují do databáze B.

| Klíč | Umístění úlomku |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapy úlomků rozsahu

V **mapě úlomku rozsahu**je rozsah klíčů popsán dvojicí **[Nízká hodnota, Vysoká hodnota),** kde *nízká hodnota* je minimální klíč v rozsahu a *vysoká hodnota* je první hodnota vyšší než rozsah.

Například **[0, 100)** zahrnuje všechna celá čísla větší než nebo rovna 0 a menší než 100. Všimněte si, že více rozsahů může překážet na stejnou databázi a jsou podporovány nesouvislé rozsahy (například [100 200) a [400 600) v následujícím příkladu.

| Klíč | Umístění úlomku |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Každá z výše uvedených tabulek je koncepčním příkladem objektu **ShardMap.** Každý řádek je zjednodušeným příkladem jednotlivého **pointmappingu** (pro mapu šiřidla seznamu) nebo **RangeMapping** (pro mapu šibenic rozsahu).

## <a name="shard-map-manager"></a>Správce mapování horizontálních oddílů

V klientské knihovně je správce mapy střepů kolekcí map střepů. Data spravovaná instancí **ShardMapManager** jsou uložena na třech místech:

1. **Globální mapa úlomků (GSM)**: Zadáte databázi, která bude sloužit jako úložiště pro všechny mapy a mapování úlomků. Pro správu informací jsou automaticky vytvořeny speciální tabulky a uložené procedury. Obvykle se jedná o malou databázi a snadno přistupovat a neměl by být používán pro jiné potřeby aplikace. Tabulky jsou ve speciálním schématu s názvem **__ShardManagement**.
2. **Místní mapa štřepů (LSM)**: Každá databáze, kterou zadáte jako střep, je upravena tak, aby obsahovala několik malých tabulek a speciální uložené procedury, které obsahují a spravují informace o mapování úlomku specifické pro daný oddíl. Tyto informace jsou redundantní s informacemi v GSM a umožňují aplikaci ověřit informace o mapách sírových bitů uložené v mezipaměti bez zatížení GSM; Aplikace používá LSM k určení, zda mapování uložené v mezipaměti je stále platný. Tabulky odpovídající LSM na každém úlomku jsou také ve schématu **__ShardManagement**.
3. **Mezipaměť aplikace**: Každá instance aplikace přistupující k objektu **ShardMapManager** udržuje místní mezipaměť mapování v paměti. Ukládá informace o směrování, které byly nedávno načteny.

## <a name="constructing-a-shardmapmanager"></a>Vytvoření shardmapmanageru

Objekt **ShardMapManager** je vytvořen pomocí vzoru factory ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) Metoda **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) přebírá pověření (včetně názvu serveru a názvu databáze, které drží GSM) ve formě **ConnectionString** a vrátí instanci **shardMapManager**.  

**Upozorňujeme:** **ShardMapManager** by měl být vytvořena instance pouze jednou na doménu aplikace, v rámci inicializačníkód pro aplikaci. Vytvoření dalších instancí ShardMapManager ve stejné doméně aplikace má za následek zvýšení paměti a využití procesoru aplikace. **ShardMapManager** může obsahovat libovolný počet map střepů. Zatímco jeden diagram mapy může být dostatečná pro mnoho aplikací, jsou časy, kdy různé sady databází se používají pro různé schéma nebo pro jedinečné účely; v těchto případech může být vhodnější více map úlomků.

V tomto kódu se aplikace pokusí otevřít existující **shardmapmanager** pomocí Metody TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Pokud objekty představující Global **ShardMapManager** (GSM) ještě neexistují uvnitř databáze, klientská knihovna je vytvoří pomocí metody CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)

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

Pro verzi rozhraní .NET můžete pomocí prostředí PowerShell vytvořit nový Správce map sít. Příklad je k dispozici [zde](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Získat RangeShardMap nebo ListShardMap

Po vytvoření správce mapy střepů můžete získat RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) nebo ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) pomocí TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), nebo GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metoda.

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

### <a name="shard-map-administration-credentials"></a>Pověření pro správu mapy skřípků

Aplikace, které spravují mapy úlomků a manipulují s nimi, se liší od aplikací, které používají mapy úlomků ke směrování připojení.

Chcete-li spravovat mapy střepů (přidat nebo změnit střepy, mapy střepů, mapování střepů atd.), musíte vytvořit instanci **ShardMapManager** pomocí **pověření, která mají oprávnění pro čtení a zápis v databázi GSM i v každé databázi, která slouží jako střep**. Pověření musí umožňovat zápisy do tabulek v GSM a LSM jako informace o mapě střepu je zadán nebo změněn, stejně jako pro vytváření tabulek LSM na nové úlomky.  

Viz [Pověření používaná pro přístup ke knihovně klienta elastické databáze](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Pouze ovlivněná metadata

Metody používané pro vyplnění nebo změnu dat **ShardMapManager** nemění uživatelská data uložená v samotných střepů. Například metody jako **CreateShard**, **DeleteShard**, **UpdateMapping**atd. Neodeberou, nepřidávají ani nemění uživatelská data obsažená ve šiřících dřících. Místo toho jsou tyto metody navrženy tak, aby byly použity ve spojení se samostatnými operacemi, které provádíte k vytvoření nebo odebrání skutečných databází, nebo které přesouvají řádky z jednoho oddílu do druhého, aby znovu vyvážily dělené prostředí.  (Nástroj **pro rozdělení sloučení,** který je součástí nástrojů pro elastické databáze, využívá tato řešení API spolu s orchestrací skutečného pohybu dat mezi úlomky.) Viz [Změna měřítka pomocí nástroje rozdělení sloučení elastické databáze](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Směrování závislé na datech

Správce mapy svižných míst se používá v aplikacích, které vyžadují připojení k databázi k provádění operací dat specifických pro aplikaci. Tato připojení musí být přidružena ke správné databázi. Tento postup se označuje jako **směrování závislé na datech**. Pro tyto aplikace vytvořte instanci objektu správce mapy svižného oddílu z továrny pomocí pověření, která mají přístup jen pro čtení v databázi GSM. Jednotlivé požadavky na pozdější připojení poskytují pověření potřebná pro připojení k příslušné databázi svižných oddílů.

Všimněte si, že tyto aplikace (pomocí **ShardMapManager** otevřel s pověření jen pro čtení) nelze provádět změny v mapách nebo mapování. Pro tyto potřeby vytvořte aplikace specifické pro správu nebo skripty prostředí PowerShell, které poskytují vyšší privilegovaná pověření, jak bylo popsáno dříve. Viz [Pověření používaná pro přístup ke knihovně klienta elastické databáze](sql-database-elastic-scale-manage-credentials.md).

Další informace naleznete v [tématu Směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Úprava mapy úlomků

Mapu úlomků lze změnit různými způsoby. Všechny následující metody upravit metadata popisující oddíly a jejich mapování, ale fyzicky nemění data v rámci oddíly, ani vytvořit nebo odstranit skutečné databáze.  Některé operace na mapě střepů popsané níže může být nutné koordinovat s akcemi správy, které fyzicky přesunout data nebo které přidávají a odeberou databáze sloužící jako úlomky.

Tyto metody spolupracují jako stavební bloky, které jsou k dispozici pro úpravu celkové distribuce dat v prostředí databáze s rozdělením oborů.  

* Přidání nebo odebrání úlomků: použijte **createshard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) a **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) třídy shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)
  
    Server a databáze představující cílový úlomek musí již existovat, aby tyto operace mohly být provedeny. Tyto metody nemají žádný vliv na samotné databáze, pouze na metadata v mapě oddílu.
* Chcete-li vytvořit nebo odebrat body nebo rozsahy, které jsou mapovány na střepy: použijte **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET),](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100)) **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) třídy RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) a **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) třídy ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)
  
    Mnoho různých bodů nebo rozsahů lze mapovat na stejný úlomek. Tyto metody ovlivňují pouze metadata - nemají vliv na žádná data, která již mohou být přítomny v úlomcích. Pokud data je třeba odebrat z databáze, aby byly konzistentní s **DeleteMapping** operace, provedete tyto operace samostatně, ale ve spojení s použitím těchto metod.  
* Chcete-li rozdělit existující rozsahy na dvě nebo sloučit sousední oblasti do jednoho: použijte **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) a **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET).](https://msdn.microsoft.com/library/azure/dn824201.aspx)  
  
    Všimněte si, že operace rozdělení a sloučení **nezmění úlomek, na který jsou mapovány hodnoty klíče**. Rozdělení rozdělí existující rozsah na dvě části, ale ponechá obě jako mapované na stejný úlomek. Sloučení pracuje na dvou sousedních oblastí, které jsou již mapovány na stejný úlomek, slučování je do jedné oblasti.  Pohyb bodů nebo rozsahy sami mezi úlomky musí být koordinovány pomocí **UpdateMapping** ve spojení s pohybem skutečné data.  Můžete použít **split/merge** služby, která je součástí elastické databázové nástroje koordinovat změny mapy sypů s pohybem dat, když je potřeba přesun.
* Chcete-li znovu mapovat (nebo přesunout) jednotlivé body nebo rozsahy na různé úlomky: použijte **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)  
  
    Vzhledem k tomu, že data může být nutné přesunout z jednoho úlomku do druhého, aby byly konzistentní s **UpdateMapping** operace, je třeba provést tento pohyb samostatně, ale ve spojení s použitím těchto metod.

* Chcete-li mapování převzít online a offline: k řízení online stavu mapování použijte **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) a **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)
  
    Některé operace na mapování oddílů jsou povoleny pouze v případě, že mapování je ve stavu "offline", včetně **UpdateMapping** a **DeleteMapping**. Pokud je mapování offline, požadavek závislý na datech založený na klíči zahrnutém v tomto mapování vrátí chybu. Kromě toho při prvním převedení oblasti do offline, všechna připojení k ovlivněné oddílu jsou automaticky ukončena, aby se zabránilo nekonzistentní nebo neúplné výsledky pro dotazy zaměřené proti rozsahy jsou změněny.

Mapování jsou neměnné objekty v .Net.  Všechny výše uvedené metody, které mění mapování také zneplatnit všechny odkazy na ně v kódu. Chcete-li usnadnit provádění posloupností operací, které mění stav mapování, všechny metody, které mění mapování, vrátí nový odkaz na mapování, takže operace mohou být zřetězeny. Chcete-li například odstranit existující mapování v shardmap sm, které obsahuje klíč 25, můžete provést následující:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Přidání šněrování

Aplikace často potřebují přidat nové oddíly pro zpracování dat, která se očekává od nových klíčů nebo rozsahů klíčů, pro mapu šicího dříče, která již existuje. Například aplikace, která je rozdělena podle ID klienta, může potřebovat zřídit nový oddíl pro nového klienta nebo data rozdělená měsíčně může potřebovat nový oddíl zřízeného před začátkem každého nového měsíce.

Pokud nový rozsah hodnot klíčů již není součástí existujícího mapování a není nutný žádný přesun dat, je snadné přidat nový oddíl a přidružit nový klíč nebo rozsah k tomuto oddílu. Podrobnosti o přidávání nových úlomků naleznete [v tématu Přidání nového štěřídla](sql-database-elastic-scale-add-a-shard.md).

Pro scénáře, které vyžadují přesun dat, je však nástroj rozdělení sloučení potřebné k orchestraci přesunu dat mezi oddíly v kombinaci s nezbytnými aktualizacemi mapy štrůdků. Podrobnosti o použití nástroje rozdělení sloučení naleznete v [tématu Přehled rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
