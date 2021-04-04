---
title: Použití klientské knihovny elastické databáze s Entity Framework
description: Použití Elastic Database klientské knihovny a Entity Framework pro kódování databází
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 48d43cb2d3c51194d0708a2b9b739a0ee87843d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793394"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Elastic Database klientské knihovny s Entity Framework
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento dokument zobrazuje změny v Entity Framework aplikaci, které jsou potřeba pro integraci s [nástroji elastic Database](elastic-scale-introduction.md). Zaměřuje se na vytváření [horizontálních oddílůch map](elastic-scale-shard-map-management.md) a [Směrování závislých na datech](elastic-scale-data-dependent-routing.md) s přístupem **Code First** Entity Framework. Kurz [Code First-New Database](/ef/ef6/modeling/code-first/workflows/new-database) pro EF slouží jako příklad spuštění v celém tomto dokumentu. Vzorový kód doprovázející tento dokument je součástí sady ukázek nástrojů elastické databáze v ukázkách Visual Studio Code.

## <a name="downloading-and-running-the-sample-code"></a>Stažení a spuštění ukázkového kódu

Stažení kódu pro tento článek:

* Vyžaduje se Visual Studio 2012 nebo novější.
* Stáhněte si [ukázku integrace nástrojů elastické databáze pro Azure SQL Entity Framework](https://github.com/Azure/elastic-db-tools/). Rozbalte ukázku do zvoleného umístění.
* Spusťte Visual Studio.
* V aplikaci Visual Studio vyberte soubor-> otevřít projekt nebo řešení.
* V dialogovém okně **Otevřít projekt** přejděte k ukázce, kterou jste stáhli, a výběrem **EntityFrameworkCodeFirst. sln** otevřete ukázku.

Pokud chcete ukázku spustit, musíte v Azure SQL Database vytvořit tři prázdné databáze:

* Databáze správce map horizontálních oddílů
* Databáze horizontálních oddílů 1
* Databáze horizontálních oddílů 2

Po vytvoření těchto databází vyplňte držitele umístění v **programu program. cs** pomocí názvu serveru, názvů databází a vašich přihlašovacích údajů pro připojení k databázím. Sestavte řešení v aplikaci Visual Studio. Visual Studio stáhne požadované balíčky NuGet pro klientskou knihovnu elastické databáze, Entity Framework a zpracování přechodných chyb v rámci procesu sestavení. Ujistěte se, že je pro vaše řešení povolené obnovování balíčků NuGet. Toto nastavení můžete povolit tak, že kliknete pravým tlačítkem na soubor řešení v Průzkumník řešení sady Visual Studio.

## <a name="entity-framework-workflows"></a>Pracovní postupy Entity Framework

Entity Framework vývojáři při sestavování aplikací a zajištění trvalého fungování pro objekty aplikací spoléhají na jeden z následujících čtyř pracovních postupů:

* **Code First (nová databáze)**: vývojář EF vytvoří model v kódu aplikace a pak z něj vygeneruje databázi EF.
* **Code First (existující databáze)**: vývojář umožňuje, aby EF vygeneroval kód aplikace pro model z existující databáze.
* **Model First**: Vývojář vytvoří model v Návrháři EF a pak EF vytvoří databázi z modelu.
* **Database First**: vývojář pomocí nástrojů EF odvodí model z existující databáze.

Všechny tyto přístupy využívají třídu DbContext k transparentní správě databázových připojení a schématu databáze pro aplikaci. Různé konstruktory v základní třídě DbContext umožňují různé úrovně kontroly nad vytvořením připojení, navázáním databáze a vytvořením schématu. K problémům dochází hlavně ze skutečnosti, že Správa připojení k databázi poskytovaná pomocí EF překládá s možnostmi správy připojení pro rozhraní směrování závislá na datech, která poskytuje Klientská knihovna elastické databáze.

## <a name="elastic-database-tools-assumptions"></a>Předpoklady pro nástroje elastické databáze

Definice termínů najdete v článku [Glosář nástrojů pro elastic Database](elastic-scale-glossary.md).

Pomocí klientské knihovny elastické databáze definujete oddíly dat vaší aplikace s názvem shardlety. Shardlety jsou označeny klíčem horizontálního dělení a jsou namapovány na konkrétní databáze. Aplikace může mít podle potřeby tolik databází a distribuovat shardlety, aby poskytoval dostatek kapacity nebo výkon pro aktuální obchodní požadavky. Mapování hodnot klíče horizontálního dělení na databáze je uloženo v mapě horizontálních oddílů, kterou poskytuje rozhraní API klienta elastické databáze. Tato funkce se nazývá **Správa map horizontálních oddílů** nebo SMM pro krátké. Mapa horizontálních oddílů slouží také jako zprostředkovatel připojení databáze pro požadavky, které přenášejí klíč horizontálního dělení. Tato funkce se označuje jako **Směrování závislé na datech**.

Správce map horizontálních oddílů chrání uživatele před nekonzistentními zobrazeními do dat shardletu, ke kterým může dojít, když se děje souběžné operace správy shardletu (například přemístění dat z jednoho horizontálních oddílů na jiný). K tomu se mapy horizontálních oddílů spravují pomocí zprostředkovatele klientské knihovny pro připojení k databázi pro aplikaci. To umožňuje, aby funkce mapy horizontálních oddílů automaticky ukončila připojení k databázi, zatímco operace správy horizontálních oddílů by mohly ovlivnit shardletu, pro které bylo připojení vytvořeno. Tento přístup se musí integrovat s některými funkcemi EF, jako je například vytváření nových připojení z existujícího objektu pro kontrolu existence databáze. Obecně jsme naše sledování ukázali, že standardní konstruktory DbContext fungují pouze spolehlivě pro uzavřená databázová připojení, která je možné bezpečně klonovat pro práci EF. Princip návrhu elastické databáze je místo toho jenom k otevřeným připojením. Může to znamenat, že před tím, než se tento problém DbContext EF, může dojít k tomu, že se před tím, než se předá přes EF, dopřed převzetí připojení Ukončením připojení a spoléháme se na EF, aby ho bylo možné znovu otevřít, jedna foregoes ověřování a kontroly konzistence prováděné knihovnou. Funkce migrace v EF používá tato připojení ke správě základního schématu databáze způsobem, který je pro aplikaci transparentní. V ideálním případě budete tyto možnosti uchovávat a kombinovat jak z klientské knihovny elastické databáze, tak z EF ve stejné aplikaci. Následující část popisuje tyto vlastnosti a požadavky podrobněji.

## <a name="requirements"></a>Požadavky

Při práci s klientskou knihovnou a Entity Framework API pro elastickou databázi chcete zachovat následující vlastnosti:

* **Horizontální** navýšení kapacity: Chcete-li přidat nebo odebrat databáze z datové vrstvy aplikace horizontálně dělené podle potřeby požadavků na kapacitu aplikace. To znamená kontrolu nad vytvořením a odstraněním databází a používáním rozhraní API elastické databáze horizontálních oddílů map pro správu databází a mapování shardlety.
* **Konzistence**: aplikace využívá horizontálního dělení a využívá možnosti směrování závislé na datech klientské knihovny. Aby se zabránilo poškození nebo špatnému výsledku dotazu, připojení se provádí prostřednictvím Správce map horizontálních oddílů. Tím se také zachovává ověřování a konzistence.
* **Code First**: pro zachování pohodlí prvního paradigma kódu EF. V Code First třídy v aplikaci jsou transparentně mapovány na podkladové struktury databáze. Kód aplikace komunikuje s DbSets, které maskují většinu aspektů při zpracování základní databáze.
* **Schéma**: Entity Framework zpracovává počáteční vytváření schématu databáze a následné vývoj schématu prostřednictvím migrací. Díky zachování těchto schopností je přizpůsobení aplikace snadné při vývoji dat.

Následující pokyny popisují, jak tyto požadavky naplnit Code First aplikací pomocí nástrojů elastické databáze.

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Směrování závislé na datech pomocí EF DbContext

Databázová připojení pomocí Entity Framework jsou obvykle spravovaná pomocí podtříd třídy **DbContext**. Vytvořte tyto podtřídy odvozené z **DbContext**. Zde definujete **DbSets** , které implementují kolekce objektů CLR zálohovaných databázemi pro vaši aplikaci. V kontextu směrování závislého na datech můžete identifikovat několik užitečných vlastností, které nejsou nutně uloženy pro jiné scénáře použití prvního kódu EF:

* Databáze již existuje a byla registrována v mapě horizontálních oddílů elastické databáze.
* Schéma aplikace již bylo nasazeno do databáze (vysvětlení níže).
* Připojení směrování závislá na datech k databázi jsou založená na mapě horizontálních oddílů.

Integrace **DbContexts** se směrováním závislým na datech pro škálování na více instancí:

1. Vytvořte připojení fyzické databáze prostřednictvím klientských rozhraní elastické databáze správce mapy horizontálních oddílů.
2. Zabalte připojení k podtřídě **DbContext**
3. Předejte připojení dolů do základních tříd **DbContext** , aby bylo zajištěno, že veškeré zpracování na straně EF dojde také k.

Následující příklad kódu ukazuje tento přístup. (Tento kód je také v doprovodném projektu sady Visual Studio.)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection
    // routed to the proper shard by the shard map manager.
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr),
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Hlavní body

* Nový konstruktor nahradí výchozí konstruktor v podtřídě DbContext.
* Nový konstruktor přijímá argumenty, které jsou vyžadovány pro směrování závislé na datech prostřednictvím klientské knihovny elastické databáze:
  
  * Mapa horizontálních oddílů pro přístup k rozhraním směrování závislým na datech,
  * klíč horizontálního dělení k identifikaci shardletu,
  * připojovací řetězec s přihlašovacími údaji pro připojení směrování závislé na datech ke službě horizontálních oddílů.
* Volání konstruktoru základní třídy převezme ze seznamu statickou metodu, která provede všechny kroky nezbytné pro směrování závislé na datech.
  
  * K navázání otevřeného připojení používá OpenConnectionForKey volání klientských rozhraní elastické databáze na mapě horizontálních oddílů.
  * Mapa horizontálních oddílů vytvoří otevřené připojení k horizontálních oddílů, které obsahuje shardletu pro daný klíč horizontálního dělení.
  * Toto otevřené připojení se předává zpět do konstruktoru základní třídy DbContext, který označuje, že toto připojení má být použito v EF místo toho, aby EF vytvořilo nové připojení automaticky. Tímto způsobem bylo připojení označeno rozhraním API klienta elastické databáze, aby mohlo zaručit konzistenci v rámci operací správy mapy horizontálních oddílů.

