---
title: Víceklientské aplikace s nástroji RLS a elastické databáze
description: Pomocí nástrojů elastické databáze se zabezpečením na úrovni řádků sestavte aplikaci s vysoce škálovatelnou datovou vrstvou.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: b9550f365eb11ffff87add041824504488c0de15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619929"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Víceklientské aplikace s nástroji elastické databáze a zabezpečením na úrovni řádků
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Nástroje elastické databáze](elastic-scale-get-started.md) a [zabezpečení na úrovni řádků (RLS)][rls] spolupracují na povolení škálování datové vrstvy víceklientské aplikace s Azure SQL Database. Společně tyto technologie vám pomůžou vytvořit aplikaci, která má vysoce škálovatelnou datovou vrstvu. Datová vrstva podporuje více tenantů horizontálních oddílů a používá **ADO.NET SqlClient** nebo **Entity Framework**. Další informace najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS s Azure SQL Database](../../sql-database/saas-tenancy-app-design-patterns.md).

- **Nástroje elastické databáze** umožňují vývojářům škálovat datovou vrstvu standardními postupy horizontálního dělení, a to pomocí knihoven .NET a šablon služeb Azure. Správa horizontálních oddílů pomocí [klientské knihovny elastic Database][s-d-elastic-database-client-library] pomáhá automatizovat a zjednodušit mnoho úloh infrastruktury typicky spojených s horizontálního dělení.
- **Zabezpečení na úrovni řádků** umožňuje vývojářům bezpečně ukládat data pro více tenantů ve stejné databázi. Zásady zabezpečení RLS odfiltrují řádky, které nepatří do tenanta, který spouští dotaz. Centralizace logiky filtru v databázi zjednodušuje údržbu a snižuje riziko chyby zabezpečení. Alternativou použití všech klientských kódu pro vymáhání zabezpečení je riziková.

Pomocí těchto funkcí společně může aplikace ukládat data pro více tenantů ve stejné databázi horizontálních oddílů. Snižuje náklady na tenanta, když klient sdílí databázi. Stejná aplikace ale může nabízet i své klienty úrovně Premium, které platí pro vlastní vyhrazené horizontálních oddílů jednoho tenanta. Jednou z výhod izolace jednoho tenanta je záruka na výkon. V databázi s jedním klientem není pro prostředky konkurenční žádný jiný tenant.

Cílem je použít rozhraní API [Směrování závislé na datech](elastic-scale-data-dependent-routing.md) klientské knihovny k automatickému připojení každého daného tenanta ke správné databázi horizontálních oddílů. Pouze jeden horizontálních oddílů obsahuje konkrétní hodnotu TenantId pro daného tenanta. TenantId je *horizontálního dělení klíč*. Po navázání připojení se zásada zabezpečení RLS v rámci databáze ujistí, že daný tenant bude mít přístup pouze k datovým řádkům, které obsahují jeho TenantId.

> [!NOTE]
> Identifikátor tenanta se může skládat z více než jednoho sloupce. Pro usnadnění práce je tato diskuze považovat za TenantId v jednom sloupci.

![Architektura aplikace blogu][1]

## <a name="download-the-sample-project"></a>Stažení ukázkového projektu

### <a name="prerequisites"></a>Požadavky

