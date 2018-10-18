---
title: 'Kurz: rozhraní API Bingu pro vyhledávání zpráv, C#'
titleSuffix: Azure Cognitive Services
description: Připojte se k rozhraní API Bingu pro vyhledávání zpráv z webové aplikace ASP.NET Core.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: f1f5c590216975ce6b0813da6d9d98279d591454
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804340"
---
# <a name="tutorial-connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Kurz: Připojení k rozhraní API Bingu pro vyhledávání zpráv pomocí připojených služeb v sadě Visual Studio

S využitím rozhraní API Bingu pro vyhledávání zpráv můžete aplikacím a službám umožnit využívat výkon vyhledávacího webu, který je bez reklam a omezený na web. Rozhraní API Bingu pro vyhledávání zpráv je jednou z vyhledávacích služeb dostupných v rámci služeb Cognitive Services.

Tento článek obsahuje podrobnosti o používání funkce připojené služby sady Visual Studio pro rozhraní API Bingu pro vyhledávání zpráv. Tato možnost je dostupná v sadě Visual Studio 2017 15.7 nebo novější s nainstalovaným rozšířením Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- Sada Visual Studio 2017 verze 15.7 s nainstalovanou sadou funkcí Vývoj pro web. [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Přidání podpory rozhraní API Bingu pro vyhledávání zpráv do projektu

1. Vytvořte nový webový projekt ASP.NET Core s názvem MyWebApplication. Použijte šablonu projektu **Webová aplikace (Model-View-Controller)** a ponechte výchozí nastavení. Je důležité projekt pojmenovat MyWebApplication, aby se při kopírování kódu do projektu shodovaly obory názvů. 

1. V **Průzkumníku řešení** zvolte **Přidat**  > **Připojená služba**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.

   ![Snímek obrazovky s položkou nabídky Přidat připojenou službu](../media/vs-common/Connected-Service-Menu.PNG)

1. V nabídce dostupných služeb zvolte **Bring Intelligent Search To Your Apps** (Integrace inteligentního vyhledávání do aplikací).

   ![Snímek obrazovky se seznamem připojených služeb](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Pokud jste přihlášení k sadě Visual Studio a máte ke svému účtu přidružené předplatné Azure, zobrazí se stránka s rozevíracím seznamem vašich předplatných. Vyberte předplatné, které chcete použít, a pak zvolte název rozhraní API Bingu pro vyhledávání zpráv. Můžete také zvolit **Upravit** a upravit automaticky vygenerovaný název.

   ![Snímek obrazovky s poli Předplatné a Název](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Zvolte skupinu prostředků a cenovou úroveň.

   ![Snímek obrazovky s poli Skupina prostředků a Cenová úroveň](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Pokud chcete zobrazit další podrobnosti o cenových úrovních, vyberte **Zkontrolovat ceny**.

1. Výběrem možnosti **Přidat** přidejte podporu této připojené služby.
   Sada Visual Studio upraví projekt tak, že přidá balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení k rozhraní API Bingu pro vyhledávání zpráv. Ve výstupu se zobrazí protokol, který ukazuje, co se s projektem děje. Mělo by se zobrazit něco podobného:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Soubor appsettings.json teď obsahuje následující nové nastavení:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Přidání vyhledávacích funkcí do webové stránky s využitím rozhraní API Bingu pro vyhledávání zpráv

Do projektu jste přidali podporu rozhraní API Bingu pro vyhledávání zpráv. Tady je postup použití rozhraní API k přidání inteligentního vyhledávání do webové stránky.

1.  V souboru *Startup.cs* přidejte do metody `ConfigureServices` volání `IServiceCollection.AddSingleton`. Tím se vytvoří objekt konfigurace obsahující klíčová nastavení, která jsou k dispozici pro kód ve vašem projektu.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Do složky **Models** přidejte nový soubor třídy *BingNewsModel.cs*. Pokud jste projekt pojmenovali jinak, místo MyWebApplication použijte obor názvů vlastního projektu. Obsah souboru nahraďte následujícím kódem:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Tento model slouží k ukládání výsledků volání služby Vyhledávání zpráv Bingu.
 
1. Do složky **Controllers** přidejte nový soubor třídy *IntelligentSearchController.cs*. Obsah souboru nahraďte následujícím kódem:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Konstruktor v tomto kódu nastaví objekt konfigurace, který obsahuje vaše klíče. Metoda trasy `Search` je prostým přesměrováním do funkce `BingSearchResult`. Tato funkce volá metodu `GetNewsSearchClient` a získá objekt klienta `NewsSearchAPI`.  Objekt klienta `NewsSearchAPI` obsahuje metodu `SearchAsync`, která ve skutečnosti volá službu a vrací výsledky v modelu `SearchResult`, který jste právě vytvořili. 

1. Přidejte třídu `MyHandler`, na kterou se odkazovalo v předchozím kódu. Tato třída deleguje asynchronní volání vyhledávací služby do základní třídy `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Přidejte podporu odesílání hledaných termínů a zobrazení výsledků tím, že ve složce **Views** vytvoříte novou složku **IntelligentSearch**. Do této nové složky přidejte zobrazení *BingSearchResult.cshtml*. Do tohoto souboru zkopírujte následující kód:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Spusťte místně webovou aplikaci, zadejte adresu URL stránky, kterou jste právě vytvořili (/IntelligentSearch/BingSearchResult), a pomocí tlačítka Search (Hledat) odešlete požadavek hledání.

   ![Snímek obrazovky s výsledky hledání zpráv Bingu](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupinu prostředků nepotřebujete, můžete ji odstranit. Tím se odstraní služba Cognitive Services a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do vyhledávacího pole v horní části portálu zadejte název vaší skupiny prostředků. Vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **Zadejte název skupiny prostředků** zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraní API Bingu pro vyhledávání zpráv najdete v tématu [Co je Vyhledávání zpráv Bingu?](index.yml).
