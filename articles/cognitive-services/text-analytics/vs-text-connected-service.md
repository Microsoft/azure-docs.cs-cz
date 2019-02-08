---
title: 'Kurz: Analýza textu s využitímC#'
titleSuffix: Azure Cognitive Services
description: Připojte se ke službě analýzy textu z webové aplikace ASP.NET Core.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: ghogen
ms.openlocfilehash: bcd0d68119d141f02f77d4d8828d55b1d984be0e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869802"
---
# <a name="tutorial-connect-to-the-text-analytics-cognitive-service-by-using-connected-services-in-visual-studio"></a>Kurz: Připojení ke službě Text Analytics Cognitive pomocí připojené služby v sadě Visual Studio

Pomocí služby analýzy textu se dá extrahovat velké množství dat, které je možné využít ke kategorizaci a zpracování vizuálních dat a také k usnadnění správy služeb díky moderování obrázků s podporou počítače.

Tento článek a jeho doprovodné články obsahují podrobnosti k použití funkce připojené služby sady Visual Studio pro službu Analýza textu. Tato možnost je dostupná v sadě Visual Studio 2017 15.7 nebo novější s nainstalovaným rozšířením Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- Sada Visual Studio 2017 verze 15.7 s nainstalovanou sadou funkcí Vývoj pro web. [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Přidání podpory služby analýzy textu do projektu

1. Vytvořte si nový webový projekt ASP.NET Core s názvem TextAnalyticsDemo. Použijte šablonu projektu Webová aplikace (Model-View-Controller) a ponechte výchozí nastavení. Je důležité projekt pojmenovat MyWebApplication, aby se při kopírování kódu do projektu shodovaly obory názvů.  Příklad v tomto článku používá MVC, ale připojenou službu analýzy textu můžete použít s libovolným typem projektu ASP.NET.

1. V **Průzkumníku řešení** poklikejte na položku **Připojená služba**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.

   ![Snímek obrazovky připojené služby v Průzkumníku řešení](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. V nabídce dostupných služeb zvolte **Evaluate Sentiment with Text Analytics** (Vyhodnotit mínění pomocí analýzy textu).

   ![Snímek obrazovky připojených služeb](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Pokud jste přihlášení k sadě Visual Studio a máte ke svému účtu přidružené předplatné Azure, zobrazí se stránka s rozevíracím seznamem vašich předplatných.

   ![Snímek obrazovky připojené služby analýzy textu](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Vyberte předplatné, které chcete použít, a pak zvolte název služby analýzy textu, nebo volbou odkazu **Upravit** změňte automaticky vygenerovaný název, zvolte skupinu prostředků a cenovou úroveň.

   ![Snímek obrazovky s poli Skupina prostředků a Cenová úroveň](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Pomocí odkazu můžete zobrazit podrobnosti o cenových úrovních.

1. Výběrem možnosti **Přidat** přidejte podporu této připojené služby.
   Sada Visual Studio upraví projekt tak, že přidá balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení ke službě analýzy textu. V **okně výstupu** se zobrazí protokol, který ukazuje, co se s projektem děje. Mělo by se zobrazit něco podobného:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Použijte službu analýzy textu k rozpoznání jazyka u ukázkového textu.

1. Přidejte do souboru Startup.cs následující příkazy using.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Přidejte konfigurační pole a konstruktor, který inicializuje toto konfigurační pole ve třídě Startup a umožní tak konfiguraci ve vašem programu.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Do složky kontrolerů přidejte soubor třídy s názvem DemoTextAnalyzeController a nahraďte jeho obsah následujícím kódem:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Kód zahrnuje příkaz GetTextAnalyzeClient pro získání objektu klienta, který můžete použít pro volání rozhraní API pro analýzu textu, a popisovač žádosti, který zavolá metodu DetectLanguage u daného textu.

1. Přidejte třídu pomocné rutiny MyHandler, kterou používá předcházející kód.

    ```csharp
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

1. Do složky modelů přidejte třídu modelu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Přidejte zobrazení pro zobrazení analyzovaného textu, určovaný jazyk a skóre, které reprezentuje úroveň spolehlivosti analýzy. To uděláte tak, že pravým tlačítkem myši kliknete na složku **Zobrazení** a zvolíte **Přidat** a potom **Zobrazení**. V zobrazeném dialogovém okně zadejte název _TextAnalyzeResult_, ve složce **Zobrazení** potvrďte výchozí hodnoty pro přidání nového souboru s názvem _TextAnalyzeResult.cshtml_ a zkopírujte do něj tento obsah:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Příklad sestavte a spusťte místně. Zadejte libovolný text a podívejte se, co jazyková analýza textu zjistí.
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ji nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služba Cognitive Services a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto kurzu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o službě analýzy textu najdete v [dokumentaci ke službě analýzy textu](index.yml).
