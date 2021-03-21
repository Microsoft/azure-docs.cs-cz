---
title: Směrování závislé na datech
description: Použití třídy ShardMapManager v aplikacích .NET pro směrování závislé na datech, funkce databází horizontálně dělené v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92787019"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Směrování dotazu do příslušné databáze pomocí směrování závislého na datech
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Směrování závislé na datech** je schopnost použít data v dotazu ke směrování požadavku do příslušné databáze. Směrování závislé na datech je základní vzor při práci s databázemi horizontálně dělené. Kontext požadavku může být také použit ke směrování požadavku, zejména pokud klíč horizontálního dělení není součástí dotazu. Jednotlivé konkrétní dotazy nebo transakce v aplikaci využívající směrování závislé na datech mají omezený přístup k jedné databázi na žádost. Pro Azure SQL Database elastické nástroje se toto směrování dosahuje pomocí třídy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

Aplikace nemusí sledovat různé připojovací řetězce nebo umístění databáze přidružené k různým řezům dat v prostředí horizontálně dělené. Místo toho [správce mapy horizontálních oddílů](elastic-scale-shard-map-management.md) otevře připojení ke správným databázím v případě potřeby na základě dat v mapě horizontálních oddílů a hodnoty klíče horizontálního dělení, který je cílem žádosti aplikace. Klíčem je obvykle *Customer_ID*, *tenant_id*, *date_key* nebo nějaký jiný konkrétní identifikátor, který je základním parametrem požadavku databáze.

Další informace najdete v tématu horizontální navýšení kapacity [SQL Server se směrováním Data-Dependent](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)).

## <a name="download-the-client-library"></a>Stažení klientské knihovny

Ke stažení:

* Verze Java knihovny, viz [centrální úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Verze rozhraní .NET knihovny, viz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Použití ShardMapManager v aplikaci směrování závislé na datech

Aplikace by měly vytvořit instanci **ShardMapManager** během inicializace pomocí volání metody Factory **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). V tomto příkladu jsou inicializovány obě **ShardMapManager** a konkrétní **ShardMap** , které obsahuje. Tento příklad ukazuje metody GetSqlShardMapManager a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Použijte k získání mapy horizontálních oddílů nejnižší možné přihlašovací údaje oprávnění.

Pokud aplikace nepracuje na samotné mapě horizontálních oddílů, musí přihlašovací údaje použité v metodě Factory mít oprávnění jen pro čtení pro **globální databázi map horizontálních oddílů** . Tyto přihlašovací údaje se obvykle liší od přihlašovacích údajů používaných k otevření připojení ke Správci map horizontálních oddílů. Viz také [pověření používaná pro přístup k klientské knihovně elastic Database](elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Volání metody OpenConnectionForKey

**Metoda ShardMap. OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) vrátí připojení připravené k vystavování příkazů příslušné databázi na základě hodnoty **klíčového** parametru. Informace horizontálních oddílů jsou v aplikaci uloženy v mezipaměti **ShardMapManager**, takže tyto požadavky obvykle nezahrnují vyhledávání databáze na **globální databázi horizontálních oddílů map** .

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* Parametr **Key** se používá jako vyhledávací klíč k mapě horizontálních oddílů k určení vhodné databáze pro danou žádost.
* **Připojovací řetězec** se používá k předání pouze přihlašovacích údajů uživatele pro požadované připojení. V tomto *připojovacím řetězci* není obsažen název databáze ani název serveru, protože metoda určuje databázi a Server pomocí **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) by měla být nastavená na **ConnectionOptions. Validate** , pokud se může změnit prostředí, kde se mapy horizontálních oddílů mohou měnit a řádky se můžou v důsledku operací rozdělení nebo sloučení přesunout do jiných databází. Toto ověření zahrnuje stručný dotaz na místní mapu horizontálních oddílů v cílové databázi (ne na globální mapu horizontálních oddílů), než se připojení doručí do aplikace.

Pokud ověření proti místní mapě horizontálních oddílů neproběhne úspěšně (což znamená, že mezipaměť není správná), správce map horizontálních oddílů se dotazuje na globální horizontálních oddílů mapu, aby získal novou správnou hodnotu pro vyhledávání, aktualizovala mezipaměť a získala a vrátila příslušné databázové připojení.

Použijte **ConnectionOptions. None** pouze v případě, že nejsou očekávány změny mapování horizontálních oddílů v době, kdy je aplikace online. V takovém případě je možné hodnoty uložené v mezipaměti předpokládat, že budou vždy správné a že je možné přeskočit další ověřovací volání přenosové cesty do cílové databáze. Což snižuje provoz databáze. **ConnectionOptions** může být také nastaveno prostřednictvím hodnoty v konfiguračním souboru, aby označovala, zda jsou v časovém intervalu očekávány změny horizontálního dělení nebo ne.  

V tomto příkladu je použita hodnota celočíselného klíče **CustomerID** s použitím objektu **ShardMap** s názvem **customerShardMap**.  

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

Metoda **OpenConnectionForKey** vrací nové již otevřené připojení ke správné databázi. Využívané připojení tímto způsobem stále využívají sdružování připojení.

**Metoda OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) je k dispozici také v případě, že vaše aplikace využívá asynchronní programování.

## <a name="integrating-with-transient-fault-handling"></a>Integrace s dočasným zpracováním chyb

Osvědčeným postupem při vývoji aplikací pro přístup k datům v cloudu je zajistit, aby byla aplikace zachycena přechodným chybám a aby se operace opakovala několikrát před vyvoláním chyby. Přechodný zpracování chyb pro cloudové aplikace je popsáno v tématu zpracování přechodného selhání ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Zpracování přechodných chyb může být přirozeně v rámci Data-Dependentho vzoru směrování. Klíčovým požadavkem je opakovat celou žádost o přístup k datům, včetně bloku **using** , který získal připojení směrování závislé na datech. Předchozí příklad může být přepsán následujícím způsobem.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Příklad – směrování závislé na datech s dočasným zpracováním chyb

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

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

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

Balíčky, které jsou nezbytné k implementaci přechodného zpracování chyb, se automaticky stáhnou při vytváření ukázkové aplikace elastické databáze.

## <a name="transactional-consistency"></a>Transakční konzistence

Transakční vlastnosti jsou zaručené pro všechny operace, které jsou místní pro horizontálních oddílů. Například transakce odeslané prostřednictvím směrování závislého na datech se provádějí v rámci rozsahu cílového horizontálních oddílů pro připojení. V tuto chvíli nejsou k dispozici žádné možnosti pro zařazení více připojení do transakce, a proto neexistují žádné transakční záruky pro operace prováděné napříč horizontálních oddílů.

## <a name="next-steps"></a>Další kroky

Chcete-li odpojit horizontálních oddílů nebo znovu připojit horizontálních oddílů, přečtěte si téma [použití třídy RecoveryManager pro řešení problémů s mapováním horizontálních oddílů](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]