Použijte nový konstruktor pro podtřídu DbContext namísto výchozího konstruktoru ve vašem kódu. Tady je příklad:

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: ");
var name = Console.ReadLine();

using (var db = new ElasticScaleContext<int>(
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString))
{
    var blog = new Blog { Name = name };
    db.Blogs.Add(blog);
    db.SaveChanges();

    // Display all Blogs for tenant 1
    var query = from b in db.Blogs
                orderby b.Name
                select b;
    …
}
```

Nový konstruktor otevře připojení k horizontálních oddílů, které obsahuje data pro shardletu identifikovaný hodnotou **tenantid1**. Kód v bloku **using** zůstane beze změny pro přístup k **negenerickými** pro blogy pomocí EF na horizontálních oddílů pro **tenantid1**. Tato změna sémantiky kódu v bloku using tak, aby všechny operace databáze byly nyní vymezeny na jeden horizontálních oddílů, kde je **tenantid1** uložený. Například dotaz LINQ přes Blogy **negenerickými** by vrátil jenom Blogy uložené na aktuální horizontálních oddílů, ale ne ty, které jsou uložené na jiných horizontálních oddílů.  

### <a name="transient-faults-handling"></a>Zpracování přechodných chyb

Tým Microsoft Patterns & Practices publikoval [blokované aplikace pro zpracování přechodného selhání](/previous-versions/msp-n-p/dn440719(v=pandp.60)). Knihovna se používá společně s klientskou knihovnou elastického škálování v kombinaci s EF. Zajistěte však, aby se jakákoliv přechodná výjimka vrátila na místo, kde můžete zajistit, že se nový konstruktor používá po přechodné chybě, aby byl proveden nový pokus o připojení pomocí vámi přidaných konstruktorů. V opačném případě není zaručené připojení ke správnému horizontálních oddílů a neexistují žádné záruky, že se připojení zachovává beze změn v mapě horizontálních oddílů.

Následující příklad kódu ukazuje, jak lze použít zásadu opakování SQL kolem nových konstruktorů podtříd **DbContext** :

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString))
        {
                var blog = new Blog { Name = name };
                db.Blogs.Add(blog);
                db.SaveChanges();
        …
        }
    });
```

