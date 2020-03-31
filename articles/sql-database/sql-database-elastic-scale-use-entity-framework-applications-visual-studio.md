---
title: Použití klientské knihovny elastické databáze s rozhraním Entity Framework
description: Použití klientské knihovny elastické databáze a entity frameworku pro kódování databází
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087186"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Klientská knihovna elastická databáze s rozhraním Entity Framework

Tento dokument zobrazuje změny v aplikaci Entity Framework, které jsou potřebné k integraci s [nástroji elastické databáze](sql-database-elastic-scale-introduction.md). Důraz je kladen na vytváření [správu mapy svižných](sql-database-elastic-scale-shard-map-management.md) [bitových rámců](sql-database-elastic-scale-data-dependent-routing.md) a směrování závislé na datech pomocí přístupu Entity Framework **Code First.** [Kurz Code First - New Database](https://msdn.microsoft.com/data/jj193542.aspx) pro EF slouží jako spuštěný příklad v celém tomto dokumentu. Ukázkový kód doprovázející tento dokument je součástí sady ukázky elastických databázových nástrojů v ukázkách kódu sady Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Stahování a spuštění ukázkového kódu

Chcete-li stáhnout kód pro tento článek:

* Visual Studio 2012 nebo novější je povinné. 
* Stáhněte si [ukázku nástroje Elastic DB pro Azure SQL – integraci entity frameworku](https://github.com/Azure/elastic-db-tools/). Rozbalte vzorek na místo podle vašeho výběru.
* Spusťte Visual Studio. 
* V Sadě Visual Studio vyberte Soubor -> Otevřít projekt/řešení. 
* V dialogovém okně **Otevřít projekt** přejděte na ukázku, kterou jste stáhli, a výběrem **entityFrameworkCodeFirst.sln** otevřete ukázku. 

Chcete-li spustit ukázku, je potřeba vytvořit tři prázdné databáze v Azure SQL Database:

* Databáze Správce map úlomků
* Databáze úlomku 1
* Databáze střepů 2

Po vytvoření těchto databází vyplňte zástupné symboly v **Program.cs** s názvem serveru Azure SQL DB, názvy databází a přihlašovacími údaji pro připojení k databázím. Vytvořte řešení v sadě Visual Studio. Visual Studio stáhne požadované balíčky NuGet pro klientskou knihovnu elastické databáze, entity framework a zpracování přechodných chyb jako součást procesu sestavení. Ujistěte se, že obnovení balíčků NuGet je povoleno pro vaše řešení. Toto nastavení můžete povolit kliknutím pravým tlačítkem myši na soubor řešení v Průzkumníku řešení sady Visual Studio. 

## <a name="entity-framework-workflows"></a>Pracovní postupy entity Framework

Vývojáři entity Framework spoléhají na jeden z následujících čtyř pracovních postupů při vytváření aplikací a zajištění trvalosti pro aplikační objekty:

* **Code First (Nová databáze)**: Vývojář EF vytvoří model v kódu aplikace a potom EF generuje databázi z něj. 
* **Code First (Existující databáze)**: Vývojář umožňuje EF generovat kód aplikace pro model z existující databáze.
* **Model First**: Vývojář vytvoří model v návrháři EF a potom EF vytvoří databázi z modelu.
* **Databáze první**: Vývojář používá nástroje EF k odvodit model z existující databáze. 

Všechny tyto přístupy spoléhají na DbContext třídy transparentní spravovat připojení k databázi a schéma databáze pro aplikaci. Různé konstruktory v základní třídě DbContext umožňují různé úrovně kontroly nad vytvářením připojení, zaváděním databáze a vytvářením schématu. Výzvy vyplývají především ze skutečnosti, že správa připojení k databázi poskytované EF protíná s možnostmi správy připojení dat závislých na směrování rozhraní poskytovaných knihovnou klienta elastické databáze. 

## <a name="elastic-database-tools-assumptions"></a>Předpoklady elastických databázových nástrojů

Definice termínů naleznete v [tématu Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md).

S klientskou knihovnou elastické databáze definujete oddíly dat aplikace nazývané shardlety. Shardlets jsou identifikovány pomocí klíče s ráždí a jsou mapovány na konkrétní databáze. Aplikace může mít tolik databází podle potřeby a distribuovat shardlety poskytnout dostatečnou kapacitu nebo výkon dané aktuální obchodní požadavky. Mapování hodnot klíče s ráždí do databází je uloženo pomocí mapy skládek poskytované rozhraní API klienta elastické databáze. Tato funkce se nazývá **Správa mapy sypu**nebo Zkráceně SMM. Mapa střepu slouží také jako zprostředkovatel připojení databáze pro požadavky, které nesou klíč s ráždí. Tato funkce se označuje jako **směrování závislé na datech**. 

Správce mapy střepů chrání uživatele před nekonzistentními zobrazeními do dat shardletu, ke kterým může dojít, když probíhají souběžné operace správy shardletu (například přemístění dat z jednoho úlomku do druhého). Chcete-li tak učinit, mapy svižné klientské knihovny zprostředkovatele připojení databáze pro aplikaci. To umožňuje funkce mapy střepu automaticky uhasit připojení k databázi, když operace správy střepu může mít vliv na shardlet, pro který bylo vytvořeno připojení. Tento přístup je třeba integrovat s některé funkce EF, jako je například vytváření nových připojení z existující ho zkontrolovat existenci databáze. Obecně naše pozorování bylo, že standardní DbContext konstruktory pracovat pouze spolehlivě pro uzavřené databázové připojení, které lze bezpečně klonovat pro práci EF. Princip návrhu elastické databáze místo toho je pouze zprostředkovatel otevřené připojení. Jeden by si mohl myslet, že uzavření připojení zprostředkované klientské knihovny před předáním ef dbcontext může tento problém vyřešit. Však uzavřením připojení a spoléhání se na EF znovu otevřít, jeden zříká ověření a kontroly konzistence prováděné knihovnou. Funkce migrace v EF, však používá tato připojení ke správě základní schéma databáze způsobem, který je transparentní pro aplikaci. V ideálním případě budete zachovat a kombinovat všechny tyto funkce z klientské knihovny elastické databáze a EF ve stejné aplikaci. Následující část popisuje tyto vlastnosti a požadavky podrobněji. 

## <a name="requirements"></a>Požadavky

Při práci s klientskou knihovnou elastické databáze a rozhraními API entity framework uchovávejte následující vlastnosti: 

* **Horizontální navýšení kapacity**: Chcete-li přidat nebo odebrat databáze z datové vrstvy horizontálně větvičné aplikace podle potřeby pro požadavky na kapacitu aplikace. To znamená, že řízení nad vytváření a odstraňování databází a pomocí elastické databáze horizontálního oddílu rozhraní API správce map pro správu databází a mapování shardlets. 
* **Konzistence**: Aplikace využívá funkce pro řídké části a využívá možnosti směrování závislé na datech klientské knihovny. Chcete-li se vyhnout poškození nebo nesprávných výsledků dotazu, jsou připojení zprostředkována prostřednictvím správce mapy sustrovatelnosti. To také zachovává ověření a konzistenci.
* **Kód první**: Chcete-li zachovat pohodlí ef kód první paradigma. V Code First třídy v aplikaci jsou mapovány transparentně na základní struktury databáze. Kód aplikace spolupracuje s DbSets, které maskují většinu aspektů zapojených do zpracování podkladové databáze.
* **Schéma**: Entity Framework zpracovává počáteční vytváření schématu databáze a následné vývoj schématu prostřednictvím migrace. Zachováním těchto funkcí je přizpůsobení aplikace s ním snadné. 

Následující pokyny návod, jak splnit tyto požadavky pro aplikace Code First pomocí elastických databázových nástrojů. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Směrování závislé na datech pomocí EF DbContext

Připojení k databázi s entity framework jsou obvykle spravovány prostřednictvím podtříd **DbContext**. Vytvořte tyto podtřídy odvozením z **DbContext**. Toto je místo, kde definujete **vaše DbSets,** které implementují kolekce objektů CLR zálohované databází pro vaši aplikaci. V kontextu směrování závislé na datech můžete identifikovat několik užitečných vlastností, které nemusí být nutně v držení pro jiné scénáře aplikace první ef kód: 

* Databáze již existuje a byla zaregistrována v mapě střepů elastické databáze. 
* Schéma aplikace již bylo nasazeno do databáze (vysvětleno níže). 
* Připojení směrování závislých na datech k databázi jsou zprostředkována mapou svižného oddílu. 

Integrace **DbContexts** s směrováním závislými na datech pro horizontální navýšení kapacity:

1. Vytvořit připojení fyzické databáze prostřednictvím klientských rozhraní elastické databáze správce map střepů, 
2. Zalomení připojení s podtřídou **DbContext**
3. Předají připojení dolů do **dbcontext** základní třídy zajistit všechny zpracování na straně EF se stane také. 

Následující příklad kódu ilustruje tento přístup. (Tento kód je také v doprovodném projektu Sady Visual Studio)

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

* Nový konstruktor nahradí výchozí konstruktor v podtřídě DbContext 
* Nový konstruktor přebírá argumenty, které jsou požadovány pro směrování závislé na datech prostřednictvím klientské knihovny elastické databáze:
  
  * mapa úlomků pro přístup k směrovacím rozhraním závislým na datech,
  * klíč k střepování k identifikaci shardletu,
  * připojovací řetězec s pověřeními pro připojení směrování závislých na datech k úlovci. 
* Volání konstruktoru základní třídy provede objížďku do statické metody, která provede všechny kroky nezbytné pro směrování závislé na datech. 
  
  * Používá OpenConnectionForKey volání rozhraní klienta elastické databáze na mapě střepu k navázání otevřeného připojení.
  * Mapa štřepů vytvoří otevřené připojení k oddílu, který drží shardlet pro daný klíč nástružníku.
  * Toto otevřené připojení je předáno zpět do konstruktoru základní třídy DbContext, který označuje, že toto připojení má být použito EF namísto toho, aby EF automaticky vytvořil nové připojení. Tímto způsobem bylo připojení označeno rozhraním API klienta elastické databáze, aby mohlo zaručit konzistenci v rámci operací správy mapy skřítek.

Použijte nový konstruktor pro podtřídu DbContext namísto výchozího konstruktoru v kódu. Zde naleznete příklad: 

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

Nový konstruktor otevře připojení k oddílu, který obsahuje data pro shardlet identifikované hodnotou **tenantid1**. Kód v **using** bloku zůstane beze změny pro přístup **k DbSet** pro blogy pomocí EF na střepu pro **tenantid1**. To změní sémantiku pro kód v using bloku tak, aby všechny databázové operace jsou nyní vymezeny na jeden oddíl, kde **tenantid1** je uložena. Například dotaz LINQ přes blogy **DbSet** by pouze vrátit blogy uložené na aktuální úlomku, ale ne ty uložené na jiných šerpů.  

#### <a name="transient-faults-handling"></a>Přechodná manipulace s chybami

Tým Microsoft Patterns & Practices publikoval [blok aplikace pro zpracování přechodných chyb](https://msdn.microsoft.com/library/dn440719.aspx). Knihovna se používá s elastickou škálou klientské knihovny v kombinaci s EF. Ujistěte se však, že všechny přechodné výjimky vrátí na místo, kde můžete zajistit, že nový konstruktor je používán po přechodné poruchy tak, aby každý nový pokus o připojení se provádí pomocí konstruktory, které jste vylepšili. V opačném případě není zaručeno připojení ke správnému oddílu a neexistují žádné záruky, že připojení je zachováno, protože dojde ke změnám mapy oddílu. 

Následující ukázka kódu ukazuje, jak lze použít zásadu opakování SQL kolem nových konstruktorů **podtřídy DbContext:** 

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

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací doby mezi opakování. Tento přístup je podobný pokyny pro EF a transakce iniciované uživatelem (viz [Omezení s opakování mnoství strategie provádění (EF6 a dále)](https://msdn.microsoft.com/data/dn307226). Obě situace vyžadují, aby aplikační program řídí obor, do kterého se vrací přechodná výjimka: znovu otevřít transakci nebo (jak je znázorněno) znovu vytvořit kontext z správného konstruktoru, který používá klientskou knihovnu elastické databáze.

Potřeba řídit, kde přechodné výjimky nás zpět v oboru také vylučuje použití vestavěné **SqlAzureExecutionStrategy,** který je dodáván s EF. **SqlAzureExecutionStrategy** by znovu otevřít připojení, ale nepoužívat **OpenConnectionForKey** a proto obejít všechny ověření, které se provádí jako součást **OpenConnectionForKey** volání. Místo toho ukázka kódu používá předdefinované **DefaultExecutionStrategy,** který také přichází s EF. Na rozdíl od **SqlAzureExecutionStrategy**, funguje správně v kombinaci s zásady opakování z přechodné zpracování chyb. Zásady spuštění jsou nastaveny ve třídě **ElasticScaleDbConfiguration.** Všimněte si, že jsme se rozhodli nepoužívat **DefaultSqlExecutionStrategy** protože navrhuje použití **SqlAzureExecutionStrategy** pokud přechodné výjimky dojít - což by vedlo k nesprávné chování, jak je popsáno. Další informace o různých zásadách opakování a EF naleznete [v tématu odolnost proti chybám připojení v EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Přepíše konstruktor

Příklady kódu výše ilustrují výchozí konstruktor re-zápisy požadované pro vaši aplikaci, aby bylo možné použít směrování závislé na datech s entity framework. Následující tabulka zobecnizuje tento přístup k ostatním konstruktorům. 

| Aktuální konstruktor | Přepsaný konstruktor pro data | Základní konstruktor | Poznámky |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |Připojení musí být funkcí mapy střepů a směrovacího klíče závislého na datech. Je třeba obejít automatické vytvoření připojení pomocí EF a místo toho použít mapu střepu k zprostředkování připojení. |
| MyContext(řetězec) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |Připojení je funkcí mapy střepů a směrovacího klíče závislého na datech. Pevný název databáze nebo připojovací řetězec nefunguje, protože obcházejí ověření mapou svižných bitových oddílů. |
| MyContext(dbcompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Připojení získá vytvořen pro danou mapu šikmu a klíče šněrování s modelem k dispozici. Zkompilovaný model je předán do základní c'tor. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |Připojení je třeba odvodit z mapy oddílu a klíče. Nelze jej poskytnout jako vstup (pokud tento vstup již používal mapu síta a klíč). Logická hodnota je předána. |
| MyContext(řetězec, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |Připojení je třeba odvodit z mapy oddílu a klíče. Nelze poskytnout jako vstup (pokud tento vstup byl pomocí mapy střežiště a klíč). Zkompilovaný model je předán. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |Nový konstruktor musí zajistit, že jakékoli připojení v ObjectContext předané jako vstup je přesměrován na připojení spravované elastické škálování. Podrobná diskuse ObjectContexts je nad rámec tohoto dokumentu. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |Připojení je třeba odvodit z mapy oddílu a klíče. Připojení nelze poskytnout jako vstup (pokud tento vstup již používal mapu sršibek a klíč). Model a logická hodnota jsou předány konstruktoru základní třídy. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Nasazení schématu oddílu prostřednictvím migrace EF

Automatická správa schématu je pohodlí poskytované entity framework. V kontextu aplikací pomocí elastických databázových nástrojů chcete zachovat tuto možnost automaticky zřídit schéma nově vytvořené oddíly, když jsou databáze přidány do rozdělené aplikace. Primární případ použití je zvýšení kapacity na datové vrstvě pro vrstvy s oddíly pomocí EF. Spoléhání se na možnosti EF pro správu schématu snižuje úsilí správy databáze s oddílové aplikace postavené na EF. 

Nasazení schématu prostřednictvím migrace EF funguje nejlépe na **neotevřených připojeních**. To je na rozdíl od scénáře pro směrování závislé na datech, který závisí na otevřené připojení poskytované rozhraní API klienta elastické databáze. Dalším rozdílem je požadavek na konzistenci: I když je žádoucí zajistit konzistenci pro všechna připojení směrování závislá na datech k ochraně proti souběžné manipulaci mapy sutých bitových kódů, není problém s počátečním nasazením schématu do nové databáze, která dosud nebyla zaregistrována v mapě střepů a ještě nebyla přidělena pro uložení shardlets. Proto můžete spoléhat na pravidelné připojení databáze pro tento scénář, na rozdíl od směrování závislé na datech.  

To vede k přístupu, kde je nasazení schématu prostřednictvím migrace EF úzce spojena s registrací nové databáze jako úlomek v mapě střepů aplikace. To závisí na následujících předpokladech: 

* Databáze již byla vytvořena. 
* Databáze je prázdná - neobsahuje žádné uživatelské schéma a žádná uživatelská data.
* K databázi zatím nelze získat přístup prostřednictvím klientských api klienta elastické databáze pro směrování závislé na datech. 

S těmito požadavky na místě můžete vytvořit pravidelné un-opened **SqlConnection** zahájit migrace EF pro nasazení schématu. Následující ukázka kódu ilustruje tento přístup. 

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

Tato ukázka ukazuje metodu **RegisterNewShard,** která registruje oddíl šiřidla v mapě šiřidla, nasazuje schéma prostřednictvím migrace EF a ukládá mapování klíče pro říťna na oddíl. Spoléhá na konstruktor u podtřídy **DbContext** **(ElasticScaleContext** v ukázce), který přebírá připojovací řetězec SQL jako vstup. Kód tohoto konstruktoru je přímočarý, jak ukazuje následující příklad: 

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

Jeden mohl použít verzi konstruktoru zděděné ze základní třídy. Ale kód musí zajistit, že výchozí inicializátor pro EF se používá při připojování. Proto krátká objížďka do statické metody před voláním do konstruktoru základní třídy s připojovacím řetězcem. Všimněte si, že registrace šdrupy by měla běžet v jiné doméně nebo procesu aplikace, aby bylo zajištěno, že nastavení inicializátoru pro EF není v konfliktu. 

## <a name="limitations"></a>Omezení

Přístupy uvedené v tomto dokumentu s sebou nesou několik omezení: 

* EF aplikace, které používají **LocalDb** nejprve je třeba migrovat do běžné databáze serveru SQL Server před použitím knihovny klienta elastické databáze. Horizontální navýšení kapacity aplikace pomocí elastického měřítka není možné s **LocalDb**. Všimněte si, že vývoj může stále používat **LocalDb**. 
* Všechny změny v aplikaci, které implikují změny schématu databáze je třeba projít migrace EF na všechny oddíly. Ukázkový kód pro tento dokument neukazuje, jak to provést. Zvažte použití Update-Database s Parametr ConnectionString iterát přes všechny šiřidla; nebo extrahujte skript T-SQL pro čekající migraci pomocí aktualizace databáze s volbou -Script a aplikujte skript T-SQL na vaše úlomky.  
* Daný požadavek, předpokládá se, že všechny jeho zpracování databáze je obsažena v rámci jednoho oddílu diagramu, jak je identifikován klíč sharding poskytované požadavek. Tento předpoklad však není vždy pravdivý. Například pokud není možné zpřístupnit klíč s ráždí. Chcete-li tento problém vyřešit, klientská knihovna poskytuje **Třídu MultiShardQuery,** která implementuje abstrakce připojení pro dotazování přes několik oddílů. Naučit se používat **MultiShardQuery** v kombinaci s EF je nad rámec tohoto dokumentu

## <a name="conclusion"></a>Závěr

Prostřednictvím kroků popsaných v tomto dokumentu mohou aplikace EF použít možnost knihovny klienta elastické databáze pro směrování závislé na datech refaktoringem konstruktorů podtříd **DbContext** používaných v aplikaci EF. To omezuje změny požadované pro místa, kde **dbContext** třídy již existují. Kromě toho ef aplikace mohou i nadále využívat automatické nasazení schématu kombinací kroků, které vyvolávají nezbytné migrace EF s registrací nových oddílů a mapování v mapě schrovacího procesu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
