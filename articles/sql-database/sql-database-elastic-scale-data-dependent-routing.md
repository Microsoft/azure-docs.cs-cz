---
title: Data závislé směrování s Azure SQL Database | Microsoft Docs
description: Jak používat třídu ShardMapManager v aplikacích .NET pro data závislé na směrování, funkce horizontálně dělené databází ve službě Azure SQL Database
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 8a9b0b2c0541504954c4a1352180552cc650a31f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="data-dependent-routing"></a>Směrování závislé na datech
**Data závislé směrování** je možnost používat data v dotazu pro směrování požadavku k příslušné databázi. Toto je základní vzor při práci s horizontálně dělené databáze. Kontext požadavku může také použít pro směrování požadavku, zvlášť pokud klíč horizontálního dělení není část dotazu. Každé specifického dotazu nebo transakce v aplikaci pomocí závislé směrování dat je omezen na přístup k jedné databáze na základě požadavku. Pro nástroje Azure SQL Database elastické tento směrování se provádí s **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) třída.

Aplikace není nutné sledovat různé připojovací řetězce nebo DB umístění přidružené k jiné řezy dat v horizontálně dělené prostředí. Místo toho [správce mapy horizontálního oddílu](sql-database-elastic-scale-shard-map-management.md) otevře připojení k databázím správné v případě potřeby na základě dat v mapě horizontálního oddílu a hodnota klíče horizontálního dělení, který je cílem dané aplikace požadavek. Klíč je obvykle *customer_id*, *tenant_id*, *date_key*, nebo některé konkrétní identifikátor, který je základní parametr žádosti databáze. 

Další informace najdete v tématu [škálování SQL serveru se Škálováním s závislé směrování dat](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Stáhnout klientské knihovny
Stažení:
* Java verzi knihovny, najdete v části [Maven centrálním úložišti](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* verze rozhraní .NET knihovny, najdete v části [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Použití ShardMapManager v závislou aplikaci dat směrování
Aplikace by měl vytvořit instanci **ShardMapManager** při inicializaci pomocí volání objektu pro vytváření **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [rozhraní .NET ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)). V tomto příkladu jak **ShardMapManager** a konkrétní **ShardMap** ji obsahující jsou inicializovány. Tento příklad ukazuje GetSqlShardMapManager a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx)) metody.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Použít pověření nejnižší oprávnění možné pro získání ID horizontálního oddílu mapy
Pokud aplikace není manipulace s samotná mapa horizontálního oddílu, přihlašovací údaje použité metoda objektu factory musí mít oprávnění stačí jen pro čtení **globální horizontálního oddílu mapy** databáze. Tyto přihlašovací údaje se obvykle liší od přihlašovací údaje používané k otevření připojení do správce mapy horizontálního oddílu. Viz také [používá přihlašovací údaje pro přístup k klientské knihovny pro elastické databáze](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Volání metody OpenConnectionForKey
**ShardMap.OpenConnectionForKey metoda** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) vrátí připojení připravená pro vydání příkazy k příslušné databázi na základě hodnoty z **klíč** parametr. Informace ID horizontálního oddílu je uložené v mezipaměti v aplikace **ShardMapManager**, takže tyto požadavky nejsou obvykle zahrnují vyhledávání v databázi proti **globální mapy horizontálního oddílu** databáze. 

```Java
// Syntax: 
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```
* **Klíč** parametr se používá jako klíč vyhledávání do mapy horizontálního oddílu určit příslušné databáze pro požadavek. 
* **ConnectionString** slouží k předávání pouze uživatelská pověření pro požadované připojení. Žádný název databáze nebo název serveru je zahrnuta v tomto *connectionString* vzhledem k tomu, že metoda určuje databáze a serveru pomocí **ShardMap**. 
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)) musí být nastavena na **ConnectionOptions.Validate** Pokud prostředí, kde horizontálního oddílu mapuje může Změna a řádky může přesunout do jiné databáze v důsledku rozdělení nebo sloučení operací. To zahrnuje stručný dotazu mapu místní horizontálního oddílu na cílové databáze (ne do globální horizontálních map) před doručením připojení k aplikaci. 

Selže-li ověření vůči mapování horizontálních místní (což znamená, že mezipaměť je nesprávný), správce mapy horizontálního oddílu dotazuje mapy globální horizontálních získat nové správnou hodnotu pro vyhledávání, aktualizace mezipaměti a získat a vrátit připojení k příslušné databázi . 

Použití **ConnectionOptions.None** pouze když změny mapování horizontálních nejsou očekáván aplikace je online. V takovém případě uložené v mezipaměti hodnoty lze předpokládat, že vždycky být správná a mohou být velmi odezvy ověření volání na cílovou databázi bezpečně přeskočeny. Který snižuje zatížení databáze. **ConnectionOptions** také lze nastavit prostřednictvím hodnotu v konfiguračním souboru k označení, zda se očekává horizontálního dělení změny nebo není během v časovém intervalu.  

Tento příklad používá hodnotu celé číslo klíče **CustomerID**pomocí **ShardMap** objekt s názvem **customerShardMap**.  

```Java 
int customerId = 12345; 
int productId = 4321; 
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

// Connect to the shard for that customer ID. No need to call a SqlConnection 
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
{ 
    // Execute a simple command. 
    SqlCommand cmd = conn.CreateCommand(); 
    cmd.CommandText = @"UPDATE Sales.Customer 
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID"; 

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
    cmd.ExecuteNonQuery(); 
}  
```

**OpenConnectionForKey** metoda vrátí nové již otevřete připojení ke správné databázi. Připojení v tímto způsobem nadále využívat všech výhod sdružování připojení. 

**OpenConnectionForKeyAsync metoda** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)) je také k dispozici, pokud vaše aplikace provede asynchronní programování pomocí.

## <a name="integrating-with-transient-fault-handling"></a>Integrace s přechodná chyba zpracování
Osvědčeným postupem při vývoji aplikací přístup dat v cloudu, je zajistit, že přechodných zachytila aplikaci a že operace, které jsou opakovat několikrát před vyvolání k chybě. Přechodná chyba zpracování pro cloudové aplikace je popsána v přechodných chyb ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx)). 

Přechodná chyba zpracování mohou existovat vedle sebe přirozeně pomocí vzoru Data závislé směrování. Klíčovým požadavkem je opakovat, včetně celého datového přístupu požadavek **pomocí** blok, který získat připojení směrování závislé na data. V předchozím příkladu by mohla být přepsána následujícím způsobem. 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Příklad: závislé směrování s přechodná chyba zpracování dat
```Java 
int customerId = 12345; 
int productId = 4321; 
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; 
{
    // Connect to the shard for a customer ID. 
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 

        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 

        Console.WriteLine("Update completed"); 
    } 
}); 
```

Balíčky, které jsou nezbytné k implementaci přechodná chyba zpracování se stáhnou automaticky při sestavování ukázkovou aplikaci elastické databáze. 

## <a name="transactional-consistency"></a>Transakční konzistence
Transakční vlastnosti jsou zaručena bezpečnost pro všechny operace místní horizontálního oddílu. Například spuštění transakce odeslat prostřednictvím závislé na data směrování v rámci oboru horizontálního oddílu cíl pro připojení. V současné době neexistují žádné možnosti zadaná pro zapsání několik připojení do transakce a proto nejsou žádné záruky transakcí pro operace prováděné napříč horizontálních oddílů.

## <a name="next-steps"></a>Další postup
Odpojit horizontálního oddílu, nebo znovu připojit horizontálního oddílu, najdete v části [pomocí třídy RecoveryManager opravit problémy horizontálního oddílu mapy](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

