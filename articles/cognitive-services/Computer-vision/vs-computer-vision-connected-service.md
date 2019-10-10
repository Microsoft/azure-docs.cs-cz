---
title: Připojená služba sady Visual Studio – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Připojte se k rozhraní API pro počítačové zpracování obrazu z webové aplikace ASP.NET Core pomocí funkce připojená služba sady Visual Studio.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: e4308f98b6e547acd4adfb62ab78c0517247d905
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177088"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Připojení k rozhraní API pro počítačové zpracování obrazu s využitím připojených služeb v sadě Visual Studio

Tento článek a jeho doprovodné články obsahují podrobnosti k použití funkce připojené služby sady Visual Studio s rozhraním API služeb Cognitive Services pro počítačové zpracování obrazu. Tato možnost je dostupná v sadě Visual Studio 2017 15.7 nebo novější s nainstalovaným rozšířením Cognitive Services.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- Visual Studio 2017 verze 15,7 nebo novější s nainstalovanou úlohou **Vývoj webu** . [Stáhnout](https://visualstudio.microsoft.com/downloads/)

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Přidání podpory rozhraní API služeb Cognitive Services pro počítačové zpracování obrazu do projektu

1. Vytvořte nový webový projekt ASP.NET Core. Použijte prázdnou šablonu projektu. 

1. V **Průzkumníku řešení** zvolte **Přidat**  > **Připojená služba**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.

   ![Nabídka kliknutí pravým tlačítkem na projekt aplikace Visual Studio: přidat připojenou službu >](../media/vs-common/Connected-Service-Menu.PNG)

1. V nabídce dostupných služeb zvolte **Rozhraní API služeb Cognitive Services pro počítačové zpracování obrazu**.

   ![Nabídka připojené služby: analýza obrázků... je popsaný](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Pokud jste přihlášení k sadě Visual Studio a máte ke svému účtu přidružené předplatné Azure, zobrazí se stránka s rozevíracím seznamem vašich předplatných.

   ![Okno rozhraní API pro počítačové zpracování obrazu se zvýrazněným rozevíracím seznamem odběru](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Vyberte předplatné, které chcete použít, a pak zvolte název rozhraní API pro počítačové zpracování obrazu, nebo zvolte odkaz Upravit a změňte automaticky vygenerovaný název, zvolte skupinu prostředků a cenovou úroveň.

   ![Úprava podrobnosti připojené služby](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Pomocí odkazu můžete zobrazit podrobnosti o cenových úrovních.

1. Volbou možnosti Přidat přidejte podporu této připojené služby.
   Sada Visual Studio upraví projekt tak, že přidá balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení rozhraní API pro počítačové zpracování obrazu. V okně Výstup se zobrazí protokol, který ukazuje, co se s projektem děje. Mělo by se zobrazit něco podobného:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.1
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Použití rozhraní API pro počítačové zpracování obrazu k detekci atributů obrázku

1. Přidejte do souboru Startup.cs následující příkazy using.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Přidejte konfigurační pole a konstruktor, který inicializuje toto konfigurační pole ve třídě `Startup` a umožní tak konfiguraci ve vašem programu.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Do složky wwwroot projektu přidejte složku images a do složky wwwroot přidejte soubor obrázku. Jako příklad můžete použít jeden z obrázků na této [stránce rozhraní API pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Klikněte pravým tlačítkem na jeden z imagí, uložte ho na místní pevný disk a pak v Průzkumník řešení klikněte pravým tlačítkem na složku images a zvolte **přidat** > **existující položku** , kterou chcete přidat do projektu. V Průzkumníku řešení by měl projekt vypadat takto: 
  
   ![Snímek obrazovky zobrazení Průzkumníka řešení se zvoleným obrázkovým souborem](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Klikněte pravým tlačítkem na soubor obrázku, zvolte Vlastnosti a pak zvolte **Kopírovat, pokud je novější**. 

   ![okno vlastností obrázku; Kopírovat do výstupního adresáře nastaveného na kopírovat, pokud je novější](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Nahraďte metodu Configure následujícím kódem pro přístup k rozhraní API pro počítačové zpracování obrazu a otestování obrázku.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Zde uvedený kód vytvoří požadavek HTTP s identifikátorem URI a obrázkem jako binárním obsahem pro volání rozhraní REST API pro počítačové zpracování obrazu.

1. Přidejte funkce pomocných rutin GetImageAsByteArray a JsonPrettyPrint.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Spusťte webovou aplikaci a podívejte se, co rozhraní API pro počítačové zpracování obrazu našlo na vašem obrázku.

   ![Obrázek a formátované výsledky rozhraní API pro počítačové zpracování obrazu](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, pokud ji už nepotřebujete. Tím se odstraní kognitivní služba a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraní API pro počítačové zpracování obrazu najdete v [dokumentaci k rozhraní API pro počítačové zpracování obrazu](Home.md).
