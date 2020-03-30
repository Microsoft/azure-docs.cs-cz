---
title: Směrování závislé na datech
description: Jak používat třídu ShardMapManager v aplikacích .NET pro směrování závislé na datech, což je funkce s yharded databází v Azure SQL Database
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
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069451"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Použití směrování závislého na datech ke směrování dotazu do příslušné databáze

**Směrování závislé na datech** je možnost použít data v dotazu ke směrování požadavku do příslušné databáze. Směrování závislé na datech je základní vzor při práci s databázemi s oddíly. Kontext požadavku lze také použít ke směrování požadavku, zejména v případě, že klíč srážlivosti není součástí dotazu. Každý konkrétní dotaz nebo transakce v aplikaci pomocí směrování závislé na datech je omezena na přístup k jedné databázi na požadavek. Pro nástroje Azure SQL Database Elastic se toto směrování provádí pomocí třídy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)

Aplikace nemusí sledovat různé připojovací řetězce nebo umístění DB přidružené k různým řezům dat v prostředí s rozdělením. Místo toho [Správce mapy syhovacích diagramů](sql-database-elastic-scale-shard-map-management.md) otevře připojení ke správným databázím v případě potřeby na základě dat v mapě střepů a hodnoty klíče srážlivosti, který je cílem požadavku aplikace. Klíčem je obvykle *customer_id*, *tenant_id*, *date_key*nebo nějaký jiný specifický identifikátor, který je základním parametrem požadavku na databázi.

Další informace naleznete v [tématu Škálování serveru SQL Server pomocí směrování závislého na datech](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Stažení klientské knihovny

Stažení:

* Java verze knihovny, viz [Maven Centrální úložiště](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Verze knihovny .NET, naleznete v tématu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Použití shardmapmanageru v aplikaci směrování závislé na datech

Aplikace by měly vytvořit instanci **ShardMapManager** během inicializace pomocí továrního volání **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). V tomto příkladu jsou inicializovány **shardmapmanager** a konkrétní **shardmap,** který obsahuje. Tento příklad ukazuje metody GetSqlShardMapManager a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET).](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Použití nejnižších oprávnění pověření možné pro získání mapy střepu

Pokud aplikace není manipulace s mapou střepů sám, pověření použitá v metodě factory by měl mít oprávnění jen pro čtení v databázi **global shard map.** Tato pověření se obvykle liší od pověření používaných k otevření připojení ke správci mapy střepů. Viz také [pověření používaná pro přístup ke knihovně klienta elastické databáze](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Volání metody OpenConnectionForKey

**Metoda ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) vrátí připojení připravené k vydání příkazů do příslušné databáze na základě hodnoty **klíčového** parametru. Informace o úlovcích jsou v aplikaci ukládány **shardmapmanagerem**, takže tyto požadavky obvykle nezahrnují vyhledávání databáze proti databázi **Global Shard Map.**

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Klíčový** parametr se používá jako vyhledávací klíč do mapy střepu k určení příslušné databáze pro požadavek.
* **ConnectionString** slouží k předání pouze pověření uživatele pro požadované připojení. V tomto *řetězci connectionString* není zahrnut žádný název databáze ani název serveru, protože metoda určuje databázi a server pomocí **shardmap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) by měla být nastavena na **ConnectionOptions.Validate** pokud prostředí, kde se mohou měnit mapy oddílů a řádky se mohou přesunout do jiných databází v důsledku operací rozdělení nebo sloučení. Toto ověření zahrnuje stručný dotaz na místní mapu střepů v cílové databázi (nikoli na globální mapu střepů) před doručením připojení do aplikace.

Pokud se ověření na mapě místního oddílu nezdaří (což znamená, že mezipaměť je nesprávná), správce mapy síní dotazuje globální mapu střepu, aby získal novou správnou hodnotu pro vyhledávání, aktualizoval mezipaměť a získal a vrátil příslušné připojení k databázi .

Použití **ConnectionOptions.None** pouze v případě, že změny mapování šidů nejsou očekávány, když je aplikace online. V takovém případě lze předpokládat, že hodnoty uložené v mezipaměti jsou vždy správné a volání ověření dalších round-trip do cílové databáze lze bezpečně přeskočit. To snižuje databázový provoz. **ConnectionOptions** lze také nastavit prostřednictvím hodnoty v konfiguračním souboru k označení, zda jsou očekávané změny mezistřevku nebo ne během časového období.  

Tento příklad používá hodnotu celého klíče **CustomerID**pomocí objektu **ShardMap** s názvem **customerShardMap**.  

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

Metoda **OpenConnectionForKey** vrátí nové již otevřené připojení ke správné databázi. Připojení využité tímto způsobem stále plně využít sdružování připojení.

**Metoda OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) je také k dispozici, pokud vaše aplikace používá asynchronní programování.

## <a name="integrating-with-transient-fault-handling"></a>Integrace s přechodnou manipulací s chybami

Osvědčeným postupem při vývoji aplikací pro přístup k datům v cloudu je zajistit, aby aplikace zachytila přechodné chyby a že operace byly před vyvoláním chyby několikrát opakovány. Přechodná manipulace s chybami pro cloudové aplikace je popsána na adrese Tranient Fault Handling[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET).](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))

Přechodná manipulace s chybami může přirozeně koexistovat se vzorem Směrování závislé na datech. Klíčovým požadavkem je opakovat celý požadavek na přístup k datům včetně **bloku using,** který získal připojení směrování závislé na datech. Předchozí příklad může být přepsán následujícím způsobem.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Příklad - směrování závislé na datech s přechodným zpracováním chyb

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

Balíčky nezbytné k implementaci přechodné zpracování chyb jsou staženy automaticky při vytváření ukázkové aplikace elastické databáze.

## <a name="transactional-consistency"></a>Transakční konzistence

Transakční vlastnosti jsou zaručeny pro všechny operace místní na střep. Například transakce odeslané prostřednictvím směrování závislé na datech spustit v rámci rozsahu cílového oddílu pro připojení. V současné době neexistují žádné možnosti pro zařazení více připojení do transakce, a proto neexistují žádné transakční záruky pro operace prováděné napříč oddíly.

## <a name="next-steps"></a>Další kroky

Chcete-li odpojit úlomek nebo znovu připojit šiřidlo, přečtěte si informace [o použití třídy RecoveryManager k opravě problémů s mapou úlomků.](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
