---
title: Počítač pro zpracování obrazu C# – tutoriál | Dokumentace Microsoftu
titleSuffix: Microsoft Cognitive Services
description: Připojení k Cognitive Services pro počítačové zpracování obrazu z webové aplikace ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 76ca1215144a5caa40971e1eda23f6462f7bf27b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665252"
---
# <a name="connecting-to-cognitive-services-computer-vision-api-by-using-connected-services-in-visual-studio"></a>Připojení k Vision API služeb Cognitive Services počítače pomocí připojené služby v sadě Visual Studio

S použitím počítači Vision API služeb Cognitive Services, můžete extrakci široké škály informací pro kategorizaci a zpracování vizuálních dat a moderování obrázků také služby k usnadnění s počítači provádět.

V tomto článku a jeho článcích doprovodná zadejte podrobnosti pro použití funkce Visual Studio připojená služba pro zpracování obrazu API služeb Cognitive Services počítače. Možnost je dostupná v obou Visual Studio 2017 15.7 nebo novější, s nainstalované rozšíření služeb Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2017 verze 15.7** s **vývoj pro Web** nainstalovaná úloha. [Stáhnout nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Přidání podpory pro zpracování obrazu API služeb Cognitive Services počítači do svého projektu

1. Vytvořte nový webový projekt ASP.NET Core. Použijte šablonu prázdného projektu. 

1. V **Průzkumníka řešení**, zvolte **přidat** > **připojenou službu**.
   Se službami, které můžete přidat do projektu se zobrazí na stránce připojené služby.

   ![Přidání položky nabídky připojenou službu](../media/vs-common/Connected-Service-Menu.PNG)

1. V nabídce dostupné služby, zvolte **Vision API služeb Cognitive Services počítače**.

   ![Zvolte službu pro připojení k](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Pokud jste se přihlašovali do sady Visual Studio a mít předplatné Azure spojené s vaším účtem, se zobrazí stránka s rozevíracím seznamu k vašemu předplatnému.

   ![Vyberte své předplatné.](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Vyberte předplatné, které chcete použít a pak zvolte název pro rozhraní API pro počítačové zpracování obrazu nebo zvolte odkaz pro úpravy automaticky vygenerovaný název upravit, vyberte skupinu prostředků a cenovou úroveň.

   ![Upravit podrobnosti připojené služby](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Na cenových úrovní najdete pod tímto odkazem podrobnosti.

1. Zvolte možnost přidat k přidání podporovaných pro připojenou službu.
   Visual Studio změní projekt tak, aby přidat balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení rozhraní API pro počítačové zpracování obrazu. V okně výstup zobrazuje protokol, co se děje do projektu. By měl vypadat přibližně takto:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 1.0.2-preview.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.0
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Použijte rozhraní API pro počítačové zpracování obrazu k detekci atributy obrázku

1. Přidejte následující příkazy using v souboru Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Přidejte konfigurační pole a přidejte konstruktor, který inicializuje pole configuration v `Startup` třídy umožňující konfiguraci ve svém programu.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Ve složce wwwroot v projektu přidat složku Obrázky a přidání souboru obrázku do složky wwwroot. Například můžete některou k imagí na tomto [stránce rozhraní API pro počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/). Klikněte pravým tlačítkem na některou k imagí, uložit na místní pevný disk a potom v Průzkumníku řešení klikněte pravým tlačítkem myši na složku Obrázky a choosee **přidat** > **existující položku** ho přidat do projektu. Projekt by měl vypadat asi takhle nějak. v Průzkumníku řešení: 
  
   ![bitové kopie složky se souborem bitové kopie](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Klikněte pravým tlačítkem na soubor obrázku, zvolte Vlastnosti a pak zvolte **kopírovat, pokud je novější**. 

   ![Kopírovat, pokud je novější](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Nahraďte metodu konfigurovat následující kód pro přístup k rozhraní API pro počítačové zpracování obrazu a otestovat bitovou kopii.

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
    Kód vytvoří žádost HTTP se identifikátor URI a bitovou kopii jako binární obsah pro volání rozhraní API pro počítačové zpracování obrazu REST.

1. Přidáte pomocné funkce GetImageAsByteArray a JsonPrettyPrint.

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

1. Spusťte webovou aplikaci a zobrazit rozhraní API pro počítačové zpracování obrazu nalezen ve vaší imagi.

   ![Rozhraní API pro zpracování obrazu image počítače a formátované výsledky](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby cognitive Services a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o rozhraní API pro počítačové zpracování obrazu pro čtení [dokumentace k rozhraní API pro zpracování obrazu počítače](Home.md).
