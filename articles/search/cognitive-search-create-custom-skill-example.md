---
title: 'Příklad: Vytvoření vlastní znalostí v kanálu kognitivní vyhledávání (Azure Search) | Microsoft Docs'
description: Ukazuje, jak pomocí rozhraní API převede Text v vlastní dovedností, které jsou namapované na kognitivní vyhledávání indexování kanálu ve službě Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a295bf741862bb58a86234b5c85f48d7a1b52be7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Příklad: Vytvoření vlastní odborností pomocí rozhraní API převede Text

V tomto příkladu zjistěte, jak vytvořit webové rozhraní API vlastní dovedností, které přijímá textu v libovolném jazyce a převede jej na angličtinu. V příkladu se používá [funkce Azure](https://azure.microsoft.com/services/functions/) zabalit [převede Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) tak, aby implementuje rozhraní vlastní znalostí.

## <a name="prerequisites"></a>Požadavky

+ Přečtěte si informace o [vlastní odborností rozhraní](cognitive-search-custom-skill-interface.md) článek Pokud nejste obeznámeni s rozhraním vstupu a výstupu, které by měla implementovat vlastní znalostí.

+ [Zaregistrujte si rozhraní API Text překladač](../cognitive-services/translator/translator-text-how-to-signup.md)a získat klíč rozhraní API ho zpracovat.

+ Nainstalujte [Visual Studio 2017 verze 15,5](https://www.visualstudio.com/vs/) nebo novější, včetně pracovního vytížení Azure development.

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

I když tento příklad používá funkci Azure k hostování webové rozhraní API, není nutné.  Tak dlouho, dokud splňujete [rozhraní požadavky pro kognitivní odborností](cognitive-search-custom-skill-interface.md), je nepodstatné, přístup, můžete provést. Azure Functions, ale usnadňují vytvořit vlastní znalostí.

### <a name="create-a-function-app"></a>Vytvoření Function App

1. V sadě Visual Studio, vyberte **nový** > **projektu** z nabídky soubor.

1. V dialogovém okně Nový projekt, vyberte **nainstalovaná**, rozbalte položku **Visual C#** > **cloudu**, vyberte **Azure Functions**, zadejte Název pro svůj projekt a vyberte **OK**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. Vyberte typ, který má být **triggeru protokolu HTTP**

1. Pro účet úložiště, můžete si vybrat **žádné**, protože nebude jakékoli úložiště, musíte pro tuto funkci.

1. Vyberte **OK** funkci vytvoříte projekt a HTTP aktivované funkce.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Upravte kód pro volání převede kognitivní služby

Visual Studio vytvoří projekt a v něm třídu, která obsahuje často používaný kód pro zvolený typ funkce. Atribut *FunctionName* metody nastavuje název funkce. Atribut *HttpTrigger* určuje, že je funkce aktivována požadavkem HTTP.

Nyní, veškerý obsah souboru nahraďte *Function1.cs* následujícím kódem:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
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


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
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
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

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
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Nezapomeňte zadat vlastní *klíč* hodnotu *TranslateText* na klíč, který jste získali při registraci pro rozhraní API převede Text na základě metod.

V tomto příkladu je jednoduchý enricher, který funguje pouze na jeden záznam současně. Tuto skutečnost stane důležité později při nastavování velikost dávky pro skillset.

## <a name="test-the-function-from-visual-studio"></a>Testování funkce ze sady Visual Studio

Stiskněte klávesu **F5** ke spuštění funkce chování programu a testování. Použijte Postman nebo Fiddler k vydávání volání, jako je uvedeno níže:

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
               "language": "es"
            }
        }
   ]
}
```
### <a name="response"></a>Odpověď
Byste měli vidět odpověď podobně jako v následujícím příkladu:

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

Jakmile budete spokojeni s chováním funkce, můžete ho publikovat.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. Zvolte **vytvořit nový** > **publikování**.

1. Pokud jste již nepřipojili Visual Studio k účtu Azure, vyberte **přidat účet...**

1. Postupujte podle na obrazovce zobrazí výzvu. Zobrazí se výzva k zadání účtu Azure, skupinu prostředků, hostování plán a účet úložiště, který chcete použít. Pokud ještě nemáte tyto můžete vytvořit novou skupinu prostředků, nový plán hostování a účet úložiště. Po dokončení vyberte **vytvořit**

1. Po dokončení nasazení Poznámka: adresa URL webu. Je to adresa funkce aplikace v Azure. 

1. V [portál Azure](https://portal.azure.com), přejděte do skupiny prostředků a vyhledejte funkci převede jste publikovali. V části **spravovat** část, měli byste vidět klíče hostitele. Vyberte **kopie** ikonu *výchozí* klíč hostitele.  


## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Teď, když máte výchozí klíč hostitele, funkci otestovat následujícím způsobem:

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
               "language": "es"
            }
        }
   ]
}
```

To by měl vytvořit výsledku podobně jako ten, který jste předtím viděli při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojení do kanálu
Teď, když máte nové vlastní odborností, můžete ho přidat do vaší skillset. Následující příklad ukazuje, jak volat dovednosti. Protože dovednosti nemůže pracovat s dávky, přidejte instrukce pro maximální velikost dávky být právě ```1``` odesílat dokumenty, jeden po druhém.

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
            "source": "/document/languageCode"
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
Blahopřejeme! Vytvoření vaší první vlastní enricher. Nyní můžete podle stejného vzoru přidat vlastní vlastní funkce. 

+ [Přidat vlastní dovednosti pro kanál kognitivní vyhledávání](cognitive-search-custom-skill-interface.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vytvoření Skillset (REST)](ref-create-skillset.md)
+ [Postup mapování provádět rozšířené pole](cognitive-search-output-field-mapping.md)