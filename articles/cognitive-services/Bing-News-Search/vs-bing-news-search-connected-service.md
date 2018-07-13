---
title: Kurzy k Bingu News Search C# | Dokumentace Microsoftu
titleSuffix: Microsoft Cognitive Services
description: Připojení k vyhledávání zpráv Bingu Cognitive Services z webové aplikace ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665217"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Připojení k rozhraní API pro vyhledávání zpráv Bingu pomocí připojené služby v sadě Visual Studio

Pomocí Bingu pro vyhledávání zpráv můžete povolit aplikacím a službám využít výkon modulu vyhledávání bez reklam omezená na webu. Vyhledávání zpráv Bingu je jedním z vyhledávací služby, který je k dispozici pomocí služeb Cognitive Services.

Tento článek obsahuje podrobnosti o použití funkce připojené služby sady Visual Studio pro vyhledávání zpráv Bingu. Možnost je dostupná ve verzi Visual Studio 2017 15.7 nebo novější, s nainstalované rozšíření služeb Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- Visual Studio 2017 verze 15.7, s nainstalovaná úloha vývoj pro Web. [Stáhnout nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Přidání podpory pro váš projekt pro rozhraní API pro vyhledávání zpráv Bingu

1. Vytvořte nový webový projekt ASP.NET Core s názvem MyWebApplication. Použití **webové aplikace (Model-View-Controller)** šablony projektu, s výchozím nastavením. Je důležité pro pojmenování projektu MyWebApplication, takže obor názvů odpovídá, pokud kód zkopírujete do projektu. 

1. V **Průzkumníka řešení**, zvolte **přidat** > **připojenou službu**.
   Zobrazí se stránka připojené služby se službami, které můžete přidat do projektu.

   ![Snímek obrazovky z přidat připojenou službu položky nabídky](../media/vs-common/Connected-Service-Menu.PNG)

1. V nabídce dostupné služby, zvolte **přenést inteligentní vyhledávání pro vaše aplikace**.

   ![Snímek připojené služby](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Pokud jste se přihlašovali do sady Visual Studio a mít předplatné Azure spojené s vaším účtem, se zobrazí stránka s rozevíracím seznamu k vašemu předplatnému. Vyberte předplatné, které chcete použít a pak zvolte název pro rozhraní API pro vyhledávání zpráv Bingu. Můžete také zvolit **upravit** upravit automaticky vygenerovaným názvem.

   ![Snímek obrazovky předplatného a název pole](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Zvolte skupinu prostředků a cenovou úroveň.

   ![Snímek obrazovky skupinu prostředků a cenové úrovně pole](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Pokud chcete další informace o cenových úrovní, vyberte **ceny kontroly**.

1. Zvolte **přidat** přidání podpory pro připojenou službu.
   Visual Studio změní projekt tak, aby přidat balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení k rozhraní API pro vyhledávání zpráv Bingu. Výstup zobrazuje protokol, co se děje do projektu. By měl vypadat přibližně takto:

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

   Soubor appsettings.json nyní obsahuje následující nová nastavení:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Použití rozhraní API pro vyhledávání zpráv Bingu pro vyhledávání na webovou stránku

Teď, když jste přidali podporu pro rozhraní API pro vyhledávání zpráv Bingu do projektu, tady je postup pomocí rozhraní API můžete přidat inteligentní vyhledávání do webové stránky.

1.  V *Startup.cs*v `ConfigureServices` metodu, přidejte volání do `IServiceCollection.AddSingleton`. Díky tomu konfigurační objekt, který obsahuje nastavení klíče, které je k dispozici pro kód ve vašem projektu.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Přidejte nový soubor třídy v rámci **modely** složka s názvem *BingNewsModel.cs*. Pokud váš projekt pojmenujete jinak, použijte místo MyWebApplication názvů vlastním projektu. Nahraďte obsah následujícím kódem:
 
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

   Tento model se používá k uložení výsledků volání služby vyhledávání zpráv Bingu.
 
1. V **řadiče** složky, přidejte nový soubor třídy *IntelligentSearchController.cs*. Nahraďte obsah následujícím kódem:

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

   V tomto kódu nastaví konstruktor objekt konfigurace obsahující vaše klíče. Metoda `Search` trasy je přesměrování na `BingSearchResult` funkce. Volá `GetNewsSearchClient` metodu k získání `NewsSearchAPI` objektu klienta.  `NewsSearchAPI` Obsahuje objekt klienta `SearchAsync` metodu, která ve skutečnosti zavolá službu a vrátí výsledky v `SearchResult` model, který jste právě vytvořili. 

1. Přidat třídu, `MyHandler`, které bylo odkazováno v předchozím kódu. To deleguje asynchronní volání služby vyhledávání se svou základní třídou `DelegatingHandler`.

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

1. Přidání podpory pro odeslání vyhledávání a zobrazení výsledků v **zobrazení** složku, vytvořte novou složku s názvem **IntelligentSearch**. V této nové složky, přidat zobrazení *BingSearchResult.cshtml*. Zkopírujte následující kód:

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

1. Spustit webovou aplikaci místně, zadejte adresu URL pro stránku, kterou jste právě vytvořili (/ IntelligentSearch/BingSearchResult) a odeslat žádost o vyhledávání s použitím tlačítko hledání.

   ![Snímek obrazovky vyhledávání zpráv Bingu výsledky](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud skupina prostředků je už nepotřebujete, můžete ho odstranit. Tím se odstraní služby cognitive Services a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do vyhledávacího pole v horní části portálu zadejte název vaší skupiny prostředků. Vyberte skupinu prostředků, kterou chcete odstranit.
2. Vyberte **Odstranit skupinu prostředků**.
3. V **zadejte název skupiny prostředků** pole, zadejte název skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o rozhraní API pro vyhledávání zpráv Bingu, naleznete v tématu [co je pro vyhledávání zpráv Bingu?](index.yml).
