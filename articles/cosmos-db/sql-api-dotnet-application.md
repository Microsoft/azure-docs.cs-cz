---
title: 'Kurz k ASP.NET MVC pro službu Azure Cosmos DB: Vývoj webové aplikace | Dokumentace Microsoftu'
description: Kurz k ASP.NET MVC, v rámci kterého se vytvoří webová aplikace MVC s použitím služby Azure Cosmos DB. Budete ukládat JSON a přístupová data z aplikace seznamu úkolů hostované na Webech Azure – podrobný kurz ASP.NET MVC.
keywords: kurz asp.net mvc, vývoj webových aplikací, aplikace mvc web, kurz asp net mvc krok za krokem
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: 52532d89-a40e-4fdf-9b38-aadb3a4cccbc
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/03/2017
ms.author: sngun
ms.custom: devcenter
ms.openlocfilehash: 1193ef84f8edf701f98e50d92a67426e36c40218
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="_Toc395809351"></a>Kurz k ASP.NET MVC: Vývoj webové aplikace s použitím služby Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Pro větší názornost, jak lze pomocí služby Azure Cosmos DB efektivně ukládat dokumenty JSON a zadávat na ně dotazy, obsahuje tento článek úplný podrobný návod, jak pomocí služby Azure Cosmos DB vytvořit aplikaci seznamu úkolů. Úkoly se budou ve službě Azure Cosmos DB ukládat jako dokumenty JSON.

