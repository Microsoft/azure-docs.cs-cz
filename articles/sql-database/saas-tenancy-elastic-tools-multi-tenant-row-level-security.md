---
title: Aplikace s více tenanty pomocí zabezpečení na úrovni řádků a nástrojů pro elastické databáze | Dokumentace Microsoftu
description: Používejte nástroje elastické databáze pomocí zabezpečení na úrovní řádků k sestavení aplikace s vysoce škálovatelnou datovou vrstvou.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: d669e7beb2d0e41ff26408d4f71c3e1648c41e3a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242564"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplikace s více tenanty s nástroji elastic database a zabezpečení na úrovní řádků

[Nástroje pro elastické databáze](sql-database-elastic-scale-get-started.md) a [nízkoúrovňového zabezpečení (RLS)] [ rls] spolupracují s cílem povolit škálování datové vrstvy aplikace s více tenanty s databází Azure SQL Database. Společně tyto technologie vám pomůže vytvářet aplikace, která má vysoce škálovatelnou datovou vrstvou. Datová vrstva podporuje víceklientské horizontální oddíly a používá **Sqlclienta ADO.NET** nebo **Entity Framework**. Další informace najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS využívající Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Nástroje pro elastické databáze** umožňují vývojářům horizontálně navýšit kapacitu datové vrstvy se standardní běžných postupech, pomocí knihoven .NET a šablon služby Azure. Správa horizontálních oddílů pomocí [Klientská knihovna Elastic Database] [ s-d-elastic-database-client-library] pomáhá automatizovat a zefektivnit mnoho infrastruktury úloh obvykle spojené s horizontálního dělení.
- **Zabezpečení na úrovní řádků** vývojářům umožňuje bezpečně ukládat data pro více tenantů v jedné databázi. Zásady zabezpečení RLS vyfiltrování řádků, které nepatří do klienta, spouštění dotazu. Centralizace filtr logika uvnitř databáze lze usnadnit správu a snižuje riziko vzniku chyb zabezpečení. Alternativou sady spoléhat na všechny klientské kódu k vynucení zabezpečení je nebezpečné.

Pomocí společně tyto funkce, může aplikace ukládat data pro více tenantů v jedné databázi horizontálních oddílů. To stojí méně každého tenanta když klienti sdílí databázi. Ještě stejné aplikace lze také nabízejí jeho tenanti úrovně premium možnost platit za své vlastní vyhrazený horizontálních oddílů jednoho tenanta. Jednou z výhod izolace jednoho tenanta je záruky týkající se posílení výkonu. V databázi jednoho tenanta neexistuje žádný tenanta, které soutěží o prostředky.

Cílem je používat Klientská knihovna elastic database [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) rozhraní API pro každý daného tenanta automaticky připojit k databázi správné horizontálních oddílů. Pouze jeden horizontální oddíl obsahuje určitou hodnotu TenantId pro daného tenanta. ID Tenanta je *klíč horizontálního dělení*. Po navázání připojení zásadu zabezpečení zabezpečení na úrovni řádků v databázi zajišťuje, že daného tenanta přístup pouze řádky dat, které obsahují jeho ID Tenanta.

> [!NOTE]
> Identifikátor tenanta může obsahovat více než jeden sloupec. Pro usnadnění práce je této diskuse, předpokládáme neformálně jednosloupcová TenantId.

![Architektura blogovací aplikace][1]

## <a name="download-the-sample-project"></a>Stáhněte si ukázkový projekt

### <a name="prerequisites"></a>Požadavky

