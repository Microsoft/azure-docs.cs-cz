---
title: Víceklientské aplikace s RLS a nástroje elastické databáze | Microsoft Docs
description: Používejte nástroje elastické databáze pomocí zabezpečení na úrovni řádků pro vytvoření aplikace s vysoce škálovatelné datové vrstvy.
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
manager: craigg
author: tmullaney
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: thmullan
ms.openlocfilehash: 02ad01185a86aa5a975be2a66b54a214029dd73f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645806"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Víceklientské aplikací pomocí nástroje elastické databáze a zabezpečení na úrovni řádků

[Nástroje elastické databáze](sql-database-elastic-scale-get-started.md) a [nízkoúrovňového zabezpečení (RLS)] [ rls] spolupracují s cílem povolit škálování datovou vrstvu víceklientské aplikace s Azure SQL Database. Společně tyto technologie vám pomohou sestavit aplikaci, která je vysoce škálovatelná datové vrstvy. Datová vrstva podporuje víceklientské horizontálních oddílů a používá **ADO.NET SqlClient** nebo **Entity Framework**. Další informace najdete v tématu [návrhová schémata pro víceklientské aplikace SaaS ve službě Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Nástroje elastické databáze** umožňuje vývojářům škálovat vrstvu dat s standardní horizontálního dělení postupy, pomocí knihovny .NET a šablony služeb Azure. Správa horizontálních oddílů pomocí [klientské knihovny pro elastické databáze] [ s-d-elastic-database-client-library] pomáhá zautomatizovat a zjednodušit řadu infrastruktury úlohy, které jsou obvykle spojené s horizontálního dělení.
- **Zabezpečení na úrovni řádků** vývojářům umožňuje bezpečně uložit data pro více klientů ve stejné databázi. Zásady zabezpečení RLS odfiltrovat řádky, které nepatří do klienta provádění dotazu. Centralizuje logice filtru uvnitř databáze zjednodušuje údržbu a snižuje riziko chyby zabezpečení. Alternativní z spoléhat na všechny kód klienta enfore zabezpečení je rizikové.

Pomocí společně tyto funkce, může aplikace ukládat data pro více klientů ve stejné ID horizontálního oddílu databázi. Ho stojí méně každého klienta, když klienti sdílení databáze. Ještě stejnou aplikaci můžete také nabízejí jeho premium klientům možnost platícího pro své vlastní vyhrazené horizontálního oddílu jednoho klienta. Jednou z výhod izolace jednoho klienta je posílení jistoty výkonu. V databázi jednoho klienta neexistuje žádný další klient neslučitelných pro prostředky.

Cílem je použijte klientskou knihovnu elastické databáze [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md) rozhraní API pro automatické připojení k databázi správný horizontálních každého daného klienta. Jedinou horizontálních obsahuje konkrétní hodnotu TenantId pro daného klienta. Hodnoty TenantId je *horizontálního dělení klíč*. Po připojení, zásady zabezpečení RLS v rámci databáze zajišťuje, daného tenanta přístup pouze tyto řádky dat, které obsahují jeho TenantId.

> [!NOTE]
> Identifikátor klienta může obsahovat více než jeden sloupec. Pro usnadnění práce je toto pojednání, předpokládáme neformálně TenantId jeden sloupec.

![Architektura aplikace blogu][1]

## <a name="download-the-sample-project"></a>Stáhněte si ukázkový projekt

### <a name="prerequisites"></a>Požadavky

- Použijte sadu Visual Studio (2012 nebo vyšší) 
- Vytvořte tři databáze SQL Azure 
- Stáhněte si ukázkový projekt: [elastické databáze nástroje pro Azure SQL - víceklientské horizontálních oddílů](http://go.microsoft.com/?linkid=9888163)
  - Zadejte informace pro vaše databáze na začátku **Program.cs** 

Rozšiřuje popsané v tomto projektu [elastické databáze nástroje pro Azure SQL - Entity Framework integrace](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) přidáním podpory pro víc klientů horizontálních databáze. Sestavení projektu jednoduché konzolové aplikace pro vytvoření blogy a příspěvky. Projekt obsahuje čtyři klientů a také dvě databáze víceklientské horizontálního oddílu. Tato konfigurace je znázorněna na předchozím obrázku. 

Sestavte a spusťte aplikaci. To běžet bootstraps správce mapy horizontálního oddílu nástroje elastické databáze a provádí následující testy: 

1. Pomocí rozhraní Entity Framework a LINQ, vytvořte nový blog a následně se zobrazí všechny blogy pro každého klienta
2. Pomocí ADO.NET SqlClient, zobrazte všechny blogy pro klienta
3. Pokuste se vložit blogu pro chybný klienta k ověření, že je vyvolána chyba  

Všimněte si, že vzhledem k tomu, že RLS dosud nebyla spuštěna v databázích horizontálního oddílu, každý z těchto testů zjistí problém: Klienti mohou vidět blogy, který nepatří do nich a aplikace není zabránila vkládání blogu pro chybný klienta. Zbývající část tohoto článku popisuje, jak vyřešit tyto problémy vynucování izolace klienta s RLS. Existují dva kroky: 

1. **Aplikační vrstvě**: Upravit kód aplikace vždy nastavit aktuální TenantId v RELACI\_KONTEXT po otevření připojení. Ukázkový projekt již nastaví hodnoty TenantId tímto způsobem. 
2. **Datová vrstva**: vytvoření zásady zabezpečení RLS v každé databázi horizontálního oddílu filtrování řádků podle TenantId uložené v RELACI\_kontextu. Vytvořit zásadu pro každou databází horizontálního oddílu, jinak nejsou filtrovány řádků v víceklientské horizontálních oddílů. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Aplikační vrstvě: Sada TenantId v RELACI\_kontextu

Nejprve je připojit k databázi horizontálního oddílu pomocí rozhraní API směrování dat závislé Klientská knihovna pro elastické databáze. Aplikace stále musí zjistit databázi které TenantId používá připojení. Hodnoty TenantId informuje zásady zabezpečení RLS řádky musí být odfiltrována jako náležící do ostatních klientů. Uložení aktuální TenantId v [relace\_kontextu](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) připojení.

Alternativu k RELACI\_kontextu, je použít [kontextu\_informace](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). RELACE, ale\_kontextu není lepší volbou. RELACE\_kontextu je jednodušší použít, vrátí hodnotu NULL ve výchozím nastavení a podporuje páry klíč hodnota.

### <a name="entity-framework"></a>Entity Framework

Pro aplikace používající rozhraní Entity Framework, je snadnější přístup k nastavení relace\_kontextu v rámci ElasticScaleContext přepsání popsané v [závislé na Data směrování pomocí EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Vytvoření a provedení SqlCommand, který nastaví TenantId v RELACI\_KONTEXT, který má shardingKey zadaný pro připojení. Pak se vraťte připojení zprostředkované přes závislé na data směrování. Tímto způsobem, je potřeba jenom jeden zápis kódu nastavit relace\_kontextu. 

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

Nyní relace\_je automaticky nastaven KONTEXT s zadaný TenantId vždy, když je volána ElasticScaleContext: 

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

Pro aplikace pomocí ADO.NET SqlClient vytvořte funkci obálku kolem metoda ShardMap.OpenConnectionForKey. Mít obálku automaticky nastaví TenantId v RELACI\_KONTEXT, který má aktuální TenantId před vrácením připojení. Zajistit, že relace\_vždy nastaven KONTEXT, otvírat pouze připojení pomocí této funkce obálku.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datová vrstva: vytvoření zásad zabezpečení na úrovni řádků

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Vytvoření zásady zabezpečení pro filtrování řádky, které můžete přístup každý klient

Teď, když je aplikace nastavení relace\_KONTEXT aktuální TenantId před dotazování, zásady zabezpečení RLS můžete filtrovat dotazy a vyloučení řádky, které mají různé TenantId.  

Funkce RLS se implementuje v Transact-SQL. Uživatelem definované funkce definuje logiku přístupu a zásady zabezpečení sváže tuto funkci Libovolný počet tabulek. Pro tento projekt:

1. Funkce ověřuje, že aplikace je připojený k databázi, a že hodnoty TenantId uložené v RELACI\_kontextu odpovídá TenantId daného řádku.
    - Aplikace je připojený místo jiný uživatel SQL.

2. Predikát filtru umožňuje řádky, které vyhovují filtru TenantId předávat vyberte aktualizace a odstranit dotazy.
    - Predikát BLOCK zabraňuje řádky, které nesplní filtr z vložené nebo aktualizované.
    - Pokud relace\_KONTEXT nebyl nastaven, funkce vrátí hodnotu NULL a viditelný nebo může být vložen nejsou žádné řádky. 

Pokud chcete povolit RLS na všechny horizontálních oddílů, spusťte následující T-SQL pomocí Visual Studio (SSDT), aplikace SSMS nebo ve skriptu PowerShell, který je zahrnutý v projektu. Nebo pokud používáte [elastické databáze úlohy](sql-database-elastic-jobs-overview.md), je možné automatizovat provádění této T-SQL na všechny horizontálních oddílů.

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
> V projektu komplexní možná budete muset přidat predikát na stovky tabulky, který může být zdlouhavé. Je pomocná uložené procedury, která automaticky generuje zásadu zabezpečení a přidá predikát ve všech tabulkách ve schématu. Další informace najdete v příspěvku na blogu [použití zabezpečení na úrovni řádků pro všechny tabulky – Pomocník skriptu (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Pokud spustíte ukázkovou aplikaci znovu, nyní tenantům v pouze řádky, které patří k nim. Kromě toho aplikace nelze vložit řádky, které patří do klientů jiných než ten, který aktuálně připojené k databázi horizontálního oddílu. Aplikaci nelze aktualizovat také TenantId v žádné řádky, které můžete zobrazit. Pokud se aplikace pokusí udělat, je vyvolána DbUpdateException.

Pokud později přidáte nové tabulky, změnit zásady zabezpečení a přidejte filtr a zároveň se ZABLOKUJÍ predikáty na novou tabulku.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Přidat výchozí omezení automatické vyplnění TenantId pro vložení

Můžete vložit výchozího omezení na každou tabulku automaticky naplnit hodnoty TenantId s hodnotou, které jsou aktuálně uloženy v RELACI\_KONTEXT při vložení řádků. Následuje příklad. 

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

Aplikaci teď není nutné zadávat TenantId při vložení řádků: 

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
> Pokud používáte výchozí omezení pro projekt Entity Framework, je doporučeno, které *není* zahrnout do modelu dat EF sloupci TenantId. Toto doporučení je, protože rozhraní Entity Framework dotazuje automaticky zadejte výchozí hodnoty, které potlačí výchozí omezení vytvořené v T-SQL, které používají relace\_kontextu.
> V projektu vzorku použít výchozí omezení, například doporučujeme odebrat TenantId z DataClasses.cs (a spuštění Add-Migration v konzole Správce balíčků) a zkontrolujte, zda pole existuje pouze v tabulkách databáze pomocí T-SQL. Tímto způsobem EF automaticky zadat nesprávné výchozí hodnoty při vkládání dat.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Volitelné) Povolit *superuživatel* přístup všechny řádky

Některé aplikace může být vhodné vytvořit *superuživatel* přístup všechny řádky. Superuživatele může povolit vytváření sestav napříč všichni klienti na všechny horizontálních oddílů. Nebo superuživatele může provádět operace sloučení rozdělení na horizontálních oddílů, které vyžadují přesunutí řádků klienta mezi databázemi.

Pokud chcete povolit superuživatele, vytvořte nového uživatele SQL (`superuser` v tomto příkladu) v každé databázi horizontálního oddílu. Potom změňte zásady zabezpečení s novou predikátem funkci, která umožňuje tento uživatel pro přístup k všechny řádky. Tyto funkce je uvedeno dále.

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

- **Přidání nové horizontálních oddílů**: spuštění skriptu T-SQL povolit RLS na všechny nové horizontálních oddílů, jinak nejsou filtrovány dotazy na tyto horizontálních oddílů.
- **Přidání nové tabulky**: Přidání predikát filtru a blokování do nastavení zásad zabezpečení na všechny horizontálních oddílů vždy, když je vytvořena nová tabulka. V opačném případě nefiltrují dotazy na novou tabulku. Přidání je možné automatizovat pomocí aktivační událost jazyka DDL, jak je popsáno v [použití zabezpečení na úrovni řádků automaticky do nově vytvořené tabulky (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Souhrn

Nástroje elastické databáze a zabezpečení na úrovni řádků může být společně slouží jako škálování aplikace datové vrstvy s podporou pro oba více klientů a jednoho klienta horizontálních oddílů. Víceklientské horizontálních oddílů lze použít k ukládání dat efektivněji. Této efektivity je výraznější, kde velký počet klientů, které mají jenom pár řádků data. Horizontálních oddílů jednoho klienta může podporovat klienty premium, které mají přísnější výkon a požadavky na izolaci.  Další informace najdete v tématu [zabezpečení na úrovni řádků odkaz][rls].

## <a name="additional-resources"></a>Další zdroje informací:

- [Co je Azure elastickém fondu?](sql-database-elastic-pool.md)
- [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Ověřování ve víceklientských aplikacích s využitím Azure AD a OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikace Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce

Máte dotazy, kontaktujte nás na [SQL Database fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). A přidejte všechny žádosti o funkce pro [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

