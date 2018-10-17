---
title: 'Kurz: Rozhraní API pro rozpoznávání tváře v jazyce C#'
titleSuffix: Azure Cognitive Services
description: Vytvořte jednoduchou aplikaci pro Windows, která využívá rozhraní API pro rozpoznávání tváře služeb Cognitive Services k detekci rysů tváře na obrázku.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: ghogen
ms.openlocfilehash: 70414674e563cf3703d2cf3ebc57f09afcdf2691
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129518"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Připojení k rozhraní API pro rozpoznávání tváře služeb Cognitive Services pomocí připojených služeb v sadě Visual Studio

S využitím rozhraní API pro rozpoznávání tváře služeb Cognitive Services můžete detekovat, analyzovat, organizovat a označovat tváře na fotkách.

Tento článek a jeho doprovodné články obsahují podrobnosti k použití funkce připojené služby sady Visual Studio s rozhraním API pro rozpoznávání tváře služeb Cognitive Services. Tato možnost je dostupná v sadě Visual Studio 2017 15.7 nebo novější s nainstalovaným rozšířením Cognitive Services.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2017 verze 15.7** s nainstalovanou sadou funkcí **Vývoj pro web**. [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Vytvoření projektu a přidání podpory rozhraní API pro rozpoznávání tváře služeb Cognitive Services

1. Vytvořte nový webový projekt ASP.NET Core. Použijte prázdnou šablonu projektu. 

1. V **Průzkumníku řešení** zvolte **Přidat**  > **Připojená služba**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.

   ![Položka nabídky Přidat připojenou službu](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. V nabídce dostupných služeb zvolte **Rozhraní API pro rozpoznávání tváře služeb Cognitive Services**.

   ![Volba služby, která se má připojit](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Pokud jste přihlášení k sadě Visual Studio a máte ke svému účtu přidružené předplatné Azure, zobrazí se stránka s rozevíracím seznamem vašich předplatných.

   ![Vyberte své předplatné.](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Vyberte předplatné, které chcete použít, a pak zvolte název rozhraní API pro rozpoznávání tváře, nebo volbou odkazu Upravit změňte automaticky vygenerovaný název, zvolte skupinu prostředků a cenovou úroveň.

   ![Úprava podrobnosti připojené služby](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Pomocí odkazu můžete zobrazit podrobnosti o cenových úrovních.

1. Volbou možnosti Přidat přidejte podporu této připojené služby.
   Visual Studio upraví projekt tak, že přidá balíčky NuGet, položky konfiguračního souboru a další změny pro podporu připojení rozhraní API pro rozpoznávání tváře.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Použití rozhraní API pro rozpoznávání tváře k detekci atributů tváří na obrázku

1. Přidejte do souboru Startup.cs následující příkazy using.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Přidejte konfigurační pole a konstruktor, který inicializuje toto konfigurační pole ve třídě Startup a umožní tak konfiguraci ve vašem programu.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Do složky wwwroot projektu přidejte složku images a do složky wwwroot přidejte soubor obrázku. Jako příklad můžete použít jeden z obrázků na této [stránce rozhraní API pro rozpoznávání tváře](https://azure.microsoft.com/services/cognitive-services/face/). Klikněte pravým tlačítkem na jeden z obrázků, uložte ho na místní pevný disk a potom v Průzkumníku řešení klikněte pravým tlačítkem na složku images a volbou příkazu **Přidat** > **Existující položka** ho přidejte do projektu. V Průzkumníku řešení by měl projekt vypadat takto:
 
   ![Složka images se souborem obrázku](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Klikněte pravým tlačítkem na soubor obrázku, zvolte Vlastnosti a pak zvolte **Kopírovat, pokud je novější**.

   ![Kopírovat, pokud je novější](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Nahraďte metodu Configure následujícím kódem pro přístup k rozhraní API pro rozpoznávání tváře a otestování obrázku. Změňte řetězec imagePath na správnou cestu a název souboru pro váš obrázek s tváří.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Kód v tomto kroku sestavuje požadavek HTTP s voláním rozhraní REST API pro rozpoznávání tváře pomocí klíče, který jste přidali při přidání připojené služby.

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

1. Spusťte webovou aplikaci a podívejte se, co rozhraní API pro rozpoznávání tváře najde na vašem obrázku.
 
   ![Obrázek rozhraní API pro rozpoznávání tváře image a formátované výsledky](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, pokud ji už nepotřebujete. Tím se odstraní kognitivní služba a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
1. Vyberte **Odstranit skupinu prostředků**.
1. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraní API pro rozpoznávání tváře najdete v [dokumentaci k rozhraní API pro rozpoznávání tváře](Overview.md).
