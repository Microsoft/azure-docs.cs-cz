---
title: Směrování závislé na datech pomocí Azure SQL Database | Dokumentace Microsoftu
description: Jak používat třídu ShardMapManager pro závislé na datech směrování, která je součástí horizontálně dělené databáze ve službě Azure SQL Database v aplikacích .NET
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
ms.date: 01/03/2019
ms.openlocfilehash: 2a862a6f1165b0cdd4dfe46e638dc6b10eae9ee5
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191322"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Použijte závislé na datech směrování směrovat dotaz k příslušné databázi

**Směrování závislé na datech** je možnost používat data v dotazu pro směrování požadavku k příslušné databázi. Směrování dat závislé je základní vzor, pokud pracujete s horizontálně dělené databáze. Kontext požadavku může také sloužit pro směrování požadavku, zejména v případě, že je klíč horizontálního dělení není součástí dotazu. Je omezena na jednu databázi na žádost o přístup k každé konkrétní dotaz nebo transakce v aplikaci pomocí směrování závislé na datech. Pro nástroje Azure SQL Database Elastic tento směrování se provádí pomocí **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) třídy.

Aplikace nemusí pro sledování různých připojovací řetězce nebo DB umístění přidružené k jiné řezy dat v horizontálně dělené prostředí. Místo toho [správce mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md) otevře připojení na správné databáze, pokud je nepotřebujete, založené na datech z mapy horizontálních oddílů a hodnota klíče horizontálního dělení, která je cílem dané žádosti o aplikace. Klíč je obvykle *customer_id*, *tenant_id*, *date_key*, nebo některé konkrétní identifikátor, který je základní parametr požadavek databáze.

Další informace najdete v tématu [škálování, SQL Server s směrování závislé na datech](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Stáhnout klientskou knihovnu

Stažení:

* Verze Javy knihovny, najdete v článku [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* Rozhraní .NET verze knihovny, najdete v článku [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Použití ShardMapManager v aplikaci směrování závislé na datech

Aplikace by měl vytvořit instanci **ShardMapManager** při inicializaci pomocí objekt pro vytváření volání **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). V tomto příkladu jak **ShardMapManager** a konkrétní **ShardMap** , který obsahuje jsou inicializovány. Tento příklad ukazuje GetSqlShardMapManager a GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) metody.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Nejnižší oprávnění pověření možné použít pro získání mapy horizontálních oddílů

Pokud aplikace není manipulace s vlastní mapy horizontálních oddílů, přihlašovací údaje použité pro výrobní metoda by měla mít oprávnění jen pro čtení na **globální mapy horizontálních oddílů** databáze. Tyto přihlašovací údaje se obvykle liší od přihlašovacích údajů, které slouží k otevření připojení do správce mapování horizontálních oddílů. Viz také [přihlašovací údaje pro přístup k Klientská knihovna Elastic Database](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Volání metody OpenConnectionForKey

**ShardMap.OpenConnectionForKey metoda** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) vrátí připojení připravené pro vydávání příkazů k příslušné databázi podle hodnoty **klíč** parametru. Informace o horizontálním oddílu se uloží do mezipaměti v aplikaci podle **ShardMapManager**, takže tyto požadavky nezahrnují obvykle databáze vyhledávání na základě **globální mapy horizontálních oddílů** databáze.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Klíč** parametr se používá jako klíč vyhledávání do mapy horizontálních oddílů k určení příslušné databázi pro požadavek.
* **ConnectionString** slouží k předání pouze uživatelská pověření pro požadovaného připojení. Žádný název databáze nebo název serveru je zahrnuté v tomto *connectionString* vzhledem k tomu, že tato metoda určí, databáze a serveru pomocí **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) by mělo být nastavené **ConnectionOptions.Validate** Pokud prostředí, ve kterém může mapy horizontálních oddílů změnit a řádků může přesunout do jiné databáze jako výsledek operace dělené tunelové propojení nebo sloučení. Toto ověření zahrnuje stručný dotaz, který mapy místní horizontálních oddílů v cílové databázi (nikoli do mapy horizontálních oddílů globální) před připojení se doručí do aplikace.

Selže-li ověření proti mapy horizontálních oddílů místní (což znamená, že mezipaměť je nesprávné), správce mapování horizontálních oddílů dotazuje mapy horizontálních oddílů globální získat nové správnou hodnotu pro vyhledávání, aktualizace mezipaměti a získat a vrátit připojení k příslušné databázi .

Použití **ConnectionOptions.None** pouze změny mapování horizontálních oddílů není očekávaného aplikace je online. V takovém případě hodnoty uložené v mezipaměti můžete být vždy předpokládá se, že správná a dalších round-trip ověření volání do cílové databáze mohly být bezpečně přeskočeny. Která snižuje zatížení databáze. **ConnectionOptions** může také nastavit prostřednictvím hodnoty v konfiguračním souboru k označení, zda změny horizontálního dělení se očekává, že nebo neměl určitou dobu.  

Tento příklad používá hodnotu celého čísla klíče **CustomerID**, použití **ShardMap** objekt s názvem **customerShardMap**.  

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

**OpenConnectionForKey** metoda vrátí nové už otevřené připojení ke správné databázi. Připojení v tímto způsobem stále plně využít sdružování připojení.

**OpenConnectionForKeyAsync metoda** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) je také k dispozici, pokud vaše aplikace provádí asynchronní programování pomocí.

## <a name="integrating-with-transient-fault-handling"></a>Integrace s zpracování přechodných chyb

Doporučujeme při vývoji aplikace přistupující k datům v cloudu je zajistit, že jsou zachyceny přechodné chyby aplikace a, operace se zopakují několikrát před došlo k chybě. Pro cloudové aplikace zpracování přechodných chyb je popsána v zpracování přechodných chyb ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Zpracování přechodných chyb mohou existovat vedle sebe přirozeně se vzorem směrování závislé na datech. Hlavním požadavkem je to chcete zkusit znovu, včetně celého datového přístupu požadavek **pomocí** blok, který se získat připojení směrování závislé na datech. V předchozím příkladu může být přepsán následujícím způsobem.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Příklad – závislé na datech směrování s zpracování přechodných chyb

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

Při vytváření elastické databáze ukázková aplikace se automaticky stáhnou balíčky, které jsou nezbytné k implementaci zpracování přechodných chyb.

## <a name="transactional-consistency"></a>Transakční konzistence

Transakční vlastnosti je zaručeno, že pro všechny operace místní do horizontálního oddílu. Můžete třeba spustit transakce odeslat prostřednictvím směrování závislé na datech v rámci oboru cílové horizontálního oddílu pro připojení. V tuto chvíli nejsou k dispozici žádné možnosti k dispozici pro uvedení více připojení do transakce, a proto neexistují žádné transakční záruky pro operace prováděné napříč horizontálními oddíly.

## <a name="next-steps"></a>Další postup

K odpojení horizontálního oddílu, nebo se znovu připojit do horizontálního oddílu, naleznete v tématu [oprava problémů s horizontálními oddíly mapy pomocí třídy RecoveryManager](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]