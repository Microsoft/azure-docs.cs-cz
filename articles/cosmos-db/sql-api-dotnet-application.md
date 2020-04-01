---
title: ASP.NET kurz webové aplikace Core MVC pomocí Azure Cosmos DB
description: ASP.NET kurz Core MVC k vytvoření webové aplikace MVC pomocí Azure Cosmos DB. Budete ukládat JSON a přístup k datům z aplikace todo hostované ve službě Azure App Service – kurz ASP NET Core MVC krok za krokem.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274079"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Kurz: Vývoj ASP.NET základní webové aplikace MVC s Azure Cosmos DB pomocí .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Tento kurz ukazuje, jak používat Azure Cosmos DB k ukládání a přístupu k datům z ASP.NET aplikace MVC, která je hostovaná v Azure. V tomto kurzu použijete .NET SDK V3. Následující obrázek znázorňuje webovou stránku, kterou vytvoříte pomocí ukázky v tomto článku:

![Snímek obrazovky s webovou aplikací Todo list MVC vytvořenou tímto kurzem - Kurz ASP NET Core MVC krok za krokem](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Pokud nemáte čas na dokončení kurzu, můžete si stáhnout kompletní ukázkový projekt z [GitHubu][GitHub].

Tento kurz zahrnuje:

> [!div class="checklist"]
>
> * Vytvoření účtu Azure Cosmos
> * Vytvoření aplikace ASP.NET Core MVC
> * Připojení aplikace k Azure Cosmos DB
> * Provádění operací vytváření, čtení, aktualizace a odstraňování dat (CRUD)

> [!TIP]
> Tento kurz předpokládá, že máte předchozí zkušenosti s používáním ASP.NET Core MVC a Azure App Service. Pokud jste novým ASP.NET Core nebo [požadované nástroje](#prerequisites), doporučujeme stáhnout kompletní ukázkový projekt z [GitHubu][GitHub], přidejte požadované balíčky NuGet a spusťte jej. Po sestavení projektu, můžete zkontrolovat tento článek získat přehled o kódu v kontextu projektu.

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky

Než budete postupujte podle pokynů v tomto článku, ujistěte se, že máte následující zdroje:

* Aktivní účet Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Všechny snímky obrazovky v tomto článku jsou z Microsoft Visual Studio Community 2019. Pokud používáte jinou verzi, obrazovky a možnosti se nemusí zcela shodovat. Řešení by mělo fungovat, pokud splňujete požadavky.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Krok 1: Vytvoření účtu Azure Cosmos

Začněme vytvořením účtu Azure Cosmos. Pokud už máte účet SQL API Azure Cosmos DB nebo pokud používáte emulátor Azure Cosmos DB, přejděte na [krok 2: Vytvořte novou ASP.NET aplikaci MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

V další části vytvoříte novou aplikaci ASP.NET Core MVC.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Krok 2: Vytvoření nové aplikace ASP.NET Core MVC

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

1. V **okně Vytvořit nový projekt**vyhledejte a vyberte ASP.NET základní **webovou aplikaci** pro c#. Pokračujte výběrem tlačítka **Next** (Další).

   ![Vytvoření nového projektu webové aplikace ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. V **části Konfigurace nového projektu**pojmenujte *projekt* a vyberte **Vytvořit**.

1. V **pořizování nové ASP.NET základní webové aplikace**zvolte **Webová aplikace (Model-View-Controller).** **Chcete-li pokračovat,** vyberte možnost Vytvořit.

   Visual Studio vytvoří prázdnou aplikaci MVC.

1. **Chcete-li** > spustit ASP.NET aplikaci místně, vyberte možnost Ladění**zahájit ladění** nebo F5.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Krok 3: Přidání balíčku Azure Cosmos DB NuGet do projektu

Teď, když máme většinu ASP.NET core MVC framework kód, který potřebujeme pro toto řešení, přidáme balíčky NuGet potřebné pro připojení k Azure Cosmos DB.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**.

1. Ve **Správci balíčků NuGet**vyhledejte a vyberte **Microsoft.Azure.Cosmos**. Vyberte **Install** (Nainstalovat).

   ![Instalace balíčku NuGet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio stáhne a nainstaluje balíček Azure Cosmos DB a jeho závislosti.

   Balíček Můžete také nainstalovat balíček NuGet pomocí **konzoly Správce balíčků.** Chcete-li tak učinit, vyberte **možnost Nástroje** > **NuGet Správce** > **balíčků Správce balíčků Konzola**. Na řádku zadejte následující příkaz:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Krok 4: Nastavení aplikace ASP.NET Core MVC

Nyní přidáme modely, zobrazení a řadiče do této aplikace MVC.

### <a name="add-a-model"></a><a name="add-a-model"></a>Přidání modelu

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku **Modely** a vyberte **přidat** > **třídu**.

1. V **položce Přidat novou položku**pojmenujte novou třídu *Item.cs* a vyberte **Přidat**.

1. Nahraďte obsah *třídy Item.cs* následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB používá JSON k přesunu a ukládání dat. Atribut můžete `JsonProperty` použít k řízení, jak JSON serializuje a deserializuje objekty. Třída `Item` demonstruje `JsonProperty` atribut. Tento kód řídí formát názvu vlastnosti, která přejde do JSON. Také přejmenuje vlastnost `Completed`.NET .

### <a name="add-views"></a><a name="add-views"></a>Přidání zobrazení

Dále vytvoříme následující tři pohledy.

* Přidání zobrazení položky seznamu
* Přidání nového zobrazení položky
* Přidání zobrazení upravit položku

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Přidání zobrazení položky seznamu

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku **Zobrazení** a vyberte **přidat** > **novou složku**. Pojmenujte složku *Item*.

1. Klepněte pravým tlačítkem myši na prázdnou složku **Položka** a pak vyberte **přidat** > **zobrazení**.

1. V **doplňku Add MVC View**zadejte následující hodnoty:

   * Do **pole Zobrazit název**zadejte *rejstřík*.
   * V **části Šablona**vyberte **Možnost Seznam**.
   * Ve **třídě Model**vyberte **položku (todo. modely)**.
   * Vyberte **Použít stránku rozložení** a zadejte *~/Zobrazení/Sdílené/_Layout.cshtml*.

   ![Snímek obrazovky s dialogovým oknem Přidat zobrazení MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Po přidání těchto hodnot vyberte **Přidat** a nechat Visual Studio vytvořit nové zobrazení šablony.

Po dokončení Visual Studio otevře soubor *cshtml,* který vytvoří. Tento soubor můžete zavřít v sadě Visual Studio. Vrátíme se k tomu později.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Přidání nového zobrazení položky

Podobně jako při vytváření zobrazení pro položky seznamu vytvořte nové zobrazení pro vytváření položek pomocí následujících kroků:

1. V **Průzkumníku řešení**klepněte znovu pravým tlačítkem myši na složku **Položka** a vyberte **přidat** > **zobrazení**.

1. V **zobrazení Přidat MVC**proveďte následující změny:

   * Do **pole Název zobrazení**zadejte *vytvořit*.
   * V **části Šablona**vyberte **Vytvořit**.
   * Ve **třídě Model**vyberte **položku (todo. modely)**.
   * Vyberte **Použít stránku rozložení** a zadejte *~/Zobrazení/Sdílené/_Layout.cshtml*.
   * Vyberte **Přidat**.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Přidání zobrazení upravit položku

A nakonec přidejte zobrazení pro úpravu položky pomocí následujících kroků:

1. V **Průzkumníku řešení**klepněte znovu pravým tlačítkem myši na složku **Položka** a vyberte **přidat** > **zobrazení**.

1. V **zobrazení Přidat MVC**proveďte následující změny:

   * Do pole **Název zobrazení** zadejte *Edit*.
   * V poli **Šablona** vyberte **Edit**.
   * V poli **Třída modelu** vyberte **Položka (todo.Models)**.
   * Vyberte **Použít stránku rozložení** a zadejte *~/Zobrazení/Sdílené/_Layout.cshtml*.
   * Vyberte **Přidat**.

Po dokončení těchto kroků zavřete všechny dokumenty *cshtml* v sadě Visual Studio, jakmile se později vrátíte k těmto zobrazením.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Deklarování a inicializaci služeb

Nejprve přidáme třídu, která obsahuje logiku pro připojení a použití Azure Cosmos DB. V tomto kurzu budeme zapouzdřit tuto `CosmosDBService` logiku do `ICosmosDBService`třídy s názvem a rozhraní s názvem . Tato služba provádí operace CRUD. Čte také operace informačního kanálu, jako je výpis neúplných položek, vytváření, úpravy a odstraňování položek.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **novou složku**. Pojmenujte složku *Services*.

1. Klepněte pravým tlačítkem myši na složku **Služby** a vyberte **přidat** > **třídu**. Pojmenujte novou třídu *CosmosDBService* a vyberte **přidat**.

1. Obsah *CosmosDBService.cs* se nahradí následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klepněte pravým tlačítkem myši na složku **Služby** a vyberte **přidat** > **třídu**. Pojmenujte novou třídu *ICosmosDBService* a vyberte **přidat**.

1. Přidejte do *třídy ICosmosDBService* následující kód:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Otevřete *soubor Startup.cs* v řešení `ConfigureServices` a nahraďte metodu:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    Kód v tomto kroku inicializuje klienta na základě konfigurace jako instance singleton, která má být vložena prostřednictvím [vkládání závislostí v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. Ve stejném souboru přidejte následující metodu **InitializeCosmosClientInstanceAsync**, která přečte konfiguraci a inicializuje klienta.

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Definujte konfiguraci v souboru *appsettings.json* projektu, jak je znázorněno v následujícím fragmentu:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Přidání kontroleru

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku **Řadiče** a vyberte **přidat** > **řadič**.

1. V **možnosti Přidat přiřazovat hlasovací zařízení**vyberte **položku MVC Controller – Empty** a vyberte **Přidat**.

   ![Vyberte řadič MVC – prázdný v přidat šaškárna](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Pojmenujte nový řadič *ItemController*.

1. Nahraďte obsah *ItemController.cs* následujícím kódem:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

**ValidateAntiForgeryToken** atribut slouží zde k ochraně této aplikace proti útokům padělání požadavků mezi lokalitami. Vaše názory by měly fungovat s tímto tokenem proti padělání. Další informace a příklady naleznete [v tématu Zabránění útokům na základě požadavků na příčné stránky (CSRF) v ASP.NET aplikace MVC][Preventing Cross-Site Request Forgery]. Zdrojový kód dostupný na [GitHubu][GitHub] má toto plně implementováno.

Používáme také **Bind** atribut na parametr metody k ochraně proti útokům over-posting. Další informace naleznete v [tématu Kurz: Implementace funkcí CRUD s entity frameworku v ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Krok 5: Spuštění aplikace místně

Chcete-li aplikaci otestovat v místním počítači, postupujte takto:

1. Stisknutím klávesy F5 v sadě Visual Studio vytvořte aplikaci v režimu ladění. Po sestavení aplikace by se měl spustit prohlížeč se stránkou s prázdnou mřížkou, kterou jsme viděli dříve:

   ![Snímek obrazovky webové aplikace seznamu úkolů vytvořené v tomto kurzu](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Pokud se aplikace místo toho otevře `/Item` na domovskou stránku, připojte adresu URL.

1. Vyberte odkaz **Vytvořit nový** a přidejte hodnoty do polí **Název** a **Popis.** Ponechejte **políčko Dokončeno** nezaškrtnuté. Pokud ji vyberete, aplikace přidá novou položku v dokončeném stavu. Položka se již nezobrazuje v počátečním seznamu.

1. Vyberte **Vytvořit**. Aplikace vás odešle zpět do zobrazení **Rejstřík** a vaše položka se zobrazí v seznamu. Do seznamu úkolů můžete přidat několik **dalších** položek.

    ![Snímek obrazovky se zobrazením rejstříku](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. V seznamu vyberte **Upravit** **vedle položky.** Aplikace otevře zobrazení **Upravit,** kde můžete aktualizovat libovolnou vlastnost objektu, včetně příznaku **Dokončeno.** Pokud vyberete **Dokončeno** a **vyberete Uložit**, aplikace zobrazí **položku** jako dokončenou v seznamu.

   ![Snímek obrazovky se zobrazením Rejstřík se zaškrtnutým políčkem Dokončeno](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Ověřte stav dat ve službě Azure Cosmos DB pomocí [aplikace Cosmos Explorer](https://cosmos.azure.com) nebo Průzkumníku dat emulátoru Azure Cosmos DB.

1. Po otestování aplikace vyberte Ctrl+F5, abyste přestali aplikaci ladit. Jste připraveni aplikaci nasadit!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Krok 6: Nasazení aplikace

Nyní, když je aplikace dokončena a správně funguje se službou Azure Cosmos DB, nasadíme tuto webovou aplikaci ve službě Azure App Service.  

1. Chcete-li tuto aplikaci publikovat, klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte příkaz **Publikovat**.

1. V **pole Vyberte cíl publikování**vyberte App **Service**.

1. Pokud chcete použít existující profil služby App Service, zvolte **Vybrat existující**a pak vyberte **Publikovat**.

1. Ve **službě App Service**vyberte **předplatné**. Pomocí filtru **Zobrazení** můžete řadit podle skupiny prostředků nebo typu prostředku.

1. Najděte svůj profil a pak vyberte **OK**. Další hledání požadované služby Azure App Service a vyberte **OK**.

   ![Dialogové okno App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Další možností je vytvoření nového profilu:

1. Stejně jako v předchozím postupu klepněte pravým tlačítkem myši na projekt v **Průzkumníku řešení** a vyberte **publikovat**.
  
1. V **pole Vyberte cíl publikování**vyberte App **Service**.

1. V **pole Vyberte cíl publikování**vyberte Vytvořit **nový** a vyberte **Publikovat**.

1. Ve **službě App Service**zadejte název webové aplikace a příslušný předplatný, skupinu prostředků a plán hostování a pak vyberte **Vytvořit**.

   ![Dialogové okno Vytvořit plán App Service v sadě Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

Během několika sekund Visual Studio publikuje vaši webovou aplikaci a spustí prohlížeč, kde můžete vidět váš projekt běží v Azure!

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit ASP.NET webové aplikace Core MVC. Vaše aplikace může přistupovat k datům uloženým v Azure Cosmos DB. Nyní můžete pokračovat s těmito prostředky:

* [Dělení ve službě Azure Cosmos DB](./partitioning-overview.md)
* [Začínáme s dotazy SQL](./how-to-sql-query.md)
* [Modelování a dělení dat ve službě Azure Cosmos DB s využitím příkladu z reálného světa](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
