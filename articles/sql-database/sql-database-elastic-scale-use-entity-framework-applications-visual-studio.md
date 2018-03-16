---
title: "Klientská knihovna pro elastické databáze pomocí rozhraní Entity Framework | Microsoft Docs"
description: "Pomocí klientské knihovny pro elastické databáze a Entity Framework pro kódování databází"
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 03/06/2017
ms.author: sstein
ms.openlocfilehash: 5f215c6c6f65804785e35ae1b3ec9cce24e2a976
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Klientská knihovna pro elastické databáze s platformou Entity Framework
Tento dokument ukazuje změny v aplikaci rozhraní Entity Framework, která jsou potřebné k integraci s [nástroje elastické databáze](sql-database-elastic-scale-introduction.md). Zaměřuje se na skládání [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md) a [závislé na data směrování](sql-database-elastic-scale-data-dependent-routing.md) s platformou Entity Framework **Code First** přístup. [Code nejprve - novou databázi](http://msdn.microsoft.com/data/jj193542.aspx) kurz pro EF slouží jako příklad spuštěné v tomto dokumentu. Ukázkový kód doplňujícími tento dokument je součástí nástroje elastické databáze sada ukázky ve Visual Studio ukázky kódu.

## <a name="downloading-and-running-the-sample-code"></a>Stažení a spuštění ukázkového kódu
Chcete-li stáhnout kód v tomto článku:

* Visual Studio 2012 nebo novější je povinný. 
* Stažení [elastické databáze nástroje pro Azure SQL – ukázka integrace Entity Framework](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-bae904ba) z webu MSDN. Rozbalte vzorku, který se umístění vašeho výběru.
* Spusťte Visual Studio. 
* V sadě Visual Studio vyberte soubor -> Otevřít projekt nebo řešení. 
* V **otevřít projekt** dialogové okno, přejděte k ukázkové jste stáhli a vyberte **EntityFrameworkCodeFirst.sln** otevřete ukázku. 

Ke spuštění ukázky, budete muset vytvořit tři prázdné databáze ve službě Azure SQL Database:

* Horizontálního oddílu mapa správce databáze
* Databáze horizontálního oddílu 1
* Databáze horizontálního oddílu 2

Po vytvoření těchto databází, vyplňte zástupného v **Program.cs** s název serveru Azure SQL DB, názvy databáze a pověření pro připojení k databázím. Sestavte řešení v sadě Visual Studio. Visual Studio stáhne požadované balíčky NuGet pro klientské knihovny elastické databáze Entity Framework a přechodná chyba zpracování v rámci procesu sestavení. Ujistěte se, že probíhá obnovení balíčků NuGet je povolena pro vaše řešení. Toto nastavení můžete povolit kliknutím pravým tlačítkem na soubor řešení v Průzkumníku řešení Visual Studio. 

## <a name="entity-framework-workflows"></a>Pracovní postupy Entity Framework
Vývojáři Entity Framework využívají následující čtyři pracovních postupů, chcete-li vytvářet aplikace a zajistit trvalosti pro objekty aplikací: 

* **Code First (nová databáze)**: EF vývojáře vytvoří model v kódu aplikace a pak EF vygeneruje databázi z něj. 
* **Code First (existující databáze)**: vývojář umožňuje EF generování kódu aplikace pro model z existující databáze.
* **Model první**: vývojář vytvoří model v EF designeru a pak EF vytvoří databázi z modelu.
* **Databáze první**: vývojář používá EF tooling odvodit modelu z existující databáze. 

Všechny tyto přístupy spoléhají na třídy DbContext transparentně Správa připojení k databázi a schéma databáze pro aplikaci. Jiné konstruktory na základní třídy DbContext povolit pro různé úrovně kontroly nad vytváření připojení, zavádění databáze a vytváření schématu. Problémy jsou vyvolány především na skutečnost, že správu připojení databáze poskytované EF protíná s závislé na data směrování rozhraní poskytuje možnosti správy připojení pomocí klientské knihovny pro elastické databáze. 

## <a name="elastic-database-tools-assumptions"></a>Předpoklady nástroje elastické databáze
Definice podmínek, najdete v části [Glosář nástroje elastické databáze](sql-database-elastic-scale-glossary.md).

Klientská knihovna pro elastické databáze definovat oddíly názvem shardlets data aplikací. Shardlets jsou identifikovány klíč horizontálního dělení a jsou namapované na konkrétní databáze. Aplikace může mít libovolný počet databází, podle potřeby a distribuovat shardlets zajistit dostatek kapacity nebo výkonu zadána aktuální podnikové požadavky. Mapování hodnot klíče horizontálního dělení k databázím ukládá horizontálního oddílu mapu poskytuje rozhraní API klienta elastické databáze. Tato funkce je volána **horizontálního oddílu mapy správu**, nebo pro zkrácení SMM. Mapování horizontálních slouží taky jako zprostředkovatel připojení databáze pro požadavky, které zajišťují klíč horizontálního dělení. Tato funkce se označuje jako **závislé na data směrování**. 

Mapa správce horizontálního oddílu chrání uživatelé z nekonzistentní zobrazení do shardlet data, která může dojít, když se děje operace správy souběžných shardlet (například přemístění dat z jedné horizontálního oddílu do jiného). Uděláte to tak mapy horizontálního oddílu spravuje zprostředkovatele knihovny klienta připojení databáze pro aplikaci. To umožňuje funkci horizontálního oddílu mapy automaticky ukončit připojení k databázi, pokud operace správy horizontálního oddílu by mohlo mít vliv shardlet, který byl vytvořen pro připojení. Tento přístup je potřeba integrovat některé EF na funkce, jako je například vytváření nových připojení z existující Zkontrolovat existenci databáze. Obecně platí naše pozorování bylo, že standardní konstruktory DbContext pouze pracovní spolehlivě pro uzavřené databázových připojení, která se dají bezpečně klonovat pro EF fungovat. Princip návrhu elastické databáze místo toho je pouze zprostředkovatel otevřené připojení. Může být jeden vezměte v úvahu uzavřením připojení pomocí klientské knihovny pro zprostředkované před předání do EF DbContext může vyřešit tento problém. Však Probíhá ukončování připojení a spoléhat na EF k ho znovu otevřít, jeden foregoes kontroly ověřování a konzistence provádí knihovny. Funkce migrace ve EF, ale používá tato připojení ke správě základní schéma databáze tak, že je transparentní pro aplikace. V ideálním případě by se zachovat a kombinovat všechny tyto možnosti z klientské knihovny pro elastické databáze a EF ve stejné aplikaci. Následující část popisuje tyto vlastnosti a požadavky podrobněji. 

## <a name="requirements"></a>Požadavky
Při práci s klientské knihovny pro elastické databáze a Entity Framework rozhraní API, které chcete uchovávat následující vlastnosti: 

* **Škálováním na více systémů**: můžete přidat nebo odebrat databáze z datové vrstvy horizontálně dělené aplikace v případě potřeby u požadavky kapacity aplikace. To znamená kontrolu nad vytváření a odstraňování databáze a pomocí správce mapy horizontálního oddílu elastické databáze rozhraní API pro správu databází a mapování shardlets. 
* **Konzistence**: aplikace používá horizontálního dělení a používá možnosti směrování dat závislé klientské knihovny. Připojení se pokud chcete vyhnout poškození nebo výsledky dotazu nesprávný, zprostředkované prostřednictvím správce mapy horizontálního oddílu. Zachová také ověření a konzistence.
* **Code First**: zachování pohodlím, které představuje první zlepší EF na kódu. Code First třídy v aplikaci se mapují transparentně základní struktury databáze. Kód aplikace komunikuje s DbSets, který maskování většinu aspektů základní zpracování databáze.
* **Schéma**: rozhraní Entity Framework zpracovává vytvoření schématu počáteční databáze a následné schématu vývoj pomocí migrace. Přizpůsobení aplikace je snadné zachováním tyto možnosti, jak zpracovaní data. 

Následující pokyny dá pokyn, jak splnit tyto požadavky pro Code First aplikací pomocí nástroje elastické databáze. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Data závislé na směrování pomocí EF DbContext
Databázová připojení s platformou Entity Framework se obvykle spravují prostřednictvím měly podtřídy **DbContext**. Vytvořit tyto podtřídy odvozené z **DbContext**. Toto je, kde můžete definovat vaše **DbSets** které implementují databáze zálohována kolekce objektů CLR pro vaši aplikaci. V kontextu směrování závislé na data můžete identifikovat několik užitečné vlastnosti, které nemají nutně další EF code první aplikaci scénáře: 

* Databáze již existuje a je zaregistrován v mapě horizontálního oddílu elastické databáze. 
* Schéma aplikace již byla nasazena do databáze (vysvětlení níže). 
* Jsou závislé na data směrování připojení k databázi zprostředkované pomocí mapy horizontálního oddílu. 

K integraci **DbContexts** s závislé na data směrování pro Škálováním na více systémů:

1. Vytvořit fyzická databáze připojení prostřednictvím rozhraní klienta elastické databáze správce mapy horizontálního oddílu, 
2. Zabalení připojení pomocí **DbContext** podtřídy
3. Předat připojení do **DbContext** základní třídy, aby veškeré zpracování na straně EF se také stane. 

Následující příklad kódu ukazuje tento přístup. (Tento kód se taky v doprovodné projektu sady Visual Studio)

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
* Nový konstruktor nahradí výchozí konstruktor v DbContext podtřídy 
* Nový konstruktor přijímá argumenty, které jsou požadovány pro závislé na data směrování pomocí klientské knihovny pro elastické databáze:
  
  * mapování horizontálních pro přístup k rozhraní směrování závislé na data
  * klíč horizontálního dělení k identifikaci shardlet,
  * připojovací řetězec s přihlašovacími údaji pro závislé na data směrování připojení k horizontálního oddílu. 
* Volání konstruktoru základní třídy zohledňuje detour statickou metodu, která provádí všechny kroky potřebné pro směrování závislé na data. 
  
  * Volání OpenConnectionForKey rozhraní klienta elastické databáze na mapě horizontálního oddílu používá k navázání otevřené připojení.
  * Mapování horizontálních vytvoří otevřít připojení k horizontálního oddílu, který obsahuje shardlet pro danou horizontálního dělení klíč.
  * Toto otevřené připojení se předá zpět do konstruktoru základní třídy DbContext k označení, že je toto připojení má být používána EF místo EF automaticky vytvořit nové připojení. Tímto způsobem připojení má byla označená klientem elastické databáze rozhraní API, tak, aby ho může zaručit konzistenci v rámci operace správy mapy horizontálního oddílu.

Pomocí konstruktoru new podtřídy DbContext místo výchozí konstruktor v kódu. Zde naleznete příklad: 

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

Nový konstruktor otevře připojení k horizontálního oddílu, který obsahuje data pro shardlet identifikovaný hodnotu **tenantid1**. Kód v **pomocí** bloku zůstává beze změny, abyste měli přístup **DbSet** pro blogy pomocí EF na horizontálního oddílu pro **tenantid1**. Tato operace změní sémantiku pro kód v pomocí blokovat tak, že všechny databázové operace jsou nyní vymezeny na jednu horizontálního oddílu kde **tenantid1** je uložen. Například dotaz LINQ přes na blozích **DbSet** vrátí pouze uložené na aktuální horizontálního oddílu blogy, ale není těm, které jsou uložené na jiné horizontálních oddílů.  

#### <a name="transient-faults-handling"></a>Zpracování přechodné chyby
Tým postupy společnosti Microsoft Patterns publikována [The přechodné chyby zpracování aplikace bloku](https://msdn.microsoft.com/library/dn440719.aspx). Knihovny se používá v kombinaci s EF Klientská knihovna pro elastické škálování. Však zajistěte, že všechny přechodný výjimka vrátí na místo, kde můžete zajistit, že nový konstruktor je použit po přechodná chyba tak, aby všechny nový pokus o připojení se provádí pomocí konstruktorů, které jste tweaked. Jinak hodnota není zaručena připojení ke správné horizontálního oddílu, a neexistují žádné záruky, které jsou prováděny změny mapy horizontálního oddílu se zachová připojení. 

Následující příklad kódu ukazuje použití zásady opakování SQL kolem nové **DbContext** podtřídami konstruktory: 

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

**SqlDatabaseUtils.SqlRetryPolicy** ve výše uvedeném kódu je definován jako **SqlDatabaseTransientErrorDetectionStrategy** s počtem opakování 10 a 5 sekund čekací dobu mezi opakovanými pokusy. Tento přístup je podobná pokyny pro EF a uživatel spustil transakce (viz [omezení s opakováním strategie provádění (EF6 a vyšší)](http://msdn.microsoft.com/data/dn307226). Obě situace vyžadují, aplikace programu určí obor, ke které se vrátí přechodný výjimka: Otevřete transakce, nebo (jak je znázorněno) znovu vytvořte kontext z správné konstruktor, který používá klientské knihovny pro elastické databáze.

Potřeba řídit, kde přechodné výjimky trvat nám zpět v oboru také neumožňuje použití předdefinované **SqlAzureExecutionStrategy** dodávaný s EF. **SqlAzureExecutionStrategy** by znovu otevřít připojení, ale nechcete použít **OpenConnectionForKey** a proto všechny ověření, které se provádí v rámci obejít **OpenConnectionForKey**volání. Místo toho ukázkový kód používá integrované **DefaultExecutionStrategy** také dodávaný s EF. Naproti tomu **SqlAzureExecutionStrategy**, funguje správně v kombinaci s zásady opakování z přechodných chyb. Zásada spouštění nastavena v **ElasticScaleDbConfiguration** třídy. Všimněte si, že jsme se rozhodli nepoužívat **DefaultSqlExecutionStrategy** vzhledem k tomu, že ho navrhuje pomocí **SqlAzureExecutionStrategy** Pokud dojde k přechodné výjimky - popsané, což by způsobit nesprávné chování. Další informace o různých opakování zásady a EF najdete v tématu [odolnost připojení v EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Konstruktor přepisů
Výše uvedený kód příklady ilustrují, výchozí konstruktor znovu zapíše potřebné pro vaši aplikaci k použití závislé na data směrování s rozhraní Entity Framework. Následující tabulka umožňuje zobecnit tento přístup k jiné konstruktory. 

| Aktuální – konstruktor | Přepsaná konstruktor pro data | Základní – konstruktor | Poznámky |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Připojení musí být funkce mapy horizontálního oddílu a klíč směrování závislé na data. Je třeba se obejít automatické připojení k vytvoření správcem EF a místo toho použijte mapování horizontálních k Zprostředkovatel připojení. |
| MyContext(string) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, bool) |Připojení je funkce mapy horizontálního oddílu a klíč směrování závislé na data. Jakmile jsou nefunguje pevné databázové název nebo připojovací řetězec obejít ověření pomocí mapy horizontálního oddílu. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, logická hodnota) |Připojení se vytvoří pro dané ID horizontálního oddílu mapy a horizontálního dělení klíč s modelem zadat. Kompilované modelu je předán na základní c'tor. |
| MyContext (DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext (DbConnection, bool) |Připojení je potřeba odvodit z mapy horizontálního oddílu a klíč. Jako vstup nemůže být zadaný (Pokud je tento vstup byl již pomocí mapy horizontálního oddílu a klíč). Logickou hodnotu, je předaná na. |
| MyContext(string, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, logická hodnota) |Připojení je potřeba odvodit z mapy horizontálního oddílu a klíč. Jako vstup nemůže být zadaný (Pokud je tento vstup byl pomocí mapy horizontálního oddílu a klíč). Kompilované modelu, je předaná na. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Nový konstruktor musí zajistit, aby jakékoli připojení ve třídě ObjectContext předat jako vstup znovu směrované na připojení spravuje elastické škálování. Podrobnou diskuzi o ObjectContexts je nad rámec tohoto dokumentu. |
| MyContext (DbConnection, DbCompiledModel, logická hodnota) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext (DbConnection, DbCompiledModel, logická hodnota); |Připojení je potřeba odvodit z mapy horizontálního oddílu a klíč. Připojení nelze zadat jako vstup (Pokud je tento vstup byl již pomocí mapy horizontálního oddílu a klíč). Model a logickou hodnotu jsou předány konstruktor základní třídy. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Nasazení schématu horizontálního oddílu pomocí EF migrace
Správa automatického schématu je pro vaše pohodlí poskytované rozhraní Entity Framework. V souvislosti s aplikací pomocí nástroje elastické databáze které chcete uchovávat tato funkce automaticky zřídit schéma tak, aby nově vytvořený horizontálních oddílů po přidání databází do aplikace horizontálně dělené. Případem primárního použití je zvýšit kapacitu na datové vrstvě pro horizontálně dělenou aplikací s využitím EF. S horizontálně dělené aplikací postavené na EF spoléhat na EF na funkce pro správu schématu sníží úsilí správu databáze. 

Nasazení schématu pomocí migrace EF funguje nejlépe na **nebyla otevřena připojení**. Tím se liší od tento scénář pro směrování závislé na data, které jsou závislé na otevřené připojení poskytnutý klientem elastické databáze rozhraní API. Další rozdíl je požadavek na konzistence: při žádoucí zajistit konzistenci pro všechny závislé na data směrování připojení k ochraně proti manipulaci mapy souběžných horizontálního oddílu, se nejedná o problém s počátečním nasazení schématu pro nové databáze má ještě není zaregistrována v mapě horizontálního oddílu a ještě není byl přidělen k uchování shardlets. Proto můžete spolehnout na standardní databázi připojení pro tento scénář a závislé na data směrování.  

To vede k přístup, kde nasazení schématu pomocí EF migrace je úzce spojeny s registrací nové databáze jako horizontálního oddílu v mapě horizontálního oddílu aplikace. To závisí na následující požadavky: 

* Databáze již existuje. 
* Databáze je prázdný – drží žádné schéma uživatele a žádná uživatelská data.
* Databázi nelze ještě přistupovat prostřednictvím rozhraní API klienta elastické databáze pro směrování závislé na data. 

Tyto požadavky splněny, můžete vytvořit běžný zrušení otevřenou **SqlConnection** k ji EF migrace pro nasazení schématu. Následující příklad kódu ukazuje tento přístup. 

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


Tento příklad ukazuje metodu **RegisterNewShard** , zaregistruje horizontálního oddílu v mapě horizontálního oddílu, nasadí schéma prostřednictvím EF migrace a ukládá zajišťuje mapování klíč horizontálního dělení do horizontálního oddílu. Přitom spoléhá na konstruktoru objektu **DbContext** podtřídami (**ElasticScaleContext** v ukázce), která má jako vstup připojovací řetězec SQL. Kód tento konstruktor je jednoduché, jak ukazuje následující příklad: 

        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // You want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 

Jeden použili verze konstruktoru zděděn ze základní třídy. Je však nutné zajistit, aby používala výchozí inicializátoru pro EF při připojování kód. Proto krátké obcházejí do statickou metodu před voláním do konstruktoru základní třídy připojovacím řetězcem. Všimněte si, že registrace horizontálních oddílů měly být spuštěny v jiné domény aplikace nebo proces, který zajišťuje, že nastavení inicializátoru EF nejsou v konfliktu. 

## <a name="limitations"></a>Omezení
Přístupy uvedených v tomto dokumentu za následek několik omezení: 

* EF aplikace, které používají **LocalDb** nejprve budete muset migrovat na standardní databázi systému SQL Server před použitím klientské knihovny pro elastické databáze. Horizontální navýšení kapacity aplikace prostřednictvím horizontálního dělení s elastickým Škálováním není možné pomocí **LocalDb**. Všimněte si, že vývoj můžete nadále používat **LocalDb**. 
* Všechny změny do aplikace, které implikují změny schématu databáze muset projít EF migrace na všechny horizontálních oddílů. Ukázkový kód pro tento dokument není ukazují, jak to udělat. Zvažte použití Update-Database s parametrem ConnectionString Iterujte přes všechny horizontálních oddílů; nebo extrahuje skriptu T-SQL pro migraci čekající na vyřízení pomocí Update-Database-skript s možnost a použít skriptu T-SQL pro vaše horizontálních oddílů.  
* Zadaný požadavek, se předpokládá, že určeno klíčem horizontálního dělení poskytované žádost, všechny její zpracování databáze je obsažena v jedné horizontálního oddílu. Však tento předpoklad vždy nemá hodnotu true. Například když není možné zpřístupnit klíč horizontálního dělení. Chcete-li vyřešit tím, poskytuje knihovna klienta **MultiShardQuery** třídu, která implementuje abstraktní připojení pro dotazování přes několik horizontálních oddílů. Osvojit si **MultiShardQuery** v kombinaci s EF je nad rámec tohoto dokumentu

## <a name="conclusion"></a>Závěr
Pomocí kroků uvedených v tomto dokumentu, EF aplikace můžete použít možnost klientské knihovny elastické databáze pro závislé na data směrování podle refaktoring konstruktory **DbContext** použitou v aplikaci EF podtřídy. Toto nastavení omezuje změny požadované na těchto místech kde **DbContext** třídy již existují. Kromě toho EF aplikace můžete nadále těžit z nasazení automatické schéma kombinací kroky, které vyvolání nezbytné migrace EF s registrací nové horizontálních oddílů a mapování v mapě horizontálního oddílu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