- Pomocí sady Visual Studio (2012 nebo novějším)
- Vytvořte tři databáze Azure SQL
- Stáhněte si ukázkový projekt: [elastické databáze nástroje pro Azure SQL – víceklientské horizontální oddíly](http://go.microsoft.com/?linkid=9888163)
  - Vyplňte informace o vašich databází na začátku **Program.cs** 

Rozšiřuje popsané v tomto projektu [elastické databáze nástroje pro Azure SQL – integrace Entity Frameworku](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) přidáním podpory pro databáze s více tenanty horizontálními oddíly. Projekt se sestaví jednoduchou konzolovou aplikaci pro vytváření blogů a příspěvky. Projekt nezahrnuje čtyřmi klienty a dvěma databázemi s horizontálními oddíly více tenantů. Tato konfigurace je znázorněno na předchozím obrázku. 

Sestavte a spusťte aplikaci. Tento běh bootstraps nástrojů elastické databáze správce mapování horizontálních oddílů a provádí následující testy: 

1. Použití rozhraní Entity Framework a LINQ, vytvoření nového blogu a zobrazte všechny blogy pro každého klienta
2. Pomocí Sqlclienta ADO.NET, zobrazte všechny blogy pro tenanta
3. Pokuste se vložit do blogu pro nesprávného tenanta, chcete-li ověřit, že se zobrazuje chyba  

Všimněte si, že vzhledem k tomu, že RLS ještě nepovolila v databázemi s horizontálními oddíly, každá z těchto testů zjistí problém: tenantů jsou vidět blogy, které nepatří k nim a aplikace není zabráněno vložení blogu pro nesprávného tenanta. Zbývající část tohoto článku popisuje, jak tyto problémy vyřešit vynucování izolace klienta pomocí zabezpečení na úrovni řádků. Existují dva kroky: 

1. **Aplikační vrstva**: upravovat kód aplikace vždy nastavit aktuální ID Tenanta v RELACI\_kontextu po otevření připojení. Ukázkový projekt již nastaví ID Tenanta tímto způsobem. 
2. **Datová vrstva**: vytvoření zásad zabezpečení zabezpečení na úrovni řádků v jednotlivých horizontálních oddílů databáze filtrovat řádky podle ID Tenanta uložená v RELACI\_kontextu. Vytvořit zásadu pro každou z vaší databáze s horizontálními oddíly, jinak nejsou filtrované řádky v horizontálních oddílech více tenantů. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Aplikační vrstva: Sada ID Tenanta v RELACI\_kontextu

Nejprve je připojit k databázi horizontálních oddílů s použitím závislé na datech směrování rozhraní API, aby Klientská knihovna elastic database. Aplikace stále zapotřebí sdělit databáze TenantId, která používá připojení. ID Tenanta říká zásad zabezpečení zabezpečení na úrovni řádků, musí se odfiltrovat, které řádky jako patřící do jiných tenantů. Aktuální ID Tenanta v Store [relace\_kontextu](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) připojení.

Alternativa k RELACI\_kontextu, je použít [kontextu\_informace](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Ale relace\_KONTEXT je lepší volbou. RELACE\_se snadněji používá KONTEXT, ve výchozím nastavení vrátí hodnotu NULL a podporuje páry klíč hodnota.

### <a name="entity-framework"></a>Entity Framework

Pro aplikace používající nástroj Entity Framework, je nejjednodušší přístup k nastavení relace\_kontextu v rámci ElasticScaleContext přepsání je popsáno v [závislé na datech směrování, pomocí EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Vytvoření a provedení SqlCommand, který nastaví ID Tenanta v RELACI\_kontextu shardingKey zadané pro připojení. Pak se vraťte připojení zprostředkované přes směrování závislé na datech. Díky tomu je potřeba jenom napsat kód jednou k nastavení relace\_kontextu. 

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

Nyní relace\_je automaticky nastaven KONTEXT s zadané TenantId pokaždé, když je vyvolána ElasticScaleContext: 

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

### <a name="adonet-sqlclient"></a>Sqlclienta ADO.NET

Pro aplikace pomocí Sqlclienta ADO.NET vytvořte funkce obálky kolem metoda ShardMap.OpenConnectionForKey. Obálka automaticky nastaví ID Tenanta v RELACI mít\_kontextu aktuální ID Tenanta před vrácením připojení. K zajištění této relace\_kontextu je vždycky nastavený, byste měli otvírat jenom připojení pomocí této funkce obálky.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datová vrstva: vytvoření zásad zabezpečení na úrovní řádků

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Vytvoření zásad zabezpečení filtrovat řádky, které můžete přístup každý klient

Teď, když je aplikace nastavení relace\_KONTEXT s aktuálním ID Tenanta před dotazování zásadu zabezpečení zabezpečení na úrovni řádků můžete filtrovat dotazy a vyloučit řádky, které mají různé TenantId.  

Zabezpečení na úrovni řádků je implementována v jazyce Transact-SQL. Uživatelem definované funkce definuje logikou přístupu a zásady zabezpečení připojí tuto funkci do libovolného počtu tabulek. Pro tento projekt:

1. Funkce ověří, že aplikace je připojený k databázi, a že ID Tenanta uložená v RELACI\_kontextu souhlasí s ID Tenanta daného řádku.
    - Připojen k aplikaci, místo jiný uživatel SQL.

2. Predikát filtru umožňuje řádky, které vyhovují filtru TenantId předávání pro SELECT, UPDATE a odstraňování dotazů.
    - Predikát BLOCK brání řádky, které nesplní filtr ze sloupce vložené nebo aktualizované.
    - Pokud relace\_kontextu nebyla nastavena, funkce vrátí hodnotu NULL a žádné řádky jsou viditelné nebo může být vložen. 

Povolit zabezpečení na úrovni řádků na všechny horizontální oddíly, spusťte následující příkaz T-SQL pomocí Visual Studio (SSDT), aplikace SSMS nebo powershellu zahrnutý v projektu. Nebo pokud používáte [úlohy Elastic Database](sql-database-elastic-jobs-overview.md), můžete automatizovat provádění tohoto jazyka T-SQL na všechny horizontální oddíly.

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
> V komplexní projekt možná budete muset přidat predikát na stovkách tabulky, který může být zdlouhavé. Je pomocná uložené procedury, která automaticky generuje zásadu zabezpečení a přidá predikát ve všech tabulkách ve schématu. Další informace najdete v příspěvku na blogu [použít zabezpečení na úrovní řádků pro všechny tabulky – pomocné rutiny skriptu (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Teď Pokud spustíte ukázkovou aplikaci znovu, tenantů se projeví pouze řádky, které patří k nim. Kromě toho aplikace nelze vložit řádky, které patří do jiných než ten, který aktuálně připojených do horizontálních oddílů databáze tenantů. Aplikaci nelze aktualizovat také ID Tenanta v nějaké řádky, které můžete zobrazit. Pokud se aplikace pokusí udělat, je vyvolána DbUpdateException.

Pokud chcete přidat novou tabulku později změnit zásady zabezpečení a přidejte filtr a blok predikáty na novou tabulku.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Přidat výchozí omezení k automatickému vyplnění TenantId pro vložení

Můžete vložit výchozího omezení na každou tabulku k automatickému vyplnění ID Tenanta s hodnotou aktuálně uložené v RELACI\_kontextu při vkládání řádků. Následuje příklad. 

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

Aplikace teď není potřeba zadat TenantId při vkládání řádků: 

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
> Pokud používáte výchozí omezení pro projekt rozhraní Entity Framework, je doporučeno, kterou jste *není* obsahovat sloupec ID Tenanta v datovém modelu EF. Toto doporučení totiž Entity Framework dotazuje automaticky zadejte výchozí hodnoty, které přepíší výchozí omezení, vytvořené v T-SQL, které používají relace\_kontextu.
> V projektu vzorku používat výchozí omezení, například by měl odeberete TenantId z DataClasses.cs (a spuštění přidat migrace v konzole Správce balíčků) a zkontrolujte, zda pole existuje pouze v tabulkách databáze pomocí jazyka T-SQL. Tímto způsobem EF automaticky zadat nesprávné výchozích hodnot při vkládání dat.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Volitelné) Povolit *superuživatele* pro přístup k všechny řádky

Některé aplikace může být vhodné vytvořit *superuživatele* kdo má přístup k všechny řádky. Superuživatele může povolit hlášení ve všech tenantech na všechny horizontální oddíly. Nebo superuživatele může provádět operace dělení a slučování s horizontálními oddíly, které zahrnují přesun řádků tenanta mezi databázemi.

Pokud chcete povolit superuživatele, vytvořte nového uživatele SQL (`superuser` v tomto příkladu) v každé databázi horizontálních oddílů. Potom změňte zásady zabezpečení s novou predikátu funkci, která umožňuje tento uživatel pro přístup k všechny řádky. Takové funkce je uvedeno dále.

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

- **Přidání nových horizontálních oddílů**: spuštění skriptu T-SQL k povolení zabezpečení na úrovni řádků na všech nových horizontálních oddílů, v opačném případě nejsou filtrovány dotazy v těchto horizontálních oddílech.
- **Přidání nových tabulek**: Přidat predikát filtru a blok zásady zabezpečení na všechny horizontální oddíly pokaždé, když je vytvořena nová tabulka. V opačném případě nefiltrují dotazy na novou tabulku. Toto přidání je možné automatizovat pomocí aktivační událost jazyka DDL, jak je popsáno v [použít zabezpečení na úrovní řádků automaticky na nově vytvořené tabulky (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Souhrn

Nástroje pro elastické databáze a zabezpečení na úrovní řádků může být horizontální oddíly společně slouží jako horizontální navýšení kapacity aplikace datové vrstvy s podporou pro oba více tenantů a jednoho tenanta. Víceklientské horizontální oddíly je možné ukládat data efektivněji. Této efektivity je výraznější, kde mají pouze několik řádků dat velkého počtu klientů. Horizontální oddíly jednoho tenanta může podporovat tenanti úrovně premium, které mají větší výkon a požadavky na izolaci.  Další informace najdete v tématu [informace o zabezpečení na úrovni řádků][rls].

## <a name="additional-resources"></a>Další zdroje informací:

- [Co je elastický fond Azure?](sql-database-elastic-pool.md)
- [Horizontální navýšení kapacity s Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Ověřování ve víceklientských aplikacích s využitím Azure AD a OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplikace Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Otázky a žádosti o funkce

Máte dotazy, kontaktujte nás na [fórum SQL Database](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). A přidejte jakékoli žádosti o funkce pro [fóru pro zpětnou vazbu SQL Database](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