**SqlDatabaseUtils. SqlRetryPolicy** ve výše uvedeném kódu je definováno jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací doba mezi opakovanými pokusy. Tento přístup je podobný pokynům pro EF a uživatelem iniciované transakce (viz [omezení s opakováním strategií spouštění (EF6 a vyšší)](/ef/ef6/fundamentals/connection-resiliency/retry-logic). Obě situace vyžadují, aby program aplikace řídí rozsah, na který vrací přechodnou výjimku: Chcete-li buď transakci znovu otevřít, nebo (jak je vidět), vytvořte kontext znovu ze správného konstruktoru, který používá knihovnu klienta elastické databáze.

Nutnost řídit, kde přechodné výjimky v oboru vznikne zpátky, kromě toho vylučuje použití vestavěných **SqlAzureExecutionStrategy** , které jsou součástí EF. **SqlAzureExecutionStrategy** by znovu otevřel připojení, ale nepoužíval **OpenConnectionForKey** , a proto obejít všechna ověření, která se provádí jako součást volání **OpenConnectionForKey** . Místo toho ukázka kódu používá integrovaný **DefaultExecutionStrategy** , který je také součástí EF. Na rozdíl od **SqlAzureExecutionStrategy** funguje správně v kombinaci se zásadami opakování při zpracování přechodného selhání. Zásady spouštění se nastavují ve třídě **ElasticScaleDbConfiguration** . Zjistili jsme, že nepoužíváme **DefaultSqlExecutionStrategy** , protože navrhuje použití **SqlAzureExecutionStrategy** , pokud dojde k přechodným výjimkám, což by vedlo k chybnému chování, jak je popsáno v tomto tématu. Další informace o různých zásadách opakování a EF najdete [v tématu odolnost připojení v EF](/ef/ef6/fundamentals/connection-resiliency/retry-logic).

#### <a name="constructor-rewrites"></a>Přepsání konstruktoru

Výše uvedené příklady kódu ilustrují výchozí konstruktor pro opětovné zápisy, které jsou požadovány pro vaši aplikaci, aby bylo možné použít směrování závislé na datech s Entity Framework. Následující tabulka generalizuje tento přístup k jiným konstruktorům.

| Aktuální konstruktor | Přepsaný konstruktor pro data | Základní konstruktor | Poznámky |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |Připojení musí být funkce mapy horizontálních oddílů a klíč směrování závislý na datech. Musíte obejít automatické vytvoření připojení podle EF a místo toho použít mapu horizontálních oddílů ke zprostředkování připojení. |
| MyContext (řetězec) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |Připojení je funkce mapy horizontálních oddílů a klíč směrování závislý na datech. Pevný název databáze nebo připojovací řetězec nefungují tak, jak by prošlo ověřením pomocí mapy horizontálních oddílů. |
| MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Vytvoří se připojení pro danou mapu horizontálních oddílů a klíč horizontálního dělení s poskytnutým modelem. Kompilovaný model je předán základnímu c'tor. |
| MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Připojení musí být odvoditelné z mapy horizontálních oddílů a klíče. Nedá se zadat jako vstup (Pokud tento vstup už nepoužívá mapu horizontálních oddílů a klíč). Logická hodnota je předána. |
| MyContext (řetězec; DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |Připojení musí být odvoditelné z mapy horizontálních oddílů a klíče. Nedá se zadat jako vstup (Pokud tento vstup nepoužil mapu horizontálních oddílů a klíč). Byl předán zkompilovaný model. |
| MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Nový konstruktor musí zajistit, aby jakékoli připojení v objektu ObjectContext předané jako vstup bylo přesměrováno na připojení spravované elastickým škálováním. Podrobná diskuze o rozhraních ObjectContext překračuje rozsah tohoto dokumentu. |
| MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, bool); |Připojení musí být odvoditelné z mapy horizontálních oddílů a klíče. Připojení nelze zadat jako vstup (Pokud tento vstup již nepoužívá mapu horizontálních oddílů a klíč). Model a logická hodnota jsou předány konstruktoru základní třídy. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Nasazení schématu horizontálních oddílů prostřednictvím migrací EF

Automatická správa schématu je pohodlí, kterou poskytuje Entity Framework. V souvislosti s aplikacemi pomocí nástrojů elastické databáze chcete tuto funkci zachovat, aby se schéma automaticky vytvořilo pro nově vytvořená horizontálních oddílů při přidání databází do aplikace horizontálně dělené. Primárním případem použití je zvýšit kapacitu v datové vrstvě pro aplikace horizontálně dělené pomocí EF. Spoléhání se na funkce EF pro správu schématu snižuje úsilí správy databáze s horizontálně dělené aplikací postavenou na EF.

Nasazení schématu prostřednictvím migrací EF funguje nejlépe pro **Neotevřená připojení**. To je na rozdíl od scénáře pro směrování závislé na datech, které spoléhá na otevřené připojení poskytované rozhraním API klienta elastické databáze. Dalším rozdílem je požadavek konzistence: Přestože je žádoucí zajistit konzistenci všech připojení směrování závislých na datech, aby se zajistila ochrana proti souběžné manipulaci s horizontálních oddílů, nejedná se o implementaci počátečního nasazení schématu do nové databáze, která ještě není zaregistrovaná v mapě horizontálních oddílů a ještě není přidělená pro blokování shardlety. Proto se můžete spoléhat na běžná připojení databáze pro tento scénář, a to na rozdíl od směrování závislého na datech.  

To vede k přístupu k tomu, že nasazení schématu prostřednictvím migrací EF je úzce spojeno s registrací nové databáze jako horizontálních oddílů v mapě horizontálních oddílů aplikace. To závisí na následujících předpokladech:

* Databáze již byla vytvořena.
* Databáze je prázdná – neobsahuje žádné uživatelské schéma ani uživatelská data.
* K databázi ještě nelze přistupovat prostřednictvím rozhraní API klienta elastické databáze pro směrování závislé na datech.

Pomocí těchto požadavků můžete vytvořit běžný neotevřený **SqlConnection** a aktivovat tak migrace EF pro nasazení schématu. Tento přístup je znázorněn v následujícím příkladu kódu.

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema

public void RegisterNewShard(string server, string database, string connStr, int key)
{

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database));

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr);
    connStrBldr.DataSource = server;
    connStrBldr.InitialCatalog = database;

    // Go into a DbContext to trigger migrations and schema deployment for the new shard.
    // This requires an un-opened connection.
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString))
    {
        // Run a query to engage EF migrations
        (from b in db.Blogs
            select b).Count();
    }

    // Register the mapping of the tenant to the shard in the shard map.
    // After this step, data-dependent routing on the shard map can be used

    this.ShardMap.CreatePointMapping(key, shard);
}
```

Tato ukázka ukazuje metodu **RegisterNewShard** , která registruje horizontálních oddílů na mapě horizontálních oddílů, nasadí schéma prostřednictvím migrací EF a ukládá mapování horizontálního dělení klíče do horizontálních oddílů. Spoléhá se na konstruktor podtřídy **DbContext** (**ElasticScaleContext** v ukázce), který jako vstup přebírá připojovací řetězec SQL. Kód tohoto konstruktoru je přímý posun, jak ukazuje následující příklad:

```csharp
// C'tor to deploy schema and migrations to a new shard
protected internal ElasticScaleContext(string connectionString)
    : base(SetInitializerForConnection(connectionString))
{
}