![Snímek obrazovky webové aplikace MVC pro seznam úkolů vytvořené v tomto kurzu – podrobný kurz ASP.NET MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Tento návod ukazuje, jak pomocí služby Azure Cosmos DB ukládat a přistupovat k datům z webové aplikace ASP.NET MVC hostované v Azure. Pokud hledáte kurz, který se zaměřuje jenom na službu Azure Cosmos DB, nikoli komponenty ASP.NET MVC, přečtěte si téma popisující [vytvoření konzolové aplikace využívající službu Azure Cosmos DB v jazyce C#](sql-api-get-started.md).

> [!TIP]
> V tomto kurzu se předpokládá, že již máte zkušenosti s používáním ASP.NET MVC a Webů Azure. Pokud jsou pro vás technologie ASP.NET nebo [požadované nástroje](#_Toc395637760) nové, doporučujeme stáhnout úplný ukázkový projekt z [GitHubu][GitHub] a postupovat podle pokynů v této ukázce. Až jej budete mít sestavený, můžete se k tomuto článku vrátit, abyste kódu lépe porozuměli v kontextu projektu.
> 
> 

## <a name="_Toc395637760"></a>Předpoklady pro tento databázový kurz
Než budete postupovat podle pokynů tohoto článku, měli byste se ujistit, že máte následující:

* Aktivní účet Azure.  Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK pro .NET pro Visual Studio 2017, k dispozici prostřednictvím Instalační program Visual Studio.

Všechny snímky obrazovky v tomto článku byly pořízeny pomocí nástroje Microsoft Visual Studio Community 2017. Pokud je systém konfigurován s jinou verzí je možné, že vaše obrazovky a možnosti budou mírně lišit, ale pokud splníte výše uvedené požadavky tohoto řešení by mělo fungovat.

## <a name="_Toc395637761"></a>Krok 1: Vytvoření účtu databáze Azure Cosmos DB
Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již máte účet SQL pro Azure Cosmos DB nebo pokud používáte emulátor DB Cosmos Azure pro účely tohoto kurzu, můžete přeskočit na [vytvoření nové aplikace ASP.NET MVC](#_Toc395637762).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Nyní vám ukážeme, jak od základů vytvořit novou aplikaci ASP.NET MVC. 

## <a name="_Toc395637762"></a>Krok 2: Vytvoření nové aplikace ASP.NET MVC

1. V nástroji Visual Studio najeďte myší v nabídce **Soubor** na **Nový** a klikněte na **Projekt**. Zobrazí se dialogové okno **Nový projekt**.

2. V podokně **Typy projektů** rozbalte **Šablony**, **Visual C#**, **Web** a vyberte **Webová aplikace ASP.NET**.

      ![Snímek obrazovky dialogového okna Nový projekt se zvýrazněným typem projektu Webová aplikace ASP.NET](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Do pole **Název** zadejte název projektu. Tento kurz používá název todo. Pokud se rozhodnete použít něco jiného, pak kdykoli v tomto kurzu bude zmíněn obor názvů todo, je potřeba upravit poskytnuté ukázky kódu tak, aby používaly vámi zvolený název aplikace. 
4. Po kliknutí na **Procházet** přejděte na složku, ve které chcete vytvořit projekt, a klikněte na **OK**.
   
      **Nové webové aplikace ASP.NET** zobrazí se dialogové okno.
   
    ![Snímek obrazovky dialogového okna nové webové aplikace ASP.NET se zvýrazněnou šablonou aplikace MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. V podokně šablon vyberte **MVC**.

6. Klikněte na **OK** a nechte Visual Studio odvést svou práci s generováním prázdné šablony ASP.NET MVC. 

          
7. Až nástroj Visual Studio dokončí vytváření standardní aplikace MVC, budete mít k dispozici prázdnou aplikaci ASP.NET, kterou můžete spouštět místně.
   
    Lokální spouštění projektu přeskočíme, protože všichni jsme již určitě viděli aplikaci ASP.NET Hello World. Přejděme rovnou k přidání služby Azure Cosmos DB do projektu a sestavení aplikace.

## <a name="_Toc395637767"></a>Krok 3: Přidání služby Azure Cosmos DB do projektu webové aplikace MVC
Teď již máme za sebou většinu potřebných příprav technologie ASP.NET MVC, které potřebujeme pro toto řešení. Přejděme tedy ke skutečnému účelu tohoto kurzu, a tím je přidání služby Azure Cosmos DB do webové aplikace MVC.

1. .NET SDK služby Azure Cosmos DB je zabalené a distribuovat jako balíčku NuGet. Balíček NuGet získáte v nástroji Visual Studio tak, že v něm použijete správce balíčků NuGet. Klikněte pravým tlačítkem na projekt v **Průzkumníkovi řešení** a pak levým na **Spravovat balíčky NuGet**.
   
    ![Snímek obrazovky možností nabídky po kliknutí pravým tlačítkem na projekt webové aplikace v Průzkumníkovi řešení se zvýrazněnou možností Spravovat balíčky NuGet.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Zobrazí se dialogové okno **Správa balíčků NuGet**.
2. Do pole **Procházet** NuGet zadejte ***Azure DocumentDB***. (Název balíčku nebyla aktualizována pro Azure Cosmos DB.)
   
    Ve výsledcích nainstalovat **Microsoft.Azure.DocumentDB Microsoft** balíčku. Tím stáhnout a nainstalovat balíček Azure Cosmos DB a také všechny závislosti, jako je například Newtonsoft.Json. Dokončete instalaci tak, že v okně **Náhled** kliknete na **OK** a v okně **Souhlas s podmínkami licence** na **Přijímám**.
   
    ![Snímek obrazovky okna Správa balíčků NuGet s Microsoft Azure Cosmos DB klientské knihovny zvýrazněná](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      K instalaci balíčku můžete případně použít i Konzolu Správce balíčků. Pokud ji chcete využít, v nabídce **Nástroje** klikněte na **Správce balíčků NuGet** a pak na **Konzola Správce balíčků**. Do příkazového řádku zadejte následující příkaz.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Až se balíček nainstaluje, řešení Visual Studio by se mělo podobat tomu na následujícím obrázku se dvěma přidanými referencemi, Microsoft.Azure.Documents.Client a Newtonsoft.Json.
   
    ![Snímek obrazovky se dvěma přidanými referencemi na datový projekt JSON v Průzkumníkovi řešení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Krok 4: Nastavení aplikace ASP.NET MVC
Nyní k této aplikaci MVC přidejme modely, zobrazení a kontrolery:

* [Přidání modelu](#_Toc395637764)
* [Přidání kontroleru](#_Toc395637765)
* [Přidání zobrazení](#_Toc395637766)

### <a name="_Toc395637764"></a>Přidání datového modelu JSON
Začněme vytvořením velkého **M** v MVC, tedy modelem. 

1. V **Průzkumníkovi řešení** klikněte pravým tlačítkem na složku **Modely**, pak levým na **Přidat** a nakonec také levým na **Třída**.
   
      Zobrazí se dialogové okno **Přidat novou položku**.
2. Pojmenujte svou novou třídu **Item.cs** a klikněte na **Přidat**. 
3. Do tohoto nového souboru **Item.cs** přidejte za poslední *příkaz using* následující příkaz.
   
        using Newtonsoft.Json;
4. Nyní nahraďte tento kód 
   
        public class Item
        {
        }
   
    následujícím kódem.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Všechna data ve službě Azure Cosmos DB se přenesou a uloží jako dokumenty JSON. Pokud chcete řídit způsob, jakým se objekty serializují a deserializují technologií JSON.NET, můžete použít atribut **JsonProperty**, jak je předvedeno v třídě **Item**, kterou jsme právě vytvořili. Není nezbytně **nutné** to provést, ale chceme se ujistit, že naše vlastnosti dodržují konvence pojmenování ve formátu JSON camelCase. 
   
    Nejenže můžete určovat formát názvu vlastnosti, když se ukládá do formátu JSON, ale můžete zcela přejmenovat vlastnosti .NET, jako jsme to udělali s vlastností **Popis**. 

### <a name="_Toc395637765"></a>Přidání kontroleru
Tím je vyřešeno **M**, teď vytvořme **C** z MVC, tedy třídu kontroleru (angl. controller).

1. V **Průzkumníkovi řešení** klikněte pravým tlačítkem na složku **Kontrolery**, pak levým na **Přidat** a nakonec také levým na **Kontroler**.
   
    Zobrazí se dialogové okno **Přidat vygenerované uživatelské rozhraní**.
2. Vyberte **Kontroler MVC 5 – prázdný** a klikněte na **Přidat**.
   
    ![Snímek obrazovky dialogového okna Přidat vygenerované uživatelské rozhraní se zvýrazněnou možností Kontroler MVC 5 – prázdný](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Pojmenujte nový kontroler, **ItemController**.
   
    ![Snímek obrazovky dialogového okna Přidat kontroler](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Až se soubor vytvoří, řešení v nástroji Visual Studio by se mělo podobat tomu na následujícím obrázku s novým souborem ItemController.cs v **Průzkumníkovi řešení**. Zobrazuje se i nový soubor Item.cs vytvořený dříve.
   
    ![Snímek obrazovky řešení Visual Studio – Průzkumník řešení se zvýrazněnými novými soubory ItemController.cs a Item.cs](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Soubor ItemController.cs můžete zavřít – vrátíme se k němu později. 

### <a name="_Toc395637766"></a>Přidání zobrazení
Nyní pojďme vytvořit **V** z MVC, tedy zobrazení (angl. view):

* [Přidání zobrazení Index položky](#AddItemIndexView)
* [Přidání zobrazení Nová položka](#AddNewIndexView)
* [Přidání zobrazení Upravit položku](#_Toc395888515)

#### <a name="AddItemIndexView"></a>Přidání zobrazení Index položky
1. V **Průzkumníkovi řešení** rozbalte složku **Zobrazení**, klikněte pravým tlačítkem na prázdnou složku **Položka**, kterou pro vás Visual Studio vytvořilo již dříve (když jste přidali **ItemController**), klikněte na **Přidat** a nakonec klikněte na **Zobrazení**.
   
    ![Snímek obrazovky Průzkumníka řešení, na kterém se zobrazuje složka Položka vytvořená nástrojem Visual Studio se zvýrazněnými příkazy Přidat zobrazení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. V dialogovém okně **Přidat zobrazení** proveďte následující akce:
   
   * Do pole **Název zobrazení** zadejte ***Index***.
   * V poli **Šablona** vyberte ***Seznam***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
     
   ![Snímek obrazovky ukazující dialogové okno Přidat zobrazení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Jakmile jsou všechny tyto hodnoty nastaveny, klikněte na **Přidat** a nechejte Visual Studio vytvořit novou šablonu zobrazení. Až se to dokončí, otevře se soubor cshtml, který se vytvoří. Tento soubor můžeme v nástroji Visual Studio zavřít, protože se k němu vrátíme později.

#### <a name="AddNewIndexView"></a>Přidání zobrazení Nová položka
Podobným způsobem, jakým jsme vytvořili zobrazení **Index položky**, vytvoříme nyní nové zobrazení pro vytváření nových **položek**.

1. V **Průzkumníkovi řešení** klikněte pravým tlačítkem na složku **Položka**, pak levým na **Přidat** a nakonec také levým na **Zobrazení**.
2. V dialogovém okně **Přidat zobrazení** proveďte následující akce:
   
   * Do pole **Název zobrazení** zadejte ***Create***.
   * V poli **Šablona** vyberte ***Create***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
   * Klikněte na tlačítko **Add** (Přidat).
   
#### <a name="_Toc395888515"></a>Přidání zobrazení Upravit položku
A nakonec stejným způsobem jako předtím přidejte jedno poslední zobrazení pro úpravu **položky**.

1. V **Průzkumníkovi řešení** klikněte pravým tlačítkem na složku **Položka**, pak levým na **Přidat** a nakonec také levým na **Zobrazení**.
2. V dialogovém okně **Přidat zobrazení** proveďte následující akce:
   
   * Do pole **Název zobrazení** zadejte ***Edit***.
   * V poli **Šablona** vyberte ***Edit***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
   * Klikněte na tlačítko **Add** (Přidat).

Až bude vše hotovo, zavřete všechny dokumenty cshtml v nástroji Visual Studio, protože se k těmto zobrazením vrátíme později.

## <a name="_Toc395637769"></a>Krok 5: Připojení služby Azure Cosmos DB
Nyní, když jsou vyřešeny všechny standardní náležitosti MVC, se můžeme zaměřit na přidání kódu pro službu Azure Cosmos DB. 

V této části přidáme kód pro zpracování následujícího:

* [Výpis neúplných položek](#_Toc395637770)
* [Přidávání položek](#_Toc395637771)
* [Úprava položek](#_Toc395637772)

### <a name="_Toc395637770"></a>Výpis neúplných položek ve webové aplikaci MVC
První věc, kterou je zde potřeba udělat, je přidat třídu, která bude obsahovat veškerou logiku pro připojení a používání služby Azure Cosmos DB. Pro účely tohoto kurzu zapouzdříme všechnu tuto logiku do třídy úložiště nazvané DocumentDBRepository. 

1. V **Průzkumníkovi řešení** klikněte pravým tlačítkem na složku projekt, pak levým na **Přidat** a nakonec také levým na **Třída**. Pojmenujte novou třídu **DocumentDBRepository** a klikněte na **Přidat**.
2. Do nově vytvořené třídy **DocumentDBRepository** přidejte následující *příkazy using* nad deklaraci *namespace*.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net
        
    Nyní nahraďte tento kód 
   
        public class DocumentDBRepository
        {
        }
   
    následujícím kódem.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Čteme několik hodnot z konfigurace, proto si otevřete soubor **Web.config** své aplikace a do části `<AppSettings>` přidejte následující řádky.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Nyní pomocí okna Klíče na Portálu Azure aktualizujte hodnoty pro *endpoint* a *authKey*. Jako hodnotu pro nastavení endpoint použijte **URI** z okna Klíče, pro nastavení authKey pak jako hodnotu využijte **PRIMÁRNÍ KLÍČ** nebo **SEKUNDÁRNÍ KLÍČ** z téhož okna.

    Vyřešeno připojení úložiště Azure Cosmos DB nyní Pojďme přidat logiku aplikace.

1. Tím prvním, co chceme mít možnost v aplikaci seznamu úkolů dělat, je zobrazit neúplné položky.  Zkopírujte a vložte následující fragment kódu kamkoli do třídy **DocumentDBRepository**.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Otevřete **ItemController**, který jsme přidali dříve, a nad deklaraci oboru názvů přidejte následující *příkazy using*.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Pokud se váš projekt nejmenuje todo, bude nutné aktualizovat příkaz todo.Models tak, aby odpovídal názvu projektu.
   
    Nyní nahraďte tento kód
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    následujícím kódem.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Otevřete **Global.asax.cs** a přidejte následující řádek do metody **Application_Start** 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

V tuto chvíli by mělo být možné sestavit vaše řešení bez jakýchkoli chyb.

Pokud jste nyní aplikaci spustili, přešli jste na **HomeController** a zobrazení **Index** tohoto kontroleru. To je výchozí chování projektu šablony MVC, který jsme zvolili na začátku, ale takto to mít nechceme! Změňte směrování v této aplikaci MVC, abychom dané chování upravili.

Otevřete ***App\_Start\RouteConfig.cs***, vyhledejte řádek, který začíná na defaults: a změňte jej tak, že bude vypadat přibližně takto.

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Tímto bude aplikaci ASP.NET MVC oznámeno, že pokud jste v adrese URL nezadali hodnotu pro řízení chování směrování, má se jako kontroler místo **Home** použít **Item** a jako zobrazení **Index**.

Když nyní aplikaci spustíte, zavolá váš **ItemController**, který zavolá třídu úložiště a použije metodu GetItems, pomocí které vrátí všechny neúplné položky do zobrazení **View**\\**Item**\\**Index**. 

Pokud teď projekt sestavíte a spustíte, mělo by se zobrazit něco přibližně takového.    

![Snímek obrazovky webové aplikace vytvořené v tomto databázovém kurzu](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Přidávání položek
Přidejme do databáze nějaké položky, abychom tam neměli jen prázdnou mřížku.

Přidejme do třídy Azure Cosmos DBRepository a ItemController kód, který zajistí trvalé uchování záznamu ve službě Azure Cosmos DB.

1. Do třídy **DocumentDBRepository** přidejte následující metodu.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Tato metoda jednoduše přijme předaný k němu objekt a zachová jej v Azure Cosmos DB.
2. Otevřete soubor ItemController.cs a přidejte do třídy následující fragment kódu. Takto ASP.NET MVC ví, co dělat s akcí **Create**. V tomto případě má pouze vykreslit přidružené zobrazení Create.cshtml vytvořené dříve.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Nyní potřebujeme v tomto kontroleru další kód, který přijme odeslané informace ze zobrazení **Create**.
3. Přidejte do třídy ItemController.cs další blok kódu, který technologii ASP.NET MVC řekne, co dělat s operací POST formuláře pro tento kontroler.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Teto kód zavolá do DocumentDBRepository a použije metodu CreateItemAsync, aby se nová položka úkolu zachovala v databázi. 
   
    **Poznámka k zabezpečení:** Atribut **ValidateAntiForgeryToken** zde slouží k tomu, aby pomohl zabezpečit tuto aplikace před útokem CSRF. Není třeba provádět žádnou další akci, jen přidat tento atribut – vaše zobrazení musí s tímto tokenem proti padělání pracovat také. Další informace k tomuto tématu a příklady, jak toto správně implementovat, najdete v článku o [zabránění útoku CSRF][Preventing Cross-Site Request Forgery]. Zdrojový kód dostupný na [GitHubu][GitHub] má toto plně implementováno.
   
    **Poznámka k zabezpečení:** U parametru metody používáme i atribut **Bind**, abychom zvýšili zabezpečení před útoky typu OVERPOST. Další informace najdete v tématu [Základní operace CRUD v ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

Tímto je kód potřebný k přidávání nových položek do databáze hotový.

### <a name="_Toc395637772"></a>Úprava položek
Ještě musíme implementovat jednu poslední funkci, a to přidat schopnost upravit **položky** v databázi a označit je jako dokončené. Zobrazení pro úpravy již bylo do projektu přidáno, takže nyní potřebujeme jen opět přidat kód do kontroleru a třídy **DocumentDBRepository**.

1. Do třídy **DocumentDBRepository** přidejte následující kód.
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    První z těchto metod, **GetItem**, načítá položku ze služby Azure Cosmos DB, která se předá zpět do kontroleru **ItemController** a pak do zobrazení **Edit**.
   
    Druhá metoda, kterou jsme právě přidali, nahrazuje **dokument** ve službě Azure Cosmos DB verzí **dokumentu** předanou z kontroleru **ItemController**.
2. Do třídy **ItemController** přidejte následující kód.
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    První metoda zpracovává operaci HTTP GET, ke které dochází, když uživatel klikne na odkaz **Upravit** v zobrazení **Index**. Tato metoda načítá [**dokument**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) ze služby Azure Cosmos DB a předává jej do zobrazení **Edit**.
   
    Zobrazení **Edit** pak provede operaci HTTP POST do **IndexControlleru**. 
   
    Druhá metoda, kterou jsme přidali, zpracovává předávání aktualizovaného objektu do služby Azure Cosmos DB pro trvalé uchování v databázi.

To je vše, nic dalšího ke spuštění aplikace, vypsání neúplných **položek**, přidávání nových **položek** a úpravě **položek** nepotřebujeme.

## <a name="_Toc395637773"></a>Krok 6: Místní spuštění aplikace
Aplikaci otestujete na svém místním počítači tak, že provedete následující akce:

1. Stiskněte v nástroji Visual Studio klávesu F5 – aplikace se sestaví v režimu ladění. Po sestavení aplikace by se měl spustit prohlížeč se stránkou s prázdnou mřížkou, kterou jsme viděli dříve:
   
    ![Snímek obrazovky webové aplikace vytvořené v tomto databázovém kurzu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Klikněte na odkaz **Vytvořit nový** a do polí **Název** a **Popis** zadejte hodnoty. Zaškrtávací políčko **Dokončeno** ponechte prázdné, jinak bude nová **položka** přidána ve stavu dokončení a nezobrazí se v úvodním seznamu.
   
    ![Snímek obrazovky zobrazení pro vytváření](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Klikněte na **Vytvořit**. Budete přesměrováni zpět na zobrazení **Index** a v seznamu se zobrazí **položka**.
   
    ![Snímek obrazovky zobrazení pro index](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Do seznamu úkolů můžete dle libosti přidat několik dalších **položek**.
    
4. Klikněte na **Upravit** vedle **položky** v seznamu. Přejdete na zobrazení **Upravit**, kde můžete aktualizovat jakoukoli vlastnost objektu, včetně příznaku **Dokončeno**. Pokud označíte příznak **Dokončeno** a kliknete na **Uložit**, **položka** se odstraní ze seznamu neúplných úkolů.
   
    ![Snímek obrazovky zobrazení Index se zaškrtnutým políčkem Dokončeno](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Až budete s testováním aplikace hotovi, stiskněte Ctrl+F5, aby se ukončilo ladění aplikace. Jste připraveni aplikaci nasadit!

## <a name="_Toc395637774"></a>Krok 7: Nasazení aplikace do služby Azure App Service 
Teď, když máte je aplikace dokončena a správně funguje s Azure Cosmos DB vytvoříme nasadíme tuto webovou aplikaci do služby Azure App Service.  

1. K publikování této aplikace stačí kliknout pravým tlačítkem na projekt v **Průzkumníkovi řešení** a kliknout na **Publikovat**.
   
    ![Snímek obrazovky s možností Publikovat v Průzkumníkovi řešení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. V **publikovat** dialogové okno, klikněte na tlačítko **Microsoft Azure App Service**, pak vyberte **vytvořit nový** vytvořit profil služby App Service, nebo kliknutím na tlačítko **vybrat existující**  sloužící k existujícímu profilu.

    ![Dialogové okno publikování v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Pokud máte existující profil Azure App Service, zadejte název vašeho předplatného. Použití **zobrazení** filtrovat a řadit podle skupiny prostředků nebo typ prostředku a potom vyberte Azure App Service. 
   
    ![Dialogové okno služby App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Chcete-li vytvořit nový profil Azure App Service, klikněte na tlačítko **vytvořit nový** v **publikovat** dialogové okno. V **vytvořit službu App Service** dialogové okno, zadejte název webové aplikace a příslušné předplatné, skupinu prostředků a plán služby App Service, a pak klikněte na tlačítko **vytvořit**.

    ![Služby App Service dialogové okno vytvořit v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Během pár sekund bude Visual Studio dokončí publikování webové aplikace a spustí prohlížeč, kde uvidíte vaše handiwork běžící v Azure!



## <a name="_Toc395637775"></a>Další kroky
Blahopřejeme! Právě vytvořené webové aplikace pomocí Azure Cosmos DB první rozhraní ASP.NET MVC a publikovali jste ji na Azure. Zdrojový kód hotové aplikace včetně podrobností a odstraněných funkcí, které nebyly součástí tohoto kurzu, si můžete stáhnout nebo naklonovat z [GitHubu][GitHub]. Pokud byste tedy chtěli tyto funkce zahrnout do své aplikace, můžete si kód stáhnout a přidat.

Do aplikace přidat další funkce, prohlédněte si rozhraní API, které jsou k dispozici v [knihovny .NET DB Cosmos Azure](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) a Nebojte se přispět do knihovny Azure DB Cosmos .NET na [Githubu] [GitHub]. 

[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
