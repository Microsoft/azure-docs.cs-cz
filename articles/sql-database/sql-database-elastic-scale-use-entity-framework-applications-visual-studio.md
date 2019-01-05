---
title: Použití Klientská knihovna elastic database s Entity Framework | Dokumentace Microsoftu
description: Použít Klientská knihovna elastické databáze a Entity Framework pro kódování databází
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
ms.date: 04/01/2018
ms.openlocfilehash: 71f024c81983fcb9c3e99bdf633a5bde306452b8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051233"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Klientská knihovna elastic Database s Entity Framework
Tento dokument ukazuje změny v aplikaci Entity Framework, které jsou potřebné k integraci s [nástrojů Elastic Database](sql-database-elastic-scale-introduction.md). Zaměřuje se na vytváření [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md) a [směrování závislé na datech](sql-database-elastic-scale-data-dependent-routing.md) s rozhraním Entity Framework **Code First** přístup. [Code First – nová databáze](https://msdn.microsoft.com/data/jj193542.aspx) EF v tomto kurzu slouží jako příklad v tomto dokumentu. Vzorový kód doprovodném tohoto dokumentu je součástí nástrojů elastic database nastavit vzorků v kódu ukázky sady Visual Studio.

## <a name="downloading-and-running-the-sample-code"></a>Stažení a spuštění vzorového kódu
Stažení kódu pro účely tohoto článku:

* Vyžaduje se sada Visual Studio 2012 nebo novější. 
* Stáhněte si [elastické databáze nástroje pro Azure SQL – ukázka integrace Entity Frameworku](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) z webu MSDN. Rozbalte ukázku do umístění podle vašeho výběru.
* Spusťte Visual Studio. 
* V sadě Visual Studio vyberte soubor -> Otevřít projekt či řešení. 
* V **otevřít projekt** dialogové okno, přejděte k ukázce jste si stáhli a vyberte **EntityFrameworkCodeFirst.sln** otevřete ukázku. 

Ke spuštění ukázky, je potřeba vytvořit tři prázdné databáze ve službě Azure SQL Database:

* Databáze správce mapování horizontálních oddílů
* Databáze horizontálního oddílu 1
* Databáze horizontálních oddílů 2

Jakmile vytvoříte tyto databáze, zadejte místo zástupné znaky v **Program.cs** s názvem vašeho serveru Azure SQL DB, názvy databáze a přihlašovací údaje pro připojení k databázím. Sestavte řešení v sadě Visual Studio. Visual Studio stáhne požadované balíčky NuGet pro klientská knihovna elastic database, Entity Framework a jako součást procesu sestavení zpracování přechodných chyb. Ujistěte se, že je povoleno obnovení balíčků NuGet pro řešení. Toto nastavení můžete povolit kliknutím pravým tlačítkem na soubor řešení v Průzkumníku řešení sady Visual Studio. 

## <a name="entity-framework-workflows"></a>Pracovní postupy Entity Framework
Entity Framework vývojáři Spolehněte se na jednu z následujících čtyř pracovní postupy pro vytváření aplikací a k zajištění trvalosti pro objekty aplikací: 

* **Code First (nová databáze)**: EF vývojářem modelu v kódu aplikace a poté vygeneruje EF databáze z něj. 
* **Code First (existující databázi)**: Vývojář umožňuje EF, generování kódu aplikace pro model z existující databáze.
* **Model první**: Vývojář vytvoří model v EF designeru a pak EF vytvoří databáze z modelu.
* **Databáze první**: Vývojář používá EF nástrojů k odvození modelu z existující databáze. 

Všechny tyto přístupy využívají třídy DbContext transparentně spravovat připojení k databázi a schéma databáze pro aplikaci. Různé konstruktory základní třídy DbContext povolit pro různé úrovně kontroly nad vytvoření připojení a spuštění databáze a také vytváří schématu. Problémy jsou vyvolány primárně skutečnost, že správa připojení databáze poskytovaná v EF protíná díky možnostem připojení správy závislé na datech směrování rozhraní, které jsou k dispozici ve Klientská knihovna elastic database. 

## <a name="elastic-database-tools-assumptions"></a>Předpoklady nástroje pro elastické databáze
Definice termínu, naleznete v tématu [Glosář nástrojů elastické databáze](sql-database-elastic-scale-glossary.md).

Oddíly dat ve vašich aplikacích nazývá shardletů lze definovat s Klientská knihovna elastic database. Shardletů se identifikují podle klíče horizontálního dělení a jsou namapované na konkrétní databáze. Aplikace může mít libovolný počet databází, podle potřeby a distribuovat shardletů poskytnout dostatek kapacity nebo výkonu, které jsou uvedené aktuální obchodní požadavky. Mapování hodnot klíče horizontálního dělení do databází se ukládá pomocí mapy horizontálních oddílů, poskytuje elastic database klientských rozhraní API. Tato funkce je volána **správy mapování horizontálních oddílů**, nebo SMM pro krátké. Mapy horizontálních oddílů slouží také jako zprostředkovatel připojení k databázi pro požadavky, které mají klíč horizontálního dělení. Tato schopnost je známá jako **směrování závislé na datech**. 

Správce mapování horizontálních oddílů uživatelé chrání před konzistentní zobrazení do shardletu data, která může dojít, když se dějí operace správy souběžných shardletu (například přemístění dat v jednom horizontálním oddílu do jiného). Uděláte to tak, mapy horizontálních oddílů spravuje klientské knihovny zprostředkovatele připojení databáze pro aplikaci. Díky tomu funkce mapy horizontálních oddílů při operacích správy horizontálních oddílů může mít vliv na shardletu, který se vytvořil připojení automaticky ukončit připojení k databázi. Tento přístup je potřeba integrovat některých funkcí na EF, jako je například vytváření nových připojení z existující k provedení kontroly existence databáze. Obecně platí naše zjišťování bylo, že fungují spolehlivě pro připojení k zavřené databázi, které lze bezpečně klonovat pro EF pouze fungují standardní DbContext konstruktory. Princip návrhu elastické databáze místo toho je jenom zprostředkovat otevřené připojení. Jeden možná myslíte, že problém může vyřešit ukončování připojení zprostředkovaných knihovnou klienta před předání do EF uvolněn objekt DbContext. Ale tak, že Probíhá ukončování připojení a spoléhání se na EF ho znovu otevřít, jeden foregoes provést knihovnou kontroly ověřování a konzistence. Funkce migrace v EF, ale používá tato připojení ke správě základní schéma databáze tak, aby je transparentní pro aplikaci. V ideálním případě by se zachovat a kombinovat všechny tyto možnosti z Klientská knihovna elastic database a EF ve stejné aplikaci. Následující část popisuje tyto vlastnosti a požadavky podrobněji. 

## <a name="requirements"></a>Požadavky
Při práci s Klientská knihovna elastic database a rozhraní API Entity Framework, budete chtít zachovat následující vlastnosti: 

* **Horizontální navýšení kapacity**: Přidání nebo odebrání databází v horizontálně dělené aplikace podle potřeby pro požadavky kapacity aplikace datové vrstvy. To znamená, že ovládací prvek za vytvoření a odstranění databází a pomocí Správce mapování horizontálních oddílů elastické databáze rozhraní API pro správu databází a mapování shardletů. 
* **Konzistence**: Aplikace využívá horizontálního dělení a využívá možnosti směrování závislé na datech klientské knihovny. Pokud chcete vyhnout poškození nebo nesprávné výsledků, jsou zprostředkovaných připojení prostřednictvím Správce mapování horizontálních oddílů. Uchovává také ověření a konzistence.
* **Code First**: Pokud chcete zachovat pohodlí první paradigma společnosti EF kódu. V Code First tříd v aplikaci jsou mapovány transparentně základní struktury databáze. Kód aplikace komunikuje s DbSets, který maskování většinu aspektů, které jsou součástí základní zpracování databáze.
* **Schéma**: Entity Framework zpracovává vytvoření schématu počáteční databáze a vývoj následné schématu prostřednictvím migrace. Přizpůsobení aplikace je snadno tak, že zachová tyto možnosti, jak data vyvíjí. 

Následující pokyny dá pokyn, jak splnit tyto požadavky pro Code First aplikace pomocí nástrojů pro elastické databáze. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Závislé na datech směrování, pomocí EF DbContext
Připojení k databázi pomocí Entity Framework se obvykle spravují prostřednictvím podtřídy třídy **DbContext**. Vytvořit tyto podtřídy třídy odvozené z **DbContext**. Tady můžete definovat vaše **DbSets** které implementují databázi záložních kolekce objektů CLR pro vaši aplikaci. V rámci směrování závislé na datech lze identifikovat několik užitečných vlastností, které nemají nutně další EF code první aplikace scénáře: 

* Databáze již existuje a je zaregistrován v mapě horizontálních oddílů elastické databáze. 
* Schéma aplikace již byla nasazena do databáze (vysvětleno níže). 
* Pomocí mapy horizontálních oddílů jsou zprostředkovaných připojení směrování závislé na datech do databáze. 

K integraci **DbContexts** s směrování závislé na datech pro horizontální navýšení kapacity:

1. Vytvoření připojení fyzická databáze prostřednictvím rozhraní klienta elastické databáze správce mapování horizontálních oddílů 
2. Zabalení připojení **DbContext** podtřídy
3. Předejte připojení **DbContext** základních tříd, aby veškeré zpracování na straně EF se také stane. 

Následující příklad kódu ukazuje tento přístup. (Tento kód je také v průvodní projektu sady Visual Studio)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

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

## <a name="main-points"></a>Hlavní body
* Nový konstruktor nahradí výchozí konstruktor v podtřídy DbContext 
* Nový konstruktor přijímá argumenty, které jsou požadovány pro směrování závislé na datech prostřednictvím Klientská knihovna elastic database:
  
  * mapy horizontálních oddílů pro přístup k rozhraní směrování závislé na datech
  * klíč horizontálního dělení k identifikaci shardletu,
  * připojovací řetězec pomocí přihlašovacích údajů pro připojení směrování závislé na datech do horizontálního oddílu. 
* Volání konstruktoru základní třídy zohledňuje náhradní proces statická metoda, která provádí všechny kroky nezbytné pro směrování závislé na datech. 
  
  * Pomocí OpenConnectionForKey volání z rozhraní klientů elastických databází na mapě horizontálních oddílů zřizuje otevřené připojení.
  * Mapy horizontálních oddílů vytvoří otevření připojení do horizontálního oddílu, který má shardletu pro klíč horizontálního dělení dané.
  * Toto otevřené připojení se předá zpět do konstruktoru základní třídy DbContext, že toto připojení je EF používané místo EF automaticky vytvoří nové připojení. Tímto způsobem připojení označená pomocí rozhraní API klienta elastické databáze tak, aby ho mohli zaručit konzistenci v rámci operace správy mapování horizontálních oddílů.

Pomocí nové konstruktoru podtřídy DbContext namísto výchozího konstruktoru ve vašem kódu. Zde naleznete příklad: 

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

Nový konstruktor otevře připojení k horizontálního oddílu, který obsahuje data pro shardletu identifikovaný hodnotu **tenantid1**. Kód v **pomocí** bloku zůstává beze změny do přístup **DbSet** pro blogy, pomocí EF na horizontální oddíl pro **tenantid1**. Změní sémantiku pro kód v pomocí blokovat tak, že všechny databázové operace jsou nyní vymezeny do jednoho horizontálního oddílu kde **tenantid1** zůstane. Například dotaz LINQ nad na blozích **DbSet** vracel pouze blogy uložené na aktuální horizontálního oddílu, ale ne těch, které jsou uložené na jiných horizontálních oddílů.  

#### <a name="transient-faults-handling"></a>Zpracování přechodných chyb
Tým Microsoft Patterns a postupy publikovaná [The přechodné Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719.aspx). Knihovny se používá v kombinaci s EF klientské knihovny pro elastické škálování. Však zajistěte, že všechny přechodné výjimky vrátí na místo, kde můžete zajistit, že nový konstruktor používá po přechodném selhání tak, aby všechny nový pokus o připojení se provádí pomocí konstruktorů, které jste tweaked. V opačném případě není zaručeno, že připojení ke správné horizontálních oddílů a neexistují žádné záruky, který se zachová připojení při změnách do mapy horizontálních oddílů. 

Následující vzorový kód ukazuje, jak lze kolem nové zásady opakování SQL **DbContext** podtřídy konstruktory: 

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

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací dobu mezi opakovanými pokusy. Tento přístup je podobný pokyny pro EF a uživatelem iniciované operace (viz [omezení strategie provádění opakováním (ef6 nebo novější)](https://msdn.microsoft.com/data/dn307226). Obě situace vyžadují, aby program aplikace určuje obor, ke které se vrátí přechodnou výjimkou: znovu otevřít transakce, nebo (jak jsme ukázali) znovu vytvořit kontext ze správné konstruktor, který používá Klientská knihovna elastic database.

Potřeba řídit, kdy přechodným výjimkám převzít nám zpět v oboru také nemůžete použít předdefinované **SqlAzureExecutionStrategy** , který je součástí EF. **SqlAzureExecutionStrategy** by znovu otevřít připojení, ale nepoužívá **OpenConnectionForKey** a tedy obejít ověření se provádí jako součást **OpenConnectionForKey**volání. Místo toho vzorový kód používá předdefinované **DefaultExecutionStrategy** , která se také dodává s EF. Nikoli **SqlAzureExecutionStrategy**, funguje správně v kombinaci s zásady opakování z zpracování přechodných chyb. Zásady spouštění v nastavena **ElasticScaleDbConfiguration** třídy. Všimněte si, že jsme se rozhodli nepoužívat **DefaultSqlExecutionStrategy** od navrhuje použití **SqlAzureExecutionStrategy** Pokud dojde k přechodným výjimkám – což by mohlo dojít k nesprávné chování jak je popsáno. Další informace o různých opakování zásady a EF, naleznete v tématu [odolnost připojení EF](https://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor přepisů
Výše uvedené příklady kódu ukazují výchozí konstruktor přepíše potřebné pro vaši aplikaci k použití s rozhraním Entity Framework směrování závislé na datech. V následující tabulce zobecňuje tento přístup k další konstruktory. 

| Aktuální konstruktor | Přepsaný konstruktor pro data | Konstruktor základní třídy | Poznámky |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, logická hodnota) |Připojení musí být funkce mapy horizontálních oddílů a klíč směrování závislé na datech. Budete si muset obejít vytvoření automatického připojení k v EF a místo toho použít mapy horizontálních oddílů pro zprostředkování připojení. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, logická hodnota) |Připojení je funkce mapy horizontálních oddílů a klíč směrování závislé na datech. Pevné databázové název nebo připojovací řetězec nebude fungovat, jakmile obejít ověřením mapy horizontálních oddílů. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection DbCompiledModel, logická hodnota) |Připojení se vytvoří pro klíč horizontálního oddílu dané mapy a horizontálním dělení se model, který poskytuje. Zkompilovaný model je předána základní c'tor. |
| MyContext (DbConnection, logická hodnota) |ElasticScaleContext (ShardMap TKey, logická hodnota) |DbContext (DbConnection, logická hodnota) |Připojení musí být odvozen z mapy horizontálního oddílu a klíč. Nemůže být zadaný jako vstup (Pokud tento vstup byl již pomocí mapy horizontálních oddílů a klíč). Je předána datový typ Boolean. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection DbCompiledModel, logická hodnota) |Připojení musí být odvozen z mapy horizontálního oddílu a klíč. Nemůže být zadaný jako vstup (Pokud tento vstup byl pomocí mapy horizontálních oddílů a klíč). Zkompilovaný model je předána. |
| MyContext (ObjectContext, logická hodnota) |ElasticScaleContext (ShardMap TKey, ObjectContext, bool) |DbContext (ObjectContext, logická hodnota) |Nový konstruktor musí Ujistěte se, že jakékoli připojení ve třídě ObjectContext předané jako vstup přesměrovány na připojení spravuje elastické škálování. Podrobnou diskuzi o ObjectContexts je nad rámec tohoto dokumentu. |
| MyContext (DbConnection DbCompiledModel, logická hodnota) |ElasticScaleContext (ShardMap TKey, DbCompiledModel, bool) |DbContext (DbConnection DbCompiledModel, bool); |Připojení musí být odvozen z mapy horizontálního oddílu a klíč. Připojení nejde zadat jako vstup (Pokud tento vstup byl již pomocí mapy horizontálních oddílů a klíč). Model a logickou hodnotu jsou předány do konstruktoru základní třídy. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Nasazení schématu horizontálních oddílů pomocí migrace EF
Správa automatického schématu je pohodlné poskytované rozhraním Entity Framework. V rámci aplikace pomocí nástrojů pro elastické databáze budete chtít zachovat tuto možnost při přidávání databází do horizontálně dělené aplikace jsou automaticky zřízena schéma do nově vytvořeného horizontálních oddílů. Primárním případem použití je zvýšit kapacitu datové vrstvy pro horizontálně dělené aplikace pomocí EF. S horizontálně dělené aplikace založená na EF spoléhat na EF v možnosti Správa schématu snižuje úsilí správu databáze. 

Nasazení schématu prostřednictvím migrace EF funguje nejlépe na **neotevřených připojení**. To se liší od scénář směrování závislé na datech, která závisí na otevřené připojení poskytuje rozhraní API klienta elastické databáze. Další rozdíl je požadavek konzistence: Při žádoucí, aby byla zaručena konzistence pro všechny závislé na datech směrování připojení k ochraně proti manipulaci mapy horizontálních oddílů souběžných není žádný problém s počátečním schématem nasazení do nové databáze, který má ještě není registrován v mapě horizontálních oddílů a ještě byl přidělen k uložení shardletů. Proto se můžete spolehnout na standardní databázi připojení pro tento scénář, na rozdíl od směrování závislé na datech.  

To vede k přístupu, kde nasazení schématu prostřednictvím migrace EF je úzce párována registrace nové databáze jako horizontálních oddílů v mapě horizontálních oddílů aplikace. To závisí na následujících požadavků: 

* Databáze již byla vytvořena. 
* Databáze je prázdná – obsahuje žádné schéma uživatele a žádná uživatelská data.
* Databáze ještě přístupné prostřednictvím rozhraní API klienta elastické databáze pro směrování závislé na datech. 

Splněny tyto požadavky, můžete vytvořit běžný zrušení otevřené **SqlConnection** aktivovala migrace EF pro nasazení schématu. Tento postup znázorňuje následující ukázka kódu. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

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