// Only static methods are allowed in calls into base class c'tors
private static string SetInitializerForConnection(string connectionString)
{
    // You want existence checks so that the schema can get deployed
    Database.SetInitializer<ElasticScaleContext<T>>(
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>());

    return connectionString;
}
```

Jedna z nich mohla použít verzi konstruktoru zděděnou ze základní třídy. Kód však musí zajistit, aby byl při připojování použit výchozí inicializátor pro EF. Proto krátká deprohlídka do statické metody před voláním do konstruktoru základní třídy s připojovacím řetězcem. Všimněte si, že registrace horizontálních oddílů by měla běžet v jiné doméně nebo procesu aplikace, aby se zajistilo, že nastavení inicializátoru pro EF nejsou v konfliktu.

## <a name="limitations"></a>Omezení

Přístup popsaný v tomto dokumentu má několik omezení:

* Aplikace EF, které používají **LocalDB** , musí nejdřív před použitím klientské knihovny pro elastickou databázi migrovat do běžné databáze SQL Server. Škálování aplikací prostřednictvím horizontálního dělení s elastickým škálováním s **LocalDB** není možné. Všimněte si, že vývoj může stále používat **LocalDB**.
* Změny v aplikaci, které zahrnují změny schématu databáze, musí projít migracemi EF na všech horizontálních oddílů. Vzorový kód tohoto dokumentu neukazuje, jak to provést. Zvažte použití Update-Database s parametrem ConnectionString k iterování všech horizontálních oddílů; nebo extrahujte skript T-SQL pro nevyřízenou migraci pomocí Update-Database s možností-Script a použijte skript T-SQL pro vaši horizontálních oddílů.  
* S ohledem na požadavek se předpokládá, že veškeré jeho zpracování databáze je obsaženo v rámci jednoho horizontálních oddílů, jak je identifikované pomocí klíče horizontálního dělení, který poskytuje požadavek. Tento předpoklad však vždy nedrží hodnotu true. Například, pokud není možné vytvořit horizontálního dělení klíč k dispozici. Pro tuto adresu Klientská knihovna poskytuje třídu **MultiShardQuery** , která implementuje abstrakci připojení pro dotazování přes několik horizontálních oddílů. Naučíte se používat **MultiShardQuery** v kombinaci s EF je nad rámec tohoto dokumentu.

## <a name="conclusion"></a>Závěr

Pomocí kroků popsaných v tomto dokumentu můžou aplikace EF používat funkci klientské knihovny elastické databáze pro směrování závislé na datech pomocí konstruktorů refaktoringu podtříd **DbContext** používaných v aplikaci EF. Tím se omezí změny požadované u těchto míst, kde třídy **DbContext** již existují. Kromě toho mohou aplikace v EF nadále využívat výhod nasazení automatického schématu kombinací kroků, které vyvolávají nezbytné migrace EF s registrací nových horizontálních oddílů a mapování v mapě horizontálních oddílů.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png