- Použití sady Visual Studio (2012 nebo vyšší)
- Vytvořte v Azure SQL Database tři databáze.
- Stažení ukázkového projektu: [nástroje elastické databáze pro Azure SQL – multi-tenant horizontálních oddílů](https://go.microsoft.com/?linkid=9888163)
  - Vyplňte informace pro vaše databáze na začátku **program.cs**

Tento projekt rozšiřuje rozhraní [elastické databáze pro Azure SQL-Entity Framework Integration](elastic-scale-use-entity-framework-applications-visual-studio.md) přidáním podpory pro více tenantů databází horizontálních oddílů. Projekt vytvoří jednoduchou konzolovou aplikaci pro vytváření blogů a příspěvků. Projekt zahrnuje čtyři klienty a navíc dvě databáze horizontálních oddílů s více klienty. Tato konfigurace je znázorněna v předchozím diagramu.

Sestavte a spusťte aplikaci. Tím spustíte nástroj elastické databáze horizontálních oddílů Map Manager a provede následující testy:

1. Pomocí Entity Framework a LINQ vytvořte nový blog a zobrazte všechny blogy pro každého tenanta.
2. Zobrazení všech blogů pro tenanta pomocí ADO.NET SqlClient
3. Zkuste vložit Blog pro nesprávného tenanta, aby se ověřilo, že se vyvolala chyba.

Vzhledem k tomu, že v databázích horizontálních oddílů ještě není povolené zabezpečení na úrovni řádků, každá z těchto testů odhalí problém: klienti můžou zobrazit Blogy, které k nim nepatří, a aplikace nebrání vložení blogu pro nesprávného tenanta. Zbývající část tohoto článku popisuje, jak tyto problémy vyřešit vynucením izolace klientů s RLS. Existují dva kroky:

1. **Aplikační vrstva**: Upravte kód aplikace tak, aby vždy nastavil aktuální TenantId v \_ kontextu relace po otevření připojení. Ukázkový projekt již nastavuje TenantId tímto způsobem.
2. **Datová vrstva**: v každé databázi horizontálních oddílů vytvořte zásadu zabezpečení RLS pro filtrování řádků na základě TenantId uložených v \_ kontextu relace. Vytvořte zásady pro každou z vašich databází horizontálních oddílů, jinak se nefiltrují řádky ve více tenantůch horizontálních oddílů.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. aplikační vrstva: nastavte TenantId v kontextu relace. \_

Nejprve se připojíte k databázi horizontálních oddílů pomocí rozhraní API směrování závislých na datech klientské knihovny elastické databáze. Aplikace musí stále sdělit databázi, která TenantId používá připojení. TenantId říká zásadě zabezpečení RLS, které řádky se musí vyfiltrovat jako patřící jiným klientům. Uloží aktuální TenantId do [ \_ kontextu relace](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) připojení.

Alternativou kontextu relace \_ je použití [ \_ informací o kontextu](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale \_ kontext relace je lepší volbou. \_Kontext relace je snazší používat, ve výchozím nastavení vrací hodnotu null a podporuje páry klíč-hodnota.

### <a name="entity-framework"></a>Entity Framework

V případě aplikací, které používají Entity Framework, je nejjednodušší přístup k nastavení \_ kontextu relace v rámci přepsání ElasticScaleContext popsaného v tématu [Směrování závislého na datech pomocí EF DbContext](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Vytvořte a spusťte příkaz SqlCommand, který nastaví TenantId v \_ kontextu relace na shardingKey určenou pro připojení. Pak vrátí připojení zprostředkované prostřednictvím směrování závislého na datech. Tímto způsobem stačí napsat kód pouze jednou pro nastavení \_ kontextu relace.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

\_Kontext relace se teď automaticky nastaví se zadaným TenantId pokaždé, když se vyvolá ElasticScaleContext:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Pro aplikace využívající ADO.NET SqlClient vytvořte obálkovou funkci kolem metody ShardMap. OpenConnectionForKey. Před vrácením připojení automaticky nastaví obálku TenantId v \_ kontextu relace na aktuální TenantId. Chcete-li zajistit, aby \_ byl kontext relace vždy nastaven, měli byste otevřít pouze připojení pomocí této funkce obálky.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datová vrstva: Vytvoření zásad zabezpečení na úrovni řádků

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Vytvoření zásad zabezpečení pro filtrování řádků, ke kterým má každý tenant přístup

Teď, když aplikace nastavuje \_ kontext relace s aktuální TenantId před dotazování, můžou zásady zabezpečení RLS filtrovat dotazy a vyloučit řádky, které mají jiný TenantId.

RLS je implementováno v jazyce Transact-SQL. Uživatelsky definovaná funkce definují logiku přístupu a zásady zabezpečení vážou tuto funkci na libovolný počet tabulek. Pro tento projekt:

1. Funkce ověří, zda je aplikace připojena k databázi a zda je TenantId uložený v \_ kontextu relace shodný s TenantIdem daného řádku.
    - Aplikace je připojená, ale ne jiný uživatel SQL.

2. Predikát filtru umožňuje, aby řádky, které splňují filtr TenantId, prošly dotazy pro výběr, aktualizaci a odstranění.
    - Predikát bloku zabrání v vložení nebo aktualizaci řádků, které filtr nezdařil.
    - Pokud kontext relace nebyl \_ nastaven, funkce vrátí hodnotu null a žádné řádky nejsou viditelné nebo nemohou být vloženy.

Pokud chcete povolit RLS na všech horizontálních oddílů, spusťte následující T-SQL pomocí sady Visual Studio (SSDT), SSMS nebo skriptu PowerShellu, který je součástí projektu. Nebo pokud používáte [elastic Database úlohy](../../sql-database/elastic-jobs-overview.md), můžete automatizovat provádění tohoto T-SQL na všech horizontálních oddílů.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> Ve složitém projektu může být nutné přidat predikát na stovky tabulek, což může být zdlouhavé. K dispozici je uložená procedura pomocníka, která automaticky generuje zásadu zabezpečení a přidá predikát do všech tabulek ve schématu. Další informace najdete v blogovém příspěvku na stránce [použít Row-Level zabezpečení pro všechny tabulky – Pomocný skript (blog)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Když teď ukázkovou aplikaci znovu spustíte, klienti uvidí jenom řádky, které do nich patří. Kromě toho aplikace nemůže vkládat řádky, které patří do jiných klientů než z toho, který je aktuálně připojen k databázi horizontálních oddílů. Aplikace také nemůže aktualizovat TenantId ve všech řádcích, které může vidět. Pokud se aplikace pokusí udělat jednu, vyvolá se DbUpdateException.

Pokud později přidáte novou tabulku, změňte zásadu zabezpečení tak, aby do nové tabulky přidala predikáty filtru a blokování.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Přidání výchozích omezení pro automatické vyplňování TenantId pro vložení

Do každé tabulky můžete umístit výchozí omezení a automaticky tak vyplnit TenantId hodnotou aktuálně uloženou v \_ kontextu relace při vkládání řádků. Následuje příklad.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Aplikace teď při vkládání řádků nepotřebuje zadat TenantId:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Pokud použijete výchozí omezení pro Entity Framework projekt, doporučujeme *Nezahrnovat sloupec* TenantId do datového modelu EF. Toto doporučení je způsobeno tím, že Entity Framework dotazy automaticky poskytnou výchozí hodnoty, které přepíší výchozí omezení vytvořená v T-SQL, které používají \_ kontext relace.
> Chcete-li použít výchozí omezení v ukázkovém projektu, například byste měli odebrat TenantId z DataClasses.cs (a spustit Add-Migration v konzole správce balíčků) a použít T-SQL, abyste zajistili, že pole existuje pouze v databázových tabulkách. V tomto případě EF při vkládání dat automaticky dodává nesprávné výchozí hodnoty.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Volitelné Povolit *uživateli* přístup ke všem řádkům

Některé aplikace mohou chtít vytvořit *uživatele* , který má přístup ke všem řádkům. Uživatel může povolit vytváření sestav ve všech klientech ve všech horizontálních oddílů. Nebo může uživatel provést operace dělení na horizontálních oddílů, které zahrnují přesun řádků tenantů mezi databázemi.

Pokud chcete uživatele povolit, vytvořte `superuser` v každé databázi horizontálních oddílů nového uživatele SQL (v tomto příkladu). Pak změňte zásadu zabezpečení novou funkcí predikátu, která umožňuje tomuto uživateli přístup ke všem řádkům. Tato funkce je uvedena dále.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Údržba

- **Přidávání nových horizontálních oddílů**: spusťte skript T-SQL, který povolí RLS na všech nových horizontálních oddílů, jinak se dotazy na tyto horizontálních oddílů nefiltrují.
- **Přidávání nových tabulek**: Pokud se vytvoří nová tabulka, přidejte do zásad zabezpečení na všech horizontálních oddílů predikát Filter a Block. Jinak se dotazy na novou tabulku nefiltrují. Toto sčítání může být automatizováno pomocí triggeru DDL, jak je popsáno v tématu [použití Row-Level zabezpečení automaticky u nově vytvořených tabulek (blog)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393).

## <a name="summary"></a>Shrnutí

Nástroje elastické databáze a zabezpečení na úrovni řádků lze použít společně k horizontálnímu navýšení kapacity datové vrstvy aplikace s podporou pro více tenantů i pro jednoho tenanta horizontálních oddílů. Více tenantů horizontálních oddílů se dá použít k efektivnějšímu ukládání dat. Tato efektivita je vyslovovaná tam, kde velký počet klientů obsahuje jenom několik řádků dat. Jeden tenant horizontálních oddílů může podporovat klienty úrovně Premium, kteří mají přísnější požadavky na výkon a izolaci. Další informace najdete v referenčních informacích o [zabezpečení na úrovni řádků][rls].

## <a name="additional-resources"></a>Další zdroje

- [Co je elastický fond Azure?](elastic-pool-overview.md)
- [Horizontální navýšení kapacity s Azure SQL Database](elastic-scale-introduction.md)
- [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](../../sql-database/saas-tenancy-app-design-patterns.md)
- [Ověřování ve víceklientských aplikacích s využitím Azure AD a OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Aplikace Tailspin Surveys](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Dotazy a žádosti o funkce

Pokud máte otázky, kontaktujte nás na [stránce s dotazem pro Microsoft Q&SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html). A do [fóra SQL Database Feedback](https://feedback.azure.com/forums/217321-sql-database/)přidejte všechny žádosti o funkce.

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md