Tento příklad ukazuje metodu **RegisterNewShard** , který registruje horizontální oddíl do mapy horizontálních oddílů, nasadí schématu pomocí migrace EF a ukládá mapování klíč horizontálního dělení do horizontálního oddílu. Spoléhá na konstruktor třídy **DbContext** podtřídy (**ElasticScaleContext** v ukázce), který přebírá připojovací řetězec SQL jako vstup. Kód tento konstruktor je přímočaré jako v následujícím příkladu: 

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

Jeden možná použili verze konstruktoru zděděné ze základní třídy. Ale kód je potřeba zajistit, aby používal výchozí inicializátor pro EF při připojování. Proto krátké předání do statické metody před voláním do konstruktoru základní třídy s připojovacím řetězcem. Všimněte si, že registrace horizontálních oddílů má běžet v jiné domény aplikace nebo proces, který zajišťuje, že nastavení inicializátor pro EF není v konfliktu. 

## <a name="limitations"></a>Omezení
Přístupů popsaných v tomto dokumentu zahrnuje několik omezení: 

* EF aplikace, které používají **LocalDb** nejdřív budete muset migrovat databázi regulární systému SQL Server před použitím Klientská knihovna elastic database. Horizontální navýšení kapacity aplikace prostřednictvím horizontálního dělení s pružným Škálováním není možné s **LocalDb**. Všimněte si, že vývoje můžete dál používat **LocalDb**. 
* Žádné změny aplikace, které znamenají změny schématu databáze potřebovat migrace EF a absolvovat na všechny horizontální oddíly. Ukázkový kód pro tento dokument není ukazují, jak to provést. Zvažte použití aktualizace databáze s parametrem připojovací řetězec k iteraci přes všechny horizontální oddíly; ani je nemohl extrahovat skriptu T-SQL pro migraci čekající na vyřízení pomocí Update-Database-skript s možností a použít skript T-SQL pro vaše horizontálních oddílů.  
* Zadaný požadavek, předpokládá se, že všechny její zpracování databáze je součástí jeden horizontální oddíl pomocí klíče horizontálního dělení zadané v požadavku. Ale tento předpoklad vždy neobsahuje hodnotu true. Například, pokud není možné zpřístupnit klíč horizontálního dělení. Z toho Klientská knihovna poskytuje **MultiShardQuery** třídu, která implementuje abstraktní připojení pro dotazování více horizontálních oddílů. Naučte se používat **MultiShardQuery** v kombinaci s EF je nad rámec tohoto dokumentu

## <a name="conclusion"></a>Závěr
Pomocí kroků uvedených v tomto dokumentu, EF aplikace mohly používat funkce Klientská knihovna elastic database směrování závislé na datech refaktoringem konstruktory **DbContext** používaných v aplikaci EF podtřídy. To omezuje změny nutné z těchto míst kde **DbContext** třídy již existují. Aplikace EF kromě toho můžete i nadále využívat schématu automatického nasazení díky kombinaci kroků, které vyvolávají potřebné migrace EF s registrací nových horizontálních oddílů a mapování horizontálních oddílů. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
