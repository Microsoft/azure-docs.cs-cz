---
title: 'Příklad: Vytvoření vlastních dovedností v kanálu kognitivního vyhledávání (Azure Search) | Dokumentace Microsoftu'
description: Ukazuje použití rozhraní API textu překlad ve vlastní dovednosti, které jsou namapované na kognitivního vyhledávání, indexování kanálu ve službě Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: luisca
ms.openlocfilehash: 4dfc4d250cae80e7837db5cfbcc3af15390b2bb4
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352332"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Příklad: Vytvoření vlastní dovednosti pomocí rozhraní API převede Text

V tomto příkladu zjistěte, jak vytvořit webové rozhraní API vlastních dovedností, která přijímá text v libovolném jazyce a převede jej na angličtinu. V příkladu se používá [funkce Azure Functions](https://azure.microsoft.com/services/functions/) zalomení [rozhraní API pro překlad textu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) tak, že implementuje rozhraní vlastních dovedností.

## <a name="prerequisites"></a>Požadavky

+ Přečtěte si informace o [vlastních dovedností rozhraní](cognitive-search-custom-skill-interface.md) článek, pokud nejste obeznámeni s vstupní a výstupní rozhraní, které by měly implementovat vlastní dovednosti.

+ [Zaregistrujte si Translator Text API](../cognitive-services/translator/translator-text-how-to-signup.md)a získání klíče rozhraní API ho zpracovat.

+ Nainstalujte [Visual Studio 2017 verze 15.5](https://www.visualstudio.com/vs/) nebo novější, včetně funkcí vývoj pro Azure.

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

Přestože tento příklad používá funkci Azure pro hostování webového rozhraní API, není povinné.  Za předpokladu splnění [rozhraní požadavky pro kognitivní dovednosti](cognitive-search-custom-skill-interface.md), přístup, je provést je důležité. Služba Azure Functions, ale usnadňují vytváření vlastních dovedností.

### <a name="create-a-function-app"></a>Vytvoření Function App

1. V sadě Visual Studio, vyberte **nový** > **projektu** z nabídky soubor.

1. V dialogovém okně Nový projekt, vyberte **nainstalováno**, rozbalte **Visual C#** > **cloudu**vyberte **Azure Functions**, zadejte Zadejte název pro váš projekt a vyberte **OK**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. Vyberte **Azure Functions v2 (.Net Core)**. To může také provést s verzí 1, ale kód napsaný níže je založen na šabloně v2.

1. Vyberte typ, který má být **triggeru HTTP**

1. Pro účet úložiště, můžete vybrat **žádný**, protože jakékoli úložiště nebude nutné pro tuto funkci.

1. Vyberte **OK** funkci vytvoříte projekt a HTTP funkce aktivovaná.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Upravit kód pro volání služby přeložit Cognitive Services

Visual Studio vytvoří projekt a v něm třídu, která obsahuje často používaný kód pro zvolený typ funkce. Atribut *FunctionName* metody nastavuje název funkce. Atribut *HttpTrigger* určuje, že je funkce aktivována požadavkem HTTP.

Nyní, veškerý obsah souboru nahraďte *Function1.cs* následujícím kódem:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Ujistěte se, že k zadání vlastní *klíč* hodnotu *TranslateText* metody založené na klíč, který jste získali při registraci pro rozhraní API pro překlad textu.

V tomto příkladu je jednoduché enricher, která funguje pouze na jeden záznam v čase. Tato skutečnost bude důležité později, při nastavování velikost dávky pro zkušenostech.

## <a name="test-the-function-from-visual-studio"></a>Testování funkce ze sady Visual Studio

Stisknutím klávesy **F5** ke spuštění funkce chování programu a testování. V tomto případě použijeme funkci níže můžete přeložit text ve španělštině na angličtinu. Pomocí nástroje Postman nebo Fiddler vydat volání, například následující:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Tělo požadavku
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Odpověď
Měli byste vidět odpovědi podobně jako v následujícím příkladu:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikování funkce Azure

Pokud jste spokojeni s chováním funkce, můžete ho publikovat.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. Zvolte **vytvořit nový** > **publikovat**.

1. Pokud jste ještě nepřipojili Visual Studio ke svému účtu Azure, vyberte **přidat účet...**

1. Použijte na obrazovce zobrazí výzvu. Zobrazí se výzva k zadání účtu Azure, skupinu prostředků, plán hostování a účet úložiště, který chcete použít. Pokud ještě nemáte tyto, můžete vytvořit novou skupinu prostředků, nový plán hostování a účet úložiště. Až budete hotovi, vyberte **Create**

1. Po dokončení nasazení se Poznámka: adresa URL webu. Je to adresa vaší aplikace function App v Azure. 

1. V [webu Azure portal](https://portal.azure.com), přejděte do skupiny prostředků a oblast pro funkci překladu jste publikovali. V části **spravovat** oddílu, měli byste vidět klíče hostitele. Vyberte **kopírování** ikonu *výchozí* klíč hostitele.  

## <a name="update-ssl-settings"></a>Aktualizovat nastavení SSL

Všechny funkce Azure vytvořené po 30. června 2018 zakázali protokol TLS 1.0, která není momentálně kompatibilní s vlastních dovedností.

1. V [webu Azure portal](https://portal.azure.com), přejděte do skupiny prostředků a oblast pro funkci překladu jste publikovali. V části **funkce platformy** části by se měla zobrazit protokol SSL.

1. Po výběru SSL, byste měli změnit **TLS minimální verze** 1.0. Protokol TLS 1.2 funkce zatím nepodporují jako vlastních dovedností.

## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Teď, když máte klíč hostitele výchozí, funkci otestovat následujícím způsobem:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Text žádosti
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

To mohou být vráceny podobného výsledku ten, který jste předtím viděli při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojit do kanálu
Teď, když máte nové vlastní dovednosti, přidáte jej do vaše dovednosti. Následující příklad ukazuje, jak volat dovednosti. Protože dovednosti nemůže pracovat s listy, přidejte instrukce pro maximální velikost dávky být právě ```1``` odesílat dokumenty jednotlivě.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Vytvoření vaší první vlastní enricher. Teď můžete použít stejný vzor pro přidání vlastních funkcí. 

+ [Přidání vlastních dovedností do kanálu kognitivního vyhledávání](cognitive-search-custom-skill-interface.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
