---
title: Kurz vytvoření webové aplikace s Redis Cache, která používá princip s doplňováním mezipaměti aplikací | Microsoft Docs
description: Zjistěte, jak vytvořit webovou aplikaci s Redis Cache, která používá princip s doplňováním mezipaměti aplikací.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/30/2018
ms.author: wesmc
ms.openlocfilehash: c2dffc178a017d8b16ea7a36407d8bbf62c6d642
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242139"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>Kurz: Vytvoření tabulky výsledků s principem s doplňováním mezipaměti aplikací v ASP.NET

V tomto kurzu aktualizujete webovou aplikaci ASP.NET *ContosoTeamStats* vytvořenou v [rychlém startu ASP.NET pro Azure Redis Cache](cache-web-app-howto.md), abyste mohli přidat tabulku výsledků, která používá [princip s doplňováním mezipaměti aplikací](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) s Azure Redis Cache. Ukázková aplikace zobrazí seznam týmových statistik z databáze a předvede vám různé způsoby použití Azure Redis Cache k ukládání a načítání dat z mezipaměti, aby se zlepšil výkon. Po dokončení kurzu budete mít funkční webovou aplikaci, která čte a zapisuje do databáze, je optimalizovaná pomocí Azure Redis Cache a je hostovaná v Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zlepšit propustnost dat a snížit zatížení databáze díky ukládání a načítání dat pomocí Azure Redis Cache
> * Použít seřazenou sadu Redis k načtení pěti nejlepších týmů
> * Zřídit prostředky Azure pro aplikaci pomocí šablony Resource Manageru
> * Publikovat aplikaci do Azure pomocí sady Visual Studio

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Tento kurz navazuje přímo na [rychlý start ASP.NET pro Azure Redis Cache](cache-web-app-howto.md). Pokud jste to ještě neudělali, absolvujte nejprve tento rychlý start.
* Nainstalovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    * Vývoj pro ASP.NET a web
    * Vývoj pro Azure
    * Vývoj pro desktopové aplikace .NET pomocí SQL Server Express LocalDB nebo [edice SQL Server 2017 Express](https://www.microsoft.com/sql-server/sql-server-editions-express)

## <a name="add-a-leaderboard-to-the-project"></a>Přidání tabulky výsledků do projektu

V této části kurzu nakonfigurujete projekt *ContosoTeamStats* s tabulkou výsledků, která oznamuje statistiku výher, proher a remíz u seznamu fiktivních týmů.

### <a name="add-the-entity-framework-to-the-project"></a>Přidání sady technologií Entity Framework do projektu

1. V sadě Visual Studio otevřete řešení *ContosoTeamStats*, které jste vytvořili v [rychlém startu ASP.NET pro Azure Redis Cache](cache-web-app-howto.md).
2. Klikněte na **Nástroje > Správce balíčků NuGet > Konzola Správce balíčků**.
3. Z okna **konzoly Správce balíčků** spusťte následující příkaz a nainstalujte sadu technologií Entity Framework:

    ```powershell
    Install-Package EntityFramework
    ```

Další informace o tomto balíčku najdete na stránce NuGet pro [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-team-model"></a>Přidání modelu Team

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Modely** a vyberte **Přidat**, **Třídu**.

1. Jako název třídy zadejte `Team` a klikněte na **Přidat**.

    ![Přidání třídy modelu](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. Nahraďte příkazy `using` v horní části souboru *Team.cs* následujícími příkazy `using`:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. Nahraďte definici třídy `Team` za následující fragment kódu, který obsahuje kromě aktualizované definice třídy `Team` i některé další pomocné třídy Entity Frameworku. Tento kurz používá přístup Code First se sadou technologií Entity Framework. Díky tomu může sada technologií Entity Framework vytvořit databázi z vašeho kódu. Další informace o přístupu Code First k Entity Frameworku, který používáme v tomto kurzu, najdete v tématu [Code First pro novou databázi](https://msdn.microsoft.com/data/jj193542).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. V **Průzkumníku řešení** dvakrát klikněte na soubor **Web.config** a otevřete ho.

    ![Soubor web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. Přidejte následující sekci `connectionStrings` do sekce `configuration`. Název připojovacího řetězce se musí shodovat s názvem třídy kontextu databáze v sadě Entity Framework, což je `TeamContext`.

    Tento připojovací řetězec předpokládá, že jste splnili [požadavky](#prerequisites) a nainstalovali jste databázi SQL Server Express LocalDB, která je součástí sady funkcí pro *vývoj desktopových aplikací .NET* instalované spolu se sadou Visual Studio 2017.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Následující příklad ukazuje novou sekci `connectionStrings` následující `configSections` v sekci `configuration`:

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>Přidání kontroleru TeamsController a zobrazení

1. V sadě Visual Studio sestavte projekt. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na složku **Kontrolery** a vyberte **Přidat**, **Kontroler**.

1. Vyberte **Kontroler MVC 5 se zobrazeními, s použitím Entity Frameworku**, a klikněte na **Přidat**. Pokud po kliknutí na **Přidat** dojde k chybě, ujistěte se, že jste nejdříve sestavili projekt.

    ![Přidání třídy kontroleru](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. Vyberte **Team (ContosoTeamStats.Models)** z rozevíracího seznamu **Třída modelu**. Vyberte **TeamContext (ContosoTeamStats.Models)** z rozevíracího seznamu **Třída kontextu dat**. Zadejte `TeamsController` do textového pole **Název kontroleru** (pokud není vyplněné automaticky). Kliknutím na **Přidat** vytvoříte třídu kontroleru a přidáte výchozí zobrazení.

    ![Konfigurace kontroleru](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. V **Průzkumníku řešení** rozbalte **Global.asax** a dvojím kliknutím otevřete soubor **Global.asax.cs**.

    ![Soubor Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. Přidejte následující dva příkazy `using` do horní části souboru pod ostatní příkazy `using`:

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. Přidejte následující řádek kódu na konec metody `Application_Start`:

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. V **Průzkumníku řešení** rozbalte `App_Start` a dvakrát klikněte na soubor `RouteConfig.cs`.

    ![Soubor RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. V metodě `RegisterRoutes` nahraďte `controller = "Home"` v cestě `Default` řetězcem `controller = "Teams"`, jak je znázorněno v následujícím příkladu:

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>Konfigurace zobrazení Rozložení

1. V **Průzkumníku řešení** rozbalte složku **Zobrazení**, poté složku **Sdílené**, a dvakrát klikněte na soubor **_Layout.cshtml**. 

    ![Soubor _Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. Změňte obsah elementu `title` a nahraďte `My ASP.NET Application` za `Contoso Team Stats`, jak je znázorněno v následujícím příkladu:

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. V části `body` přidejte následující nový příkaz `Html.ActionLink` pro *Contoso Team Stats* hned pod odkaz pro *test Azure Redis Cache*.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![Změny kódu](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. Stisknutím kombinace kláves **Ctrl+F5** sestavíte a spustíte aplikaci. Tato verze aplikace načítá výsledky přímo z databáze. Všimněte si akcí **Vytvořit nový**, **Upravit**, **Podrobnosti** a **Odstranit**, které do aplikace automaticky přidalo vygenerované uživatelské rozhraní **Kontroler MVC 5 se zobrazeními, s použitím Entity Frameworku**. V další části kurzu přidáte mezipaměť Redis Cache pro optimalizování přístupu k datům a poskytnutí dodatečných funkcí aplikaci.

    ![Startovní aplikace](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-redis-cache"></a>Konfigurace aplikace pro Redis Cache

V této části kurzu nakonfigurujete ukázkovou aplikaci pro ukládání a načítání týmových statistik Contoso z instance služby Azure Redis Cache pomocí klienta mezipaměti [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Přidání připojení mezipaměti ke kontroleru Teams

V rychlém startu jste už nainstalovali balíček klientské knihovny *StackExchange.Redis*. Také jste nakonfigurovali aplikaci *CacheConnection*, aby se používala místně a pomocí publikované služby App Service. Použijte stejnou klientskou knihovnu a informace *CacheConnection* uvedené v kontroleru *TeamsController*.

1. V **Průzkumníku řešení** rozbalte složku **Kontrolery** a dvojím kliknutím otevřete soubor **TeamsController.cs**.

    ![Kontroler Teams](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. Přidejte následující dva příkazy `using` do souboru **TeamsController.cs**:

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. Přidejte následující dvě vlastnosti do třídy `TeamsController`:

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>Aktualizace kontroleru TeamsController na čtení z mezipaměti nebo databáze

V této ukázce lze týmové statistiky získat z databáze nebo z mezipaměti. Týmové statistiky jsou v mezipaměti uložené pomocí datových typů Redis jako serializovaný seznam `List<Team>`, a také jako seřazená sada. Při načítání položek ze seřazené sady můžete načíst část položek, všechny položky nebo provézt dotaz na určité položky. V této ukázce spustíte dotaz na seřazenou sadu, abyste získali seznam 5 nejlepších týmů seřazených podle počtu výher.

Pro používání Azure Redis Cache není nutné v mezipaměti ukládat týmové statistiky ve více formátech. V tomto kurzu používáme více formátů, abychom předvedli různé způsoby a datové typy, které můžete použít pro ukládání dat do mezipaměti.

1. Přidejte následující příkazy `using` do horní části souboru `TeamsController.cs` k ostatním příkazům `using`:

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. Nahraďte stávající implementaci metody `public ActionResult Index()` za následující implementaci:

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. Přidejte následující tři metody do třídy `TeamsController` pro implementaci typů akcí `playGames`, `clearCache` a `rebuildDB` z výrazu switch, který jste přidali v předchozím fragmentu kódu.

    Metoda `PlayGames` aktualizuje týmové statistiky tak, že simuluje sezónu her, výsledky uloží do databáze, a vymaže již zastaralá data z mezipaměti.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Metoda `RebuildDB` znovu inicializuje databázi s výchozí sadou týmů, vygeneruje pro ně statistiky, a vymaže již zastaralá data z mezipaměti.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    Metoda `ClearCachedTeams` odebere z mezipaměti všechny uložené týmové statistiky.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. Přidejte následující čtyři metody do třídy `TeamsController` pro implementaci různých způsobů načítání týmových statistik z mezipaměti a z databáze. Každá z těchto metod vrátí seznam `List<Team>`, který se následně zobrazí v zobrazení.

    Metoda `GetFromDB` načítá týmové statistiky z databáze.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    Metoda `GetFromList` načítá týmové statistiky z mezipaměti jako serializovaný seznam `List<Team>`. Pokud se statistika v mezipaměti nenachází, dojde k neúspěšnému přístupu do mezipaměti. V takovém případě se týmové statistiky načtou z databáze a uloží se do mezipaměti pro použití v další žádosti. V této ukázce je pro serializaci objektů .NET do a z mezipaměti použita serializace JSON.NET. Další informace najdete v tématu [Práce s objekty .NET v Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    Metoda `GetFromSortedSet` načítá týmové statistiky ze seřazené sady v mezipaměti. Dojde-li k neúspěšnému přístupu do mezipaměti, týmové statistiky se načtou z databáze a uloží se do mezipaměti jako seřazená sada.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    Metoda `GetFromSortedSetTop5` načte seznam pěti nejlepších týmů ze seřazené sady uložené v mezipaměti. Začne tím, že zjistí přítomnost klíče `teamsSortedSet` v mezipaměti. Pokud tento klíč neexistuje, zavolá se metoda `GetFromSortedSet`, která načte týmové statistiky a uloží je do mezipaměti. Poté se provede dotaz na seřazenou sadu, aby se vrátil seznam pěti nejlepších týmů.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Aktualizace metod Create, Edit a Delete pro práci s mezipamětí

Kód generování uživatelského rozhraní vygenerovaný jako součást této ukázky obsahuje metody pro přidávání, upravování a odstraňování týmů. Pokaždé když dojde k přidání, úpravě nebo odstranění týmu se data v mezipaměti stanou zastaralými. V této části upravíte tyto tři metody pro vymazání týmů z mezipaměti, aby bylo možné mezipaměť aktualizovat.

1. Přejděte k metodě `Create(Team team)` ve třídě `TeamsController`. Přidejte volání metody `ClearCachedTeams`, jak je znázorněno v následujícím příkladu:

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. Přejděte k metodě `Edit(Team team)` ve třídě `TeamsController`. Přidejte volání metody `ClearCachedTeams`, jak je znázorněno v následujícím příkladu:

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. Přejděte k metodě `DeleteConfirmed(int id)` ve třídě `TeamsController`. Přidejte volání metody `ClearCachedTeams`, jak je znázorněno v následujícím příkladu:

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>Přidání metod ukládání do mezipaměti do zobrazení indexu Teams

1. V **Průzkumníku řešení** rozbalte složku **Zobrazení**, poté složku **Týmy**, a dvakrát klikněte na soubor **Index.cshtml**.

    ![Soubor Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. V horní části souboru vyhledejte následující element odstavce:

    ![Tabulka akcí](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    Tento odkaz vytvoří nový tým. Nahraďte element odstavce za následující tabulku. Tato tabulka obsahuje odkazy pro vytvoření nového týmu, hraní nové sezóny her, vymazání mezipaměti, načtení týmů z mezipaměti v různých formátech, načtení týmů z databáze a nové sestavení databáze s novými vzorovými daty.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. Přejděte do dolní části souboru **Index.cshtml** a přidejte následující element `tr` tak, aby byl posledním řádkem poslední tabulky v souboru:

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    Tento řádek zobrazuje hodnotu `ViewBag.Msg` obsahující zprávu o stavu aktuální operace. Hodnota `ViewBag.Msg` se nastaví po kliknutí na některý z odkazů na akce z předchozího kroku.

    ![Zpráva o stavu](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. Projekt sestavíte stisknutím klávesy **F6**.

## <a name="run-the-app-locally"></a>Místní spuštění aplikace

Spusťte aplikaci místně na počítači a ověřte funkci přidanou pro podporu týmů.

V tomto testu jsou aplikace i databáze spuštěny místně. Redis Cache se ale hostuje vzdáleně v Azure. Z tohoto důvodu bude mezipaměť pravděpodobně méně výkonná než databáze. Pro zajištění nejlepšího výkonu by klientská aplikace a instance služby Azure Redis Cache měly být ve stejném umístění. V další části nasadíte všechny prostředky do Azure, abyste viděli, jak se zlepší výkon při použití mezipaměti.

Spuštění aplikace místně:

1. Aplikaci spustíte stisknutím kombinace kláves **Ctrl+F5**.

    ![Aplikace spuštěná místně](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. Otestuje všechny nové metody, které jste přidali do zobrazení. Vzhledem k tomu, že mezipaměť je v těchto testech vzdálená, bude databáze o něco málo výkonnější než mezipaměť.

## <a name="publish-and-run-in-azure"></a>Publikování a spuštění v Azure

### <a name="provision-a-sql-azure-database-for-the-app"></a>Zřízení databáze SQL Azure pro aplikaci

V této části zřídíte novou databázi SQL Azure pro aplikaci, která se bude používat při hostování v Azure.

1. V levém horním rohu portálu [Azure Portal](https://portal.azure.com/) klikněte na **Vytvořit prostředek**.

1. Na stránce **Nový** klikněte na **Databases** > **SQL Database**.

1. Pro novou databázi SQL použijte následující nastavení:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název databáze** | *ContosoTeamsDatabase* | Platné názvy databází najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Předplatné** | *Vaše předplatné*  | Vyberte stejné předplatné, které jste použili k vytvoření mezipaměti a hostování služby App Service. |
   | **Skupina prostředků**  | *TestResourceGroup* | Klikněte na **Použít existující** a použijte stejnou skupinu prostředků, do které jste umístili mezipaměť a App Service. |
   | **Výběr zdroje** | **Prázdná databáze** | Začněte s prázdnou databází. |

1. V části **Server** klikněte na **Konfigurovat požadované nastavení** > **Vytvořit nový server** a zadejte následující informace. Potom klikněte na tlačítko **Vybrat**:

   | Nastavení       | Navrhovaná hodnota | Popis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Název serveru** | Libovolný globálně jedinečný název | Platné názvy serverů najdete v tématu [Pravidla a omezení pojmenování](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Přihlašovací jméno správce serveru** | Libovolné platné jméno | Platná přihlašovací jména najdete v tématu [Identifikátory databází](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Heslo** | Libovolné platné heslo | Heslo musí mít alespoň 8 znaků a musí obsahovat znaky ze tří z následujících kategorií: velká písmena, malá písmena, číslice a jiné než alfanumerické znaky. |
   | **Umístění** | *USA – východ* | Vyberte stejnou oblast, ve které jste vytvořili mezipaměť a App Service. |

1. Kliknutím na **Připnout na řídicí panel** a potom na **Vytvořit** vytvořte novou databázi a server.

1. Po vytvoření databáze klikněte na **Zobrazit databázové připojovací řetězce** a zkopírujte připojovací řetězec **ADO.NET**.

    ![Zobrazení připojovacích řetězců](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Na portálu Azure Portal přejděte na App Service a klikněte na **Nastavení aplikace** a potom v části Připojovací řetězce na **Přidat nový připojovací řetězec**.

1. Přidejte nový připojovací řetězec s názvem *TeamContext*, aby odpovídal třídě kontextu databáze sady technologií Entity Framework. Vložte připojovací řetězec nové databáze jako hodnotu. Nezapomeňte nahradit následující zástupné symboly v připojovacím řetězci a klikněte na **Uložit**:

    | Zástupný symbol | Navrhovaná hodnota |
    | --- | --- |
    | *{vaše_uživatelské jméno}* | Pro server databáze, který jste právě vytvořili, použijte **přihlašovací údaje správce serveru**. |
    | *{vaše_heslo}* | Pro server databáze, který jste právě vytvořili, použijte heslo. |

    Když uživatelské jméno a heslo přidáte jako nastavení aplikace, nezahrnou se do vašeho kódu. Tento přístup vám pomůže přihlašovací údaje ochránit.

### <a name="publish-the-application-updates-to-azure"></a>Publikování aktualizací aplikace do Azure

V tomto kroku kurzu publikujete aktualizace aplikace do Azure a spustíte ji v cloudu.

1. V sadě Visual Studio klikněte pravým tlačítkem na projekt **ContosoTeamStats** a vyberte **Publikovat**.

    ![Publikování](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. Klikněte na **Publikovat**, aby se použil stejný profil publikování, který jste vytvořili v rychlém startu.

3. Po dokončení publikování Visual Studio spustí aplikaci ve výchozím webovém prohlížeči.

    ![Mezipaměť byla přidána](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    Následující tabulka popisuje každý odkaz na akci v ukázkové aplikaci:

    | Akce | Popis |
    | --- | --- |
    | Vytvořit nový |Vytvoření nového týmu. |
    | Odehrát sezónu |Odehrání sezóny her, aktualizace týmových statistik a vymazání zastaralých dat z mezipaměti. |
    | Vymazat mezipaměť |Vymazání týmových statistik z mezipaměti. |
    | Seznam z mezipaměti |Načtení týmových statistik z mezipaměti. Dojde-li k neúspěšnému přístupu do mezipaměti, statistiky se načtou z databáze a uloží se do mezipaměti pro další použití. |
    | Seřazená sada z mezipaměti |Načtení týmových statistik z mezipaměti pomocí seřazené sady. Dojde-li k neúspěšnému přístupu do mezipaměti, statistiky se načtou z databáze a uloží se do mezipaměti jako seřazená sada. |
    | Top 5 týmů z mezipaměti |Načtení Top 5 týmů z mezipaměti pomocí seřazené sady. Dojde-li k neúspěšnému přístupu do mezipaměti, statistiky se načtou z databáze a uloží se do mezipaměti jako seřazená sada. |
    | Načíst z databáze |Načtení týmových statistik z databáze. |
    | Znovu sestavit databázi |Opětovné vytvoření databáze se vzorovými týmovými daty. |
    | Upravit / Podrobnosti / Odstranit |Upravení týmu, zobrazení podrobností o týmu, odstranění týmu. |

Klikněte na některé akce a experimentujte s načítáním dat z různých zdrojů. Všimněte si rozdílů v době trvání různých způsobů načítání dat z databáze a mezipaměti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení ukázkové aplikace můžete odstranit použité prostředky Azure a uspořit tak náklady a prostředky. Všechny prostředky se musí nacházet ve stejné skupině prostředků, abyste je mohli společně odstranit v rámci jedné operace odstranění skupiny prostředků. V tomto tématu byla použita skupina prostředků s názvem *TestResources* (TestovacíProstředky).

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.
2. Zadejte název vaší skupiny prostředků do textového pole **Filtrování položek...**.
3. Klikněte na **...** napravo od skupiny prostředků a klikněte na **Odstranit skupinu prostředků**.

    ![Odstranění](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

    Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Škálování služby Azure Redis Cache](./cache-how-to-scale.md)