---
title: 'Kurz ASP.NET Core MVC pro Azure Cosmos DB: Vývoj webových aplikací'
description: Kurz ASP.NET Core MVC k vytvoření webové aplikace MVC pomocí Azure Cosmos DB. Data JSON a Access se budou ukládat z aplikace TODO hostované v kurzu Azure App Service-ASP NET Core MVC krok za krokem.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67985884"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Kurz: Vývoj webové aplikace ASP.NET Core MVC pomocí Azure Cosmos DB pomocí sady .NET SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


V tomto kurzu se dozvíte, jak pomocí služby Azure Cosmos DB k ukládání a přístup k datům z aplikace ASP.NET MVC, která je hostována v Azure. V tomto kurzu použijete sadu .NET SDK v3. Následující obrázek ukazuje webové stránky, které vytvoříte pomocí ukázky v tomto článku:
 
![Snímek webové aplikace MVC seznamu úkolů vytvořené v tomto kurzu – Kurz ASP NET Core MVC krok za krokem](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Pokud nemáte čas k dokončení kurzu, můžete si stáhnout kompletní ukázkový projekt z [GitHubu][GitHub].

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos
> * Vytvoření aplikace ASP.NET Core MVC
> * Připojení aplikace ke službě Azure Cosmos DB 
> * Provádění operací CRUD u dat

> [!TIP]
> V tomto kurzu se předpokládá, že máte předchozí zkušenosti s používáním ASP.NET Core MVC a Azure App Service. Pokud ASP.NET Core nebo požadované [nástroje](#prerequisites)nepoužíváte, doporučujeme si stáhnout kompletní ukázkový projekt z GitHubu, přidat požadované balíčky [][GitHub]NuGet a spustit ho. Po sestavení projektu, můžete zkontrolovat tohoto článku, abyste lépe porozuměli v kontextu projektu kódu.

## <a name="prerequisites"></a>Požadavky 

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte následující prostředky:

* **Aktivní účet Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Všechny snímky obrazovky v tomto článku byly pořízeny pomocí Microsoft Visual Studio Community 2019. Pokud je systém konfigurován s jinou verzí, je možné, že vaše obrazovky a možnosti nemusí mírně lišit, ale pokud splníte výše uvedené požadavky tohoto řešení by mělo fungovat.

## <a name="create-an-azure-cosmos-account"></a>Krok 1: Vytvoření účtu Azure Cosmos

Začněme vytvořením účtu služby Azure Cosmos. Pokud už máte účet rozhraní SQL API služby Azure Cosmos DB nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [vytvořit novou aplikaci ASP.NET MVC](#create-a-new-mvc-application) oddílu.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

V další části vytvoříte novou ASP.NET Core aplikaci MVC. 

## <a name="create-a-new-mvc-application"></a>Krok 2: Vytvořit novou ASP.NET Core aplikaci MVC

1. V sadě Visual Studio z **souboru** nabídce zvolte **nový**a pak vyberte **projektu**. Zobrazí se dialogové okno **Nový projekt**.

2. V okně **Nový projekt** použijte vstupní pole **Hledat šablony** a vyhledejte text "Web" a pak vyberte **ASP.NET Core webové aplikace**. 

   ![Vytvořit nový projekt ASP.NET Core webové aplikace](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Do pole **Název** zadejte název projektu. Tento kurz používá název todo. Pokud se rozhodnete použít jinou hodnotu než tento název, pak bez ohledu na to, jestli tento kurz mluví s oborem názvů todo, upravte poskytnuté ukázky kódu tak, aby se používaly bez ohledu na to, kterou aplikaci jste 

4. Vyberte **Procházet** a přejděte do složky, ve které chcete vytvořit projekt. Vyberte **Vytvořit**. 

5. Zobrazí se dialogové okno **vytvořit novou webovou aplikaci ASP.NET Core** . V seznamu šablony vyberte možnost **Webová aplikace (model-zobrazení-kontroler)** .

6. Vyberte **vytvořit** a nechte Visual Studio provádět generování uživatelského rozhraní kolem prázdné ASP.NET Core šablony MVC. 

7. Jakmile Visual Studio dokončí vytváření standardní aplikace MVC, máte prázdnou aplikaci ASP.NET, které můžete spustit místně.

## <a name="add-nuget-packages"></a>Krok 3: Přidat Azure Cosmos DB balíček NuGet do projektu

Teď, když máme většinu ASP.NET Core rozhraní MVC, které potřebujeme pro toto řešení, můžeme přidat balíčky NuGet, které jsou potřebné pro připojení k Azure Cosmos DB.

1. Sada Azure Cosmos DB .NET SDK se připravuje a distribuuje jako balíček NuGet. Chcete-li získat balíček NuGet v sadě Visual Studio, používat Správce balíčků NuGet v sadě Visual Studio kliknutím pravým tlačítkem myši na projekt v **Průzkumníka řešení** a pak vyberte **spravovat balíčky NuGet**.
   
   ![Snímek obrazovky s možnostmi kliknutí pravým tlačítkem myši pro projekt webové aplikace v Průzkumník řešení s zvýrazněnou možností spravovat balíčky NuGet.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Zobrazí se dialogové okno **Správa balíčků NuGet**. Nuget **Procházet** zadejte **Microsoft.Azure.Cosmos**. Z výsledků nainstalujte balíček **Microsoft. Azure. Cosmos** . Stáhne a nainstaluje balíček Azure Cosmos DB a jeho závislosti. V okně pro **přijetí licence** vyberte **Souhlasím** a dokončete instalaci.
   
   Alternativně můžete použít Konzola správce balíčků pro instalaci balíčku NuGet. Provedete to tak, na **nástroje** nabídce vyberte možnost **Správce balíčků NuGet**a pak vyberte **Konzola správce balíčků**. Do příkazového řádku zadejte následující příkaz:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Po instalaci balíčku by měl projekt sady Visual Studio obsahovat odkaz na knihovnu Microsoft. Azure. Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Krok 4: Nastavení aplikace ASP.NET Core MVC

Teď k této aplikaci MVC přidejme modely, zobrazení a kontrolery:

* [Přidání modelu](#add-a-model)
* [Přidání kontroleru](#add-a-controller)
* [Přidání zobrazení](#add-views)

### <a name="add-a-model"></a> Přidání modelu

1. Z **Průzkumníku řešení**, klikněte pravým tlačítkem myši **modely** složky, vyberte **přidat**a pak vyberte **třídy**. Zobrazí se dialogové okno **Přidat novou položku**.

1. Pojmenujte novou třídu **Item.cs** a vyberte **Přidat**. 

1. Dále nahraďte kód ve třídě "Item.cs" následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Data uložená ve službě Azure Cosmos DB se přenesou a uloží jako dokumenty JSON. Chcete-li určit způsob, jakým jsou objekty serializovány/deserializovány pomocí JSON.NET, můžete použít atribut **JsonProperty** , jak je znázorněno ve třídě **Item** , kterou jste vytvořili. Nejenom můžete řídit formát názvu vlastnosti, který se nachází ve formátu JSON, můžete také přejmenovat vlastnosti .NET tak, jako jste to udělali s vlastností **dokončeno** . 

### <a name="add-a-controller"></a>Přidání kontroleru

1. Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče** složky, vyberte **přidat**a pak vyberte **řadič**. Zobrazí se dialogové okno **Přidat vygenerované uživatelské rozhraní**.

1. Vyberte **kontroler MVC – prázdné** a vyberte **Přidat**.

   ![Snímek obrazovky dialogového okna Přidat generování uživatelského rozhraní s kontrolérem MVC – prázdná možnost – zvýraznění](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Pojmenujte nový kontroler, **ItemController**a nahraďte kód v tomto souboru následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   **ValidateAntiForgeryToken** atributů se tady používá k pomohl zabezpečit tuto aplikace před útoky proti padělání žádosti více webů. Existuje více než jen přidat tento atribut, zobrazení by měla fungovat s tímto tokenem proti padělání také. Další informace o tomto předmětu a příklady, jak to provést správně, najdete v tématu [prevence padělání požadavků mezi weby][Preventing Cross-Site Request Forgery]. Zdrojový kód dostupný na [GitHubu][GitHub] má toto plně implementováno.

   Použijeme také **svázat** atribut u parametru metody a pomáhá chránit před útoky typu overpost over-pass-the. Další podrobnosti najdete [v tématu základní operace CRUD v ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

### <a name="add-views"></a>Přidání zobrazení

V dalším kroku vytvoříme následující tři zobrazení: 

* [Přidat položky zobrazení seznamu](#AddItemIndexView).
* [Přidat nové položky zobrazení](#AddNewIndexView).
* [Přidání zobrazení upravit položku](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Přidat položky zobrazení seznamu

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na prázdnou **položky** složku, která sadě Visual Studio vytvoří za vás během přidávání  **ItemController** dříve, klikněte na tlačítko **přidat**a potom klikněte na tlačítko **zobrazení**.
   
   ![Snímek obrazovky Průzkumník řešení zobrazující složku položek, kterou vytvořila aplikace Visual Studio, pomocí zvýrazněných příkazů přidat zobrazení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. V **přidat zobrazení** dialogovém okně aktualizací následujících hodnot:
   
   * Do pole **Název zobrazení** zadejte ***Index***.
   * V poli **Šablona** vyberte ***Seznam***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
     
   ![Snímek obrazovky se zobrazeným dialogovým oknem přidat zobrazení](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Po přidání těchto hodnot, vyberte **přidat** a nechte Visual Studio vytvořte novou šablonu zobrazení. Až to bude hotové, otevře se soubor cshtml, který je vytvořen. Tento soubor v sadě Visual Studio můžete zavřít, jak můžete se vrátit k němu později.

#### <a name="AddNewIndexView"></a>Přidat nové zobrazení položky

Podobně jako způsob vytvoření zobrazení seznamu položek, vytvořit nové zobrazení pro vytváření položek pomocí následujících kroků:

1. Z **Průzkumníku řešení**, klikněte pravým tlačítkem **položky** složky znovu, vyberte **přidat**a pak vyberte **zobrazení**.

1. V **přidat zobrazení** dialogovém okně aktualizací následujících hodnot:
   
   * Do pole **Název zobrazení** zadejte ***Create***.
   * V poli **Šablona** vyberte ***Create***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
   * Vyberte **Přidat**.
   
#### <a name="AddEditIndexView"></a>Přidání zobrazení upravit položku

A nakonec přidání zobrazení upravit položku pomocí následujících kroků:

1. Z **Průzkumníku řešení**, klikněte pravým tlačítkem **položky** složky znovu, vyberte **přidat**a pak vyberte **zobrazení**.

1. V dialogovém okně **Přidat zobrazení** proveďte následující akce:
   
   * Do pole **Název zobrazení** zadejte ***Edit***.
   * V poli **Šablona** vyberte ***Edit***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
   * Vyberte **Přidat**.

Až to uděláte, zavřete všechny dokumenty cshtml v sadě Visual Studio, protože později vrátit k těmto zobrazením.

## <a name="connect-to-cosmosdb"></a>Krok 5: Připojení ke službě Azure Cosmos DB 

Teď, když standardní MVC náležitosti z, přejdeme na přidávání kódu se připojte ke službě Azure Cosmos DB a provádění operací CRUD. 

### <a name="perform-crud-operations"></a> Provádění operací CRUD u dat

První věc, kterou zde potřeba udělat, je přidat třídu, která obsahuje logiku pro připojení k a používat službu Azure Cosmos DB. Pro tento kurz zapouzdřeme tuto logiku do třídy s názvem `CosmosDBService` a z rozhraní s názvem. `ICosmosDBService` Tato služba provádí operace CRUD a čtení kanálu, jako je například výpis neúplných položek, vytváření, úpravy a odstraňování položek. 

1. Z **Průzkumníka řešení**, vytvořte novou složku v rámci projektu s názvem **služby**.

1. Klikněte pravým tlačítkem myši **služby** složky, vyberte **přidat**a pak vyberte **třídy**. Pojmenujte novou třídu **CosmosDBService** a vyberte **Přidat**.

1. Do třídy **CosmosDBService** přidejte následující kód a nahraďte kód v tomto souboru následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Opakujte kroky 2-3, ale tentokrát pro třídu s názvem **ICosmosDBService**a přidejte následující kód:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Předchozí kód obdrží `CosmosClient` jako součást konstruktoru. Po ASP.NET Core kanálu Musíme přejít na **Startup.cs** projektu a inicializovat klienta na základě konfigurace jako instance typu Singleton, kterou chcete vložit prostřednictvím [Injektáže závislosti](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). V obslužné rutině **ConfigureServices** definujeme:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. V rámci stejného souboru definujeme naši pomocnou metodu **InitializeCosmosClientInstanceAsync**, která načte konfiguraci a inicializuje klienta.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. Konfigurace je definována v souboru **appSettings. JSON** projektu. Otevřete ho a přidejte oddíl s názvem **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Když teď aplikaci spustíte, ASP.NET Core kanál vytvoří instanci **CosmosDbService** a udržuje jednu instanci jako typ singleton; Když se **ItemController** používá ke zpracování požadavků na straně klienta, obdrží tuto jedinou instanci a bude ji moct použít k provádění operací CRUD.

Pokud tento projekt sestavíte a spustíte nyní, měla by se zobrazit něco, co vypadá takto:

![Snímek obrazovky webové aplikace seznamu úkolů vytvořené pomocí tohoto databázového kurzu](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Krok 6: Místní spuštění aplikace

Chcete-li aplikaci otestovat na místním počítači, použijte následující kroky:

1. Stisknutím klávesy F5 v sadě Visual Studio k vytvoření aplikace v režimu ladění. Po sestavení aplikace by se měl spustit prohlížeč se stránkou s prázdnou mřížkou, kterou jsme viděli dříve:
   
   ![Snímek webové aplikace seznamu úkolů vytvořené v tomto kurzu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Klikněte na odkaz **Vytvořit nový** a do polí **Název** a **Popis** zadejte hodnoty. Nechte **dokončeno** zaškrtávací políčko nevybranou jinak nová položka přidána ve stavu dokončení a nezobrazí v úvodním seznamu.
   
3. Klikněte na tlačítko **vytvořit** a budete přesměrováni zpět **Index** zobrazení a položky se zobrazí v seznamu. Můžete přidat několik více položek do seznamu úkolů.

    ![Snímek obrazovky s zobrazením indexu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Klikněte na **Upravit** vedle **položky** v seznamu. Přejdete na zobrazení **Upravit**, kde můžete aktualizovat jakoukoli vlastnost objektu, včetně příznaku **Dokončeno**. Pokud označíte příznak **dokončení** a kliknete na **Uložit**, **položka** se v seznamu zobrazí jako dokončená.
   
   ![Snímek obrazovky zobrazení indexu se zaškrtnutým políčkem dokončeno](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. Můžete kdykoli ověřit stav dat ve službě Azure Cosmos DB pomocí [Průzkumníka Cosmos](https://cosmos.azure.com) nebo Průzkumník dat emulátoru Azure Cosmos DB.

6. Až budete s testováním aplikace hotovi, stiskněte Ctrl+F5, aby se ukončilo ladění aplikace. Jste připraveni aplikaci nasadit!

## <a name="deploy-the-application-to-azure"></a>Krok 7: Nasazení aplikace 
Nyní, když je aplikace dokončena a správně funguje se službou Azure Cosmos DB, nasadíme tuto webovou aplikaci ve službě Azure App Service.  

1. Tuto aplikaci publikovat, klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.
   
2. V dialogovém okně **publikovat** vyberte možnost **App Service**, vyberte **vytvořit novou** a vytvořte App Service profil, nebo zvolte **možnost vybrat existující** a použijte stávající profil.

3. Pokud máte existující profil Azure App Service, vyberte **předplatné** z rozevíracího seznamu. Použití **zobrazení** filtr seřadíte položky podle skupiny prostředků nebo typ prostředku. Dále vyhledejte požadované služby Azure App Service a vyberte **OK**.
   
   ![Dialogové okno App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Chcete-li vytvořit nový profil Azure App Service, klikněte na **Vytvořit nový** v dialogovém okně **Publikovat**. V **vytvořit službu App Service** dialogové okno, zadejte název vaší webové aplikace a odpovídající předplatné, skupinu prostředků a plán služby App Service a pak vyberte **vytvořit**.

   ![Dialogové okno Vytvořit plán App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Během několika sekund Visual Studio publikuje vaši webovou aplikaci a spustí prohlížeč, kde vidíte svůj projekt spuštěný v Azure!

## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili, jak vytvořit webovou aplikaci ASP.NET Core MVC, která má přístup k datům uloženým v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

* [Další informace o dělení dat v Azure Cosmos DB](./partitioning-overview.md)
* [Další informace o tom, jak provádět pokročilejší dotazy v Azure Cosmos DB](./how-to-sql-query.md)
* [Další informace o modelování dat v pokročilejším scénáři](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
