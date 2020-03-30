---
title: Aplikace pro více klientů s RLS a elastickými databázovými nástroji
description: Pomocí elastických databázových nástrojů se zabezpečením na úrovni řádků můžete vytvořit aplikaci s vysoce škálovatelnou datovou vrstvou.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5fe5d6d4076c5d82d33737d05bb95ede0a89c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822021"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplikace s více klienty s elastickými databázovými nástroji a zabezpečením na úrovni řádků

[Elastické databázové nástroje](sql-database-elastic-scale-get-started.md) a [zabezpečení na úrovni řádků (RLS)][rls] spolupracují, aby umožnily škálování datové vrstvy víceklientské aplikace pomocí Azure SQL Database. Společně tyto technologie vám pomohou vytvořit aplikaci, která má vysoce škálovatelnou datovou vrstvu. Datová vrstva podporuje víceklientské střepy a používá **ADO.NET sqlclient** nebo **entity framework .** Další informace najdete [v tématu Návrhové vzory pro víceklientské aplikace SaaS s databází Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Nástroje elastické databáze** umožňují vývojářům škálovat datovou vrstvu pomocí standardních postupů horizontálního horizontálování pomocí knihoven .NET a šablon služeb Azure. Správa úlomků pomocí [klientské knihovny elastické databáze][s-d-elastic-database-client-library] pomáhá automatizovat a zefektivnit mnoho infrastrukturních úloh, které jsou obvykle spojeny s říšnictvím.
- **Zabezpečení na úrovni řádků** umožňuje vývojářům bezpečně ukládat data pro více klientů ve stejné databázi. Zásady zabezpečení RLS odfiltrovat řádky, které nepatří do klienta provádění dotazu. Centralizace logiky filtru uvnitř databáze zjednodušuje údržbu a snižuje riziko chyby zabezpečení. Alternativa spoléhání se na všechny klientský kód k vynucení zabezpečení je riskantní.

Pomocí těchto funkcí společně aplikace můžete ukládat data pro více klientů ve stejné databázi svižné skříně. Náklady méně na klienta, když klienti sdílejí databázi. Přesto stejná aplikace může také nabídnout svým prémiovým nájemcům možnost platit za své vlastní vyhrazené jednotenantské střepy. Jednou z výhod izolace jednoho tenanta jsou pevnější záruky výkonu. V databázi s jedním tenantem neexistuje žádný jiný klient, který by soutěžil o prostředky.

Cílem je použít elastické databáze klientské knihovny [dat závislé směrování](sql-database-elastic-scale-data-dependent-routing.md) API automaticky připojit každý daný klient a správné databáze svižných materiálů. Pouze jeden úlomek obsahuje konkrétní Hodnotu TenantId pro daného klienta. TenantId je *klíč srážlivých.* Po navázání připojení zásady zabezpečení RLS v rámci databáze zajišťuje, že daný klient má přístup pouze k těm řádkům dat, které obsahují jeho TenantId.

> [!NOTE]
> Identifikátor klienta se může skládat z více než jednoho sloupce. Pro pohodlí je tato diskuse neformálně předpokládáme, že tenantid s jedním sloupcem.

![Architektura aplikací pro blogování][1]

## <a name="download-the-sample-project"></a>Stažení ukázkového projektu

### <a name="prerequisites"></a>Požadavky

- Použití sady Visual Studio (2012 nebo vyšší)
- Vytvoření tří databází Azure SQL
- Stáhnout ukázkový projekt: [Elastické nástroje DB pro Azure SQL – střepy s více tenanty](https://go.microsoft.com/?linkid=9888163)
  - Vyplňte informace o svých databázích na začátku **Program.cs**

Tento projekt rozšiřuje ten popsaný v [elastické DB nástroje pro Azure SQL – entity framework integrace](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) přidáním podpory pro víceklientské databáze shard. Projekt vytváří jednoduchou konzolovou aplikaci pro vytváření blogů a příspěvků. Projekt zahrnuje čtyři klienty a dvě víceklientské databáze střepů. Tato konfigurace je znázorněna v předchozím diagramu.

Sestavte a spusťte aplikaci. Tento běh zaváděcí zavádění správce střepů elastické databázové nástroje a provede následující testy:

1. Pomocí Entity Framework a LINQ vytvořte nový blog a pak zobrazte všechny blogy pro každého klienta.
2. Použití ADO.NET SqlClient, zobrazit všechny blogy pro klienta
3. Pokuste se vložit blog pro nesprávného klienta a ověřte, zda je vyvolána chyba.

Všimněte si, že vzhledem k tomu, že RLS ještě nebyla povolena v databázích střepů, každý z těchto testů odhalí problém: klienti jsou schopni zobrazit blogy, které nepatří k nim a aplikace není zabráněno vložení blogu pro nesprávného klienta. Zbývající část tohoto článku popisuje, jak vyřešit tyto problémy vynucením izolace klienta s RLS. Existují dva kroky:

1. **Aplikační vrstva**: Upravte kód aplikace tak, aby\_vždy nastavil aktuální Id tenanta v kontextu relace po otevření připojení. Ukázkový projekt již nastaví TenantId tímto způsobem.
2. **Datová vrstva**: Vytvořte zásady zabezpečení RLS v každé databázi\_svižných_ Vytvořte zásadu pro každou z databází střepu, jinak řádky ve víceklientských šmejdů nejsou filtrovány.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Aplikační vrstva: Nastavení Id tenanta v kontextu relace\_

Nejprve se připojíte k databázi svižných pásů pomocí směrovacích api závislých na datech klientské knihovny elastické databáze. Aplikace stále musí sdělit databázi, která TenantId používá připojení. TenantId říká zásady zabezpečení RLS, které řádky musí být odfiltrovány jako patřící k ostatním klientům. Uložte aktuální TenantId v [kontextu relace\_](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) připojení.

Alternativou k\_kontextu relace je použití [kontextové\_informace](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale\_session context je lepší volba. SESSION\_CONTEXT je jednodušší, ve výchozím nastavení vrátí hodnotu NULL a podporuje dvojice klíč-hodnota.

### <a name="entity-framework"></a>Entity Framework

Pro aplikace používající entity Framework je nejjednodušším\_přístupem nastavení kontextu SESSION v rámci přepsání ElasticScaleContext popsaného v [směrování závislém na datech pomocí EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Vytvořte a spusťte SqlCommand, který\_nastaví TenantId v kontextu SESSION na shardingKey určené pro připojení. Potom vraťte připojení zprostředkované prostřednictvím směrování závisléna datech. Tímto způsobem stačí napsat kód jednou nastavit\_kontext relace.

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

Nyní je\_kontext SESSION automaticky nastaven se zadaným Id tenanta při každém vyvolání Funkce ElasticScaleContext:

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

### <a name="adonet-sqlclient"></a>ADO.NET sqlklienta

Pro aplikace používající ADO.NET SqlClient vytvořte obálkovou funkci kolem metody ShardMap.OpenConnectionForKey. Mít obálku automaticky nastavit TenantId\_v kontextu relace na aktuální TenantId před vrácením připojení. Chcete-li\_zajistit, aby kontext relace byl vždy nastaven, měli byste otevřít pouze připojení pomocí této funkce obálky.

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

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Vytvoření zásad zabezpečení pro filtrování řádků, ke kterýmá má každý klient přístup

Teď, když aplikace\_nastavuje KONTEXT SESSION s aktuálním Id tenanta před dotazováním, může zásada zabezpečení RLS filtrovat dotazy a vyloučit řádky, které mají jiné Id tenanta.

RLS je implementována v Transact-SQL. Uživatelem definovaná funkce definuje logiku přístupu a zásada zabezpečení váže tuto funkci na libovolný počet tabulek. Pro tento projekt:

1. Funkce ověří, že aplikace je připojenk databázi a že TenantId uložené v kontextu SESSION\_odpovídá TenantId daného řádku.
    - Aplikace je připojena, spíše než některé jiné uživatele SQL.

2. Predikát FILTER umožňuje řádky, které splňují tenantika filtr předat pro select, UPDATE a DELETE dotazy.
    - Predikát BLOCK zabraňuje řádkům, které nepolyká, že filtr je INSERTed nebo UPDATEd.
    - Pokud\_kontext relace nebylnastaven, funkce vrátí hodnotu NULL a žádné řádky nejsou viditelné nebo nelze vložit.

Chcete-li povolit RLS na všech šerpů, spusťte následující T-SQL pomocí Visual Studio (SSDT), SSMS nebo powershellskript zahrnuty v projektu. Nebo pokud používáte [funkce Elastic Database Jobs](elastic-jobs-overview.md), můžete automatizovat provádění tohoto T-SQL na všech šmejdů.

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
> Ve složitém projektu možná budete muset přidat predikát na stovky tabulek, které by mohly být únavné. Existuje pomocná procedura, která automaticky generuje zásady zabezpečení a přidá predikát ve všech tabulkách ve schématu. Další informace naleznete v příspěvku blogu [na adrese Apply Row-Level Security pro všechny tabulky – pomocný skript (blog).](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)

Nyní pokud spustíte ukázkovou aplikaci znovu, klienti zobrazit pouze řádky, které patří k nim. Kromě toho aplikace nemůže vložit řádky, které patří do klientů než ten, který je aktuálně připojen k databázi střepů. Aplikace také nelze aktualizovat TenantId v libovolné řádky, které můžete vidět. Pokud se aplikace pokusí provést buď DbUpdateException je aktivována.

Pokud přidáte novou tabulku později, změňte zásady zabezpečení přidat filtr a blok predikáty na novou tabulku.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Přidání výchozích omezení k automatickému vyplnění Id tenanta pro INSERTs

Můžete umístit výchozí omezení pro každou tabulku automaticky naplnit TenantId s\_hodnotou aktuálně uloženou v kontextu relace při vkládání řádků. Příklad následuje.

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

Nyní aplikace není nutné zadat TenantId při vkládání řádků:

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
> Pokud používáte výchozí omezení pro projekt entity framework, je *doporučeno,* že není zahrnuta TenantId sloupec v datovém modelu EF. Toto doporučení je, že entity Framework dotazy automaticky poskytují výchozí hodnoty, které\_přepsat výchozí omezení vytvořená v T-SQL, které používají KONTEXT RELACE.
> Chcete-li použít výchozí omezení v ukázkovém projektu, například byste měli odebrat TenantId z DataClasses.cs (a spustit migraci doplňků v konzole Správce balíčků) a pomocí T-SQL zajistit, že pole existuje pouze v databázových tabulkách. Tímto způsobem EF automaticky zadat nesprávné výchozí hodnoty při vkládání dat.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Nepovinné) Povolení přístupu *superuživatele* ke všem řádkům

Některé aplikace mohou chtít vytvořit *superuživatele,* který má přístup ke všem řádkům. Superuživatel by mohl povolit vytváření sestav napříč všemi tenanty na všech šmejdů. Nebo superuživatel může provádět operace rozdělení sloučení na štrůdk, které zahrnují přesunutí řádků klienta mezi databázemi.

Chcete-li povolit superuser, vytvořte nového uživatele SQL (`superuser` v tomto příkladu) v každé databázi střepů. Potom změňte zásady zabezpečení pomocí nové funkce predikátu, která umožňuje tomuto uživateli přístup ke všem řádkům. Taková funkce je uvedena dále.

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

- **Přidání nových štřepů**: Spusťte skript T-SQL, který povolí RLS na všech nových šerpech, jinak se dotazy na tyto šerpy nefiltrují.
- **Přidání nových tabulek**: Při každé vytvoření nové tabulky přidejte predikát FILTER a BLOCK do zásad zabezpečení na všech štrůdcích. V opačném případě nejsou dotazy v nové tabulce filtrovány. Toto přidání lze automatizovat pomocí aktivační události DDL, jak je popsáno v [článku Použít zabezpečení na úrovni řádků automaticky na nově vytvořené tabulky (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Souhrn

Elastické databázové nástroje a zabezpečení na úrovni řádků lze společně škálovat na škálování datové vrstvy aplikace s podporou pro horizontální chod více klientů i jednoklientské horizontálních oddílů. Víceklientské úlomky lze použít k efektivnějšímu ukládání dat. Tato účinnost se vyslovuje, kde velký počet klientů má pouze několik řádků dat. Oddíly s jedním tenantem mohou podporovat klienty úrovně úrovně, které mají přísnější požadavky na výkon a izolaci. Další informace naleznete v [tématu Odkaz na zabezpečení na úrovni řádků][rls].

## <a name="additional-resources"></a>Další zdroje

- [Co je elastický fond Azure?](sql-database-elastic-pool.md)
- [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Ověřování ve víceklientských aplikacích s využitím Azure AD a OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikace Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce

V případě dotazů nás kontaktujte na [fóru SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). A přidat všechny funkce požadavky na [sql databáze zpětnou vazbu forum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
