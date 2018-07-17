---
title: Text Analytics C# – tutoriál | Dokumentace Microsoftu
titleSuffix: Microsoft Cognitive Services
description: Připojení k rozhraní Text Analytics z webové aplikace ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: eb9730f785b01a620e36a265216488c401eac63a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068484"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>Připojení ke službě Text Analytics pomocí připojené služby v sadě Visual Studio

Pomocí službě Text Analytics můžete extrakci široké škály informací pro kategorizaci a zpracování vizuálních dat a moderování obrázků také služby k usnadnění s počítači provádět.

V tomto článku a jeho článcích doprovodná zadejte podrobnosti pro použití funkce připojené služby sady Visual Studio pro službu analýzy textu. Možnost je dostupná v obou Visual Studio 2017 15.7 nebo novější, s nainstalované rozšíření služeb Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- Visual Studio 2017 verze 15.7, s nainstalovaná úloha vývoj pro Web. [Stáhnout nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Přidání podpory pro váš projekt pro službu analýzy textu

1. Vytvořte nový webový projekt ASP.NET Core, který je volána TextAnalyticsDemo. Použijte šablonu projektu webové aplikace (Model-View-Controller) s výchozím nastavením. Je důležité pro pojmenování projektu MyWebApplication, takže obor názvů odpovídá, pokud kód zkopírujete do projektu.  V příkladu v tomto článku se používá MVC, ale Text Analytics připojené služby můžete použít s libovolným typem projektu ASP.NET.

1. V **Průzkumníka řešení**, dvakrát klikněte na **připojenou službu** položky.
   Zobrazí se stránka připojené služby se službami, které můžete přidat do projektu.

   ![Snímek obrazovky s připojenou službu v Průzkumníku řešení](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. V nabídce dostupné služby, zvolte **vyhodnocení subjektivního hodnocení s rozhraní Text Analytics**.

   ![Snímek obrazovky připojené služby obrazovky](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Pokud jste se přihlašovali do sady Visual Studio a mít předplatné Azure spojené s vaším účtem, se zobrazí stránka s rozevíracím seznamu k vašemu předplatnému.

   ![Snímek obrazovky z Text Analytics připojenou službu obrazovky](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Vyberte předplatné, které chcete použít a potom zvolte název pro službu analýzy textu nebo jej vybrat **upravit** odkaz na automaticky generovaný název upravit, vyberte skupinu prostředků a cenovou úroveň.

   ![Snímek obrazovky skupinu prostředků a cenové úrovně pole](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Na cenových úrovní najdete pod tímto odkazem podrobnosti.

1. Zvolte **přidat** přidání podpory pro připojenou službu.
   Visual Studio změní projekt tak, aby přidat balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení ke službě Text Analytics. **Okno výstup** obsahuje protokol o co se děje do projektu. By měl vypadat přibližně takto:

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
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Služba Text Analytics použijte k detekci jazyk pro vzorku text.

1. Přidejte následující příkazy using v souboru Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Přidejte pole Konfigurace a konstruktor, který inicializuje pole konfigurace ve třídě spuštění umožňující konfiguraci ve svém programu.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Přidejte soubor třídy do řadiče složku s názvem DemoTextAnalyzeController a nahraďte jeho obsah následujícím kódem:

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
    
    Tento kód obsahuje GetTextAnalyzeClient klienta nechte objekt, který můžete použít k volání rozhraní API pro analýzu textu a žádost o obslužnou rutinu, která volá DetectLanguage na daný text.

1. Přidáte MyHandler pomocná třída, která se používá v předchozím kódu.

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

1. Ve složce modely přidejte třídu pro model.

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

1. Přidání zobrazení pro analyzovaný text, určit jazyk a skóre, které představuje úroveň spolehlivosti při analýze. Chcete-li to provést, klikněte pravým tlačítkem na **zobrazení** složky, zvolte **přidat**, pak **zobrazení**. V dialogovém okně, které se zobrazí, zadejte název _TextAnalyzeResult_, přijměte výchozí hodnoty pro přidání nového souboru s názvem _TextAnalyzeResult.cshtml_ v **zobrazení** složky a Zkopírujte do něj následující obsah:
    
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
 
1. Sestavit a spustit místně v příkladu. Zadejte nějaký text a zjistěte, jaký jazyk pro analýzu textu rozpozná.
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby cognitive Services a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Když se zobrazí skupina prostředků použitá v tomto kurzu ve výsledcích hledání, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o službě Text Analytics pro čtení [dokumentace ke službě Text Analytics](index.yml).
