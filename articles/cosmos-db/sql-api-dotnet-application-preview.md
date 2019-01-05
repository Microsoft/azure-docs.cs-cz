---
title: Kurz pro vývoj webové aplikace ASP.NET MVC pomocí služby Azure Cosmos DB pomocí rozhraní .net ve verzi preview SDK.
description: Tento kurz popisuje, jak vytvořit ASP .net MVC webovou aplikaci pomocí služby Azure Cosmos DB. Budete ukládat a přistupovat k datům JSON z aplikace seznamu úkolů hostované v Azure.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: e3ad852246b4b78d5ed7ac938348e59e9b7e6ce0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037119"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Kurz: Vývoj webové aplikace ASP.NET MVC pomocí služby Azure Cosmos DB pomocí rozhraní .net ve verzi preview SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Rozhraní .NET ve verzi preview](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


V tomto kurzu se dozvíte, jak pomocí služby Azure Cosmos DB k ukládání a přístup k datům z aplikace ASP.NET MVC, která je hostována v Azure. V tomto kurzu pomocí .net SDK V3, který je v současné době ve verzi preview. Následující obrázek ukazuje webové stránky, které vytvoříte pomocí ukázky v tomto článku:
 
![Snímek obrazovky webové aplikace MVC pro seznam úkolů vytvořené v tomto kurzu – podrobný kurz ASP.NET MVC](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Pokud nemáte čas k dokončení tohoto kurzu, si můžete stáhnout úplný ukázkový projekt z [Githubu][GitHub]. 

Tento kurz zahrnuje:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos
> * Vytvoření aplikace ASP.NET MVC
> * Připojení aplikace ke službě Azure Cosmos DB 
> * Provádění operací CRUD u dat

> [!TIP]
> V tomto kurzu se předpokládá, že již máte zkušenosti s používáním ASP.NET MVC a Webů Azure. Pokud jste ještě na technologii ASP.NET nebo [požadované nástroje](#prerequisites), doporučujeme stáhnout úplný ukázkový projekt z [Githubu][GitHub], přidat požadované balíčky NuGet a spusťte ho. Po sestavení projektu, můžete zkontrolovat tohoto článku, abyste lépe porozuměli v kontextu projektu kódu.

## <a name="prerequisites"></a>Požadavky 

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte následující prostředky:

* **Aktivní účet Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* Microsoft Azure SDK pro .NET pro Visual Studio 2017, k dispozici prostřednictvím Instalačního programu pro Visual Studio.

Všechny snímky obrazovky v tomto článku byly pořízeny pomocí Microsoft Visual Studio Community 2017. Pokud je systém konfigurován s jinou verzí, je možné, že vaše obrazovky a možnosti nemusí mírně lišit, ale pokud splníte výše uvedené požadavky tohoto řešení by mělo fungovat.

## <a name="create-an-azure-cosmos-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos

Začněme vytvořením účtu služby Azure Cosmos. Pokud už máte účet rozhraní SQL API služby Azure Cosmos DB nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [vytvořit novou aplikaci ASP.NET MVC](#create-a-new-mvc-application) oddílu.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

V další části můžete vytvořit novou aplikaci ASP.NET MVC. 

## <a name="create-a-new-mvc-application"></a>Krok 2: Vytvoření nové aplikace ASP.NET MVC

1. V sadě Visual Studio z **souboru** nabídce zvolte **nový**a pak vyberte **projektu**. Zobrazí se dialogové okno **Nový projekt**.

2. V **nový projekt** okna rozbalte **nainstalováno** šablony, **Visual C#** , **webové**a pak vyberte **ASP. NET webovou aplikaci**. 

   ![Vytvořit nový projekt webové aplikace ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. Do pole **Název** zadejte název projektu. Tento kurz používá název todo. Pokud se rozhodnete použít něco jiného, pak bez ohledu na to v tomto kurzu bude zmíněn obor názvů todo, upravte poskytnuté ukázky kódu pro používaly vámi zvolený název aplikace. 

4. Vyberte **Procházet** přejděte do složky, ve kterém chcete vytvořit projekt a klikněte na tlačítko **rozhraní .net framework 4.6.1** nebo vyšší. Vyberte **OK**. 

5. Zobrazí se dialogové okno **Nová webová aplikace ASP.NET**. V podokně šablon vyberte **MVC**.

6. Vyberte **OK** a nechte Visual Studio generování uživatelského rozhraní kolem prázdnou šablonu ASP.NET MVC. 

7. Jakmile Visual Studio dokončí vytváření standardní aplikace MVC, máte prázdnou aplikaci ASP.NET, které můžete spustit místně.

## <a name="add-nuget-packages"></a>Krok 3: Přidejte do projektu balíček NuGet pro Azure Cosmos DB

Když teď máme většinu kódu ASP.NET MVC framework, kterou potřebujeme pro toto řešení, přidáme balíčky NuGet, které jsou potřebné pro připojení ke službě Azure Cosmos DB.

1. Sada Azure Cosmos DB .NET SDK se připravuje a distribuuje jako balíček NuGet. Chcete-li získat balíček NuGet v sadě Visual Studio, používat Správce balíčků NuGet v sadě Visual Studio kliknutím pravým tlačítkem myši na projekt v **Průzkumníka řešení** a pak vyberte **spravovat balíčky NuGet**.
   
   ![Snímek obrazovky možností nabídky po kliknutí pravým tlačítkem na projekt webové aplikace v Průzkumníkovi řešení se zvýrazněnou možností Spravovat balíčky NuGet.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Zobrazí se dialogové okno **Správa balíčků NuGet**. Nuget **Procházet** zadejte **Microsoft.Azure.Cosmos**. Z výsledků nainstalujte **Microsoft.Azure.Cosmos** 3.0.0.1-preview verze. Stáhne a nainstaluje balíček služby Azure Cosmos DB a jeho závislosti, jako je například Newtonsoft.Json. Vyberte **OK** v **ve verzi Preview** okně a **souhlasím** v **přijetí licence** okno pro dokončení instalace.
   
   Alternativně můžete použít Konzola správce balíčků pro instalaci balíčku NuGet. Provedete to tak, na **nástroje** nabídce vyberte možnost **Správce balíčků NuGet**a pak vyberte **Konzola správce balíčků**. Do příkazového řádku zadejte následující příkaz:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Po instalaci balíčku řešení sady Visual Studio by měl obsahovat dva nové odkazy knihovny Microsoft.Azure.Cosmos.Client a Newtonsoft.Json.
  
## <a name="set-up-the-mvc-application"></a>Krok 4: Nastavení aplikace ASP.NET MVC

Teď k této aplikaci MVC přidejme modely, zobrazení a kontrolery:

* [Přidání modelu](#add-a-model)
* [Přidání kontroleru](#add-a-controller)
* [Přidání zobrazení](#add-views)

### <a name="add-a-model"></a> Přidání modelu

1. Z **Průzkumníku řešení**, klikněte pravým tlačítkem myši **modely** složky, vyberte **přidat**a pak vyberte **třídy**. Zobrazí se dialogové okno **Přidat novou položku**.

1. Pojmenujte novou třídu **TodoItem.cs** a vyberte **přidat**. 

1. Dále nahraďte kód ve třídě "Todoitem" následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Data uložená ve službě Azure Cosmos DB se přenesou a uloží jako dokumenty JSON. Chcete-li řídit způsob, jakým se objekty serializují a deserializují technologií JSON.NET, můžete použít **JsonProperty** atributu, jak je ukázáno v **TodoItem** třídy, které jste vytvořili. Nejenže můžete určovat formát názvu vlastnosti, přejde do formátu JSON, můžete také přejmenovat vlastnosti .NET stejně jako **popis** vlastnost. 

### <a name="add-a-controller"></a>Přidání kontroleru

1. Z **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče** složky, vyberte **přidat**a pak vyberte **řadič**. Zobrazí se dialogové okno **Přidat vygenerované uživatelské rozhraní**.

1. Vyberte **kontroler MVC 5 – prázdný** a vyberte **přidat**.

   ![Snímek obrazovky dialogového okna Přidat vygenerované uživatelské rozhraní se zvýrazněnou možností Kontroler MVC 5 – prázdný](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Pojmenujte nový kontroler, ** ItemController a nahraďte kód v tomto souboru následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   **ValidateAntiForgeryToken** atributů se tady používá k pomohl zabezpečit tuto aplikace před útoky proti padělání žádosti více webů. Existuje více než jen přidat tento atribut, zobrazení by měla fungovat s tímto tokenem proti padělání také. Další informace k tomuto tématu a příklady toho, jak toto správně implementovat, najdete v článku [brání padělání žádosti více webů][Preventing Cross-Site Request Forgery]. Zdrojový kód dostupný na [GitHubu][GitHub] má toto plně implementováno.
   
   Použijeme také **svázat** atribut u parametru metody a pomáhá chránit před útoky typu overpost over-pass-the. Další podrobnosti najdete v tématu [základní operace CRUD v ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Přidání zobrazení

V dalším kroku vytvoříme následující tři zobrazení: 

* [Přidat položky zobrazení seznamu](#AddItemIndexView).
* [Přidat nové položky zobrazení](#AddNewIndexView).
* [Přidání zobrazení upravit položku](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Přidat položky zobrazení seznamu

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na prázdnou **položky** složku, která sadě Visual Studio vytvoří za vás během přidávání  **ItemController** dříve, klikněte na tlačítko **přidat**a potom klikněte na tlačítko **zobrazení**.
   
   ![Snímek obrazovky Průzkumníka řešení, na kterém se zobrazuje složka Položka vytvořená nástrojem Visual Studio se zvýrazněnými příkazy Přidat zobrazení](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. V **přidat zobrazení** dialogovém okně aktualizací následujících hodnot:
   
   * Do pole **Název zobrazení** zadejte ***Index***.
   * V poli **Šablona** vyberte ***Seznam***.
   * V poli **Třída modelu** vyberte ***Položka (todo.Models)***.
   * Do pole stránky rozložení zadejte ***~/Views/Shared/_Layout.cshtml***.
     
   ![Snímek obrazovky ukazující dialogové okno Přidat zobrazení](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

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

První věc, kterou zde potřeba udělat, je přidat třídu, která obsahuje logiku pro připojení k a používat službu Azure Cosmos DB. Pro účely tohoto kurzu zapouzdříme tuto logiku do třídy nazvané TodoItemService.cs. Tento kód načte formulář hodnoty koncového bodu služby Azure Cosmos DB konfigurační soubor a provádí operace CRUD, jako je výpis neúplných položek, vytváření, úpravy a odstraňování položek. 

1. Z **Průzkumníka řešení**, vytvořte novou složku v rámci projektu s názvem **služby**.

1. Klikněte pravým tlačítkem myši **služby** složky, vyberte **přidat**a pak vyberte **třídy**. Pojmenujte novou třídu **TodoItemService** a vyberte **přidat**.

1. Přidejte následující kód, který **TodoItemService** třídy a nahraďte kód v tomto souboru následujícím kódem:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. Předchozí kód čte hodnoty řetězce připojení z konfiguračního souboru. Chcete-li aktualizovat hodnoty připojovacího řetězce účtu Azure Cosmos, otevřete **Web.config** ve vašem projektu a přidejte následující řádky pod `<AppSettings>` části:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Aktualizujte hodnoty koncového bodu a primarykey pomocí hodnot načtených z **klíče** okna na webu Azure Portal. Použít **URI** z okna klíče jako hodnotu tohoto nastavení koncového bodu a **primární klíč**, nebo **sekundární klíč** z okna klíče pro nastavení klíče. To se postará o připojení služby Azure Cosmos DB pro aplikace, nyní Pojďme přidat logiku aplikace.

1. Otevřete **Global.asax.cs** a přidejte následující řádek do metody **Application_Start** 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   V tomto okamžiku řešení mohly k sestavení projektu bez chyb. Při spuštění aplikace, nyní **HomeController** a **Index** by měla otevřít tohoto kontroleru. Toto je výchozí chování projektu šablony MVC, který jsme zvolili na začátku. Změňte směrování v této aplikaci MVC, abychom dané chování upravili.

1. Otevřít ***aplikace\_Start\RouteConfig.cs*** a vyhledejte řádek, počínaje "výchozí hodnoty:" a aktualizujte ji následujícím kódem:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

  Tento kód nyní technologii ASP.NET MVC řekne, že pokud jste nezadali hodnotu v adrese URL pro řízení chování směrování, namísto **Domů**, používá **položky** jako kontroler a **Index**jako zobrazení.

Teď Pokud aplikaci spouštíte, zavolá do vašeho **ItemController** , která volá metodu getitems, pomocí z TodoItemService třídy, které definujete v další části. 

Pokud teď projekt sestavíte a spustíte, mělo by se zobrazit něco přibližně takového.    

![Snímek obrazovky webové aplikace vytvořené v tomto databázovém kurzu](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Krok 6: Místní spuštění aplikace

Chcete-li aplikaci otestovat na místním počítači, použijte následující kroky:

1. Stisknutím klávesy F5 v sadě Visual Studio k vytvoření aplikace v režimu ladění. Po sestavení aplikace by se měl spustit prohlížeč se stránkou s prázdnou mřížkou, kterou jsme viděli dříve:
   
   ![Snímek obrazovky webové aplikace vytvořené v tomto databázovém kurzu](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Klikněte na odkaz **Vytvořit nový** a do polí **Název** a **Popis** zadejte hodnoty. Nechte **dokončeno** zaškrtávací políčko nevybranou jinak nová položka přidána ve stavu dokončení a nezobrazí v úvodním seznamu.
   
3. Klikněte na tlačítko **vytvořit** a budete přesměrováni zpět **Index** zobrazení a položky se zobrazí v seznamu. Můžete přidat několik více položek do seznamu úkolů.

    ![Snímek obrazovky zobrazení pro index](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Klikněte na **Upravit** vedle **položky** v seznamu. Přejdete na zobrazení **Upravit**, kde můžete aktualizovat jakoukoli vlastnost objektu, včetně příznaku **Dokončeno**. Pokud označíte příznak **Dokončeno** a kliknete na **Uložit**, **položka** se odstraní ze seznamu neúplných úkolů.
   
   ![Snímek obrazovky zobrazení Index se zaškrtnutým políčkem Dokončeno](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Až budete s testováním aplikace hotovi, stiskněte Ctrl+F5, aby se ukončilo ladění aplikace. Jste připraveni aplikaci nasadit!

## <a name="deploy-the-application-to-azure"></a>Krok 7: Nasazení aplikace 
Nyní, když je aplikace dokončena a správně funguje se službou Azure Cosmos DB, nasadíme tuto webovou aplikaci ve službě Azure App Service.  

1. Tuto aplikaci publikovat, klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat**.
   
2. V **publikovat** dialogu **Microsoft Azure App Service**a pak vyberte **vytvořit nový** chcete vytvořit profil služby App Service, nebo zvolte **vyberte Existující** použití existujícího profilu.

3. Pokud máte existující profil Azure App Service, vyberte **předplatné** z rozevíracího seznamu. Použití **zobrazení** filtr seřadíte položky podle skupiny prostředků nebo typ prostředku. Dále vyhledejte požadované služby Azure App Service a vyberte **OK**. 
   
   ![Dialogové okno App Service v sadě Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Chcete-li vytvořit nový profil Azure App Service, klikněte na **Vytvořit nový** v dialogovém okně **Publikovat**. V **vytvořit službu App Service** dialogové okno, zadejte název vaší webové aplikace a odpovídající předplatné, skupinu prostředků a plán služby App Service a pak vyberte **vytvořit**.

   ![Dialogové okno Vytvořit plán App Service v sadě Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Za několik sekund Visual Studio publikuje webovou aplikaci a spustí prohlížeč, ve kterém uvidíte svůj projekt v Azure!

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak sestavit webovou aplikaci, která můžete přístup k datům uloženým ve službě Azure Cosmos DB ASP.NET MVC. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Vytvoření aplikace Java pro přístup k datům uloženým v účtu rozhraní SQL API služby Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
