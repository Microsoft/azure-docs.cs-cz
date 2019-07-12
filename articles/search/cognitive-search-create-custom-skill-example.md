---
title: 'Příklad: Vytvoření vlastního kognitivních dovedností pomocí rozhraní API týkající se vyhledávání entit Bingu – Azure Search'
description: Ukazuje, jak pomocí služby vyhledávání entit Bingu ve vlastních dovedností namapované na kanál indexování kognitivního vyhledávání ve službě Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672149"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Příklad: Vytvoření vlastních dovedností pomocí rozhraní API Bingu pro vyhledávání entit

V tomto příkladu zjistěte, jak vytvořit webové rozhraní API vlastních dovedností. Tato dovedností se přijímají umístění, veřejné obrázky a organizace a vrací popisy pro ně. V příkladu se používá [funkce Azure Functions](https://azure.microsoft.com/services/functions/) zalomení [API pro vyhledávání entit Bingu](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) tak, že implementuje rozhraní vlastních dovedností.

## <a name="prerequisites"></a>Požadavky

+ Přečtěte si informace o [vlastních dovedností rozhraní](cognitive-search-custom-skill-interface.md) článek, pokud nejste obeznámeni s vstupní a výstupní rozhraní, které by měly implementovat vlastní dovednosti.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) nebo novější, včetně funkcí vývoj pro Azure.

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

Přestože tento příklad používá funkci Azure pro hostování webového rozhraní API, není to nutné.  Za předpokladu splnění [rozhraní požadavky pro kognitivní dovednosti](cognitive-search-custom-skill-interface.md), přístup, je provést je důležité. Služba Azure Functions, ale usnadňují vytváření vlastních dovedností.

### <a name="create-a-function-app"></a>Vytvoření Function App

1. V sadě Visual Studio, vyberte **nový** > **projektu** z nabídky soubor.

1. V dialogovém okně Nový projekt, vyberte **nainstalováno**, rozbalte **Visual C#**  > **cloudu**vyberte **Azure Functions**, zadejte Zadejte název pro váš projekt a vyberte **OK**. Název aplikace funkcí musí být platný jako C# obor názvů, takže nepoužívejte podtržítka, pomlčky nebo jiné než alfanumerické znaky.

1. Vyberte **Azure Functions v2 (.NET Core)** . To může také provést s verzí 1, ale kód napsaný níže je založen na šabloně v2.

1. Vyberte typ, který má být **triggeru HTTP**

1. Pro účet úložiště, můžete vybrat **žádný**, protože jakékoli úložiště nebude nutné pro tuto funkci.

1. Vyberte **OK** funkci vytvoříte projekt a HTTP funkce aktivovaná.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Úprava kódu k volání služby vyhledávání entit Bingu

Visual Studio vytvoří projekt a v něm třídu, která obsahuje často používaný kód pro zvolený typ funkce. Atribut *FunctionName* metody nastavuje název funkce. Atribut *HttpTrigger* určuje, že je funkce aktivována požadavkem HTTP.

Nyní, veškerý obsah souboru nahraďte *Function1.cs* následujícím kódem:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Ujistěte se, že k zadání vlastní *klíč* hodnotu `key` – konstanta podle klíče, který jste získali při registraci pro vyhledávání entit Bingu, rozhraní API.

Tato ukázka obsahuje všechny nezbytného kódu do jednoho souboru ke zvýšení pohodlí. Můžete najít trochu více strukturovanými verzi stejné dovednosti v [úložiště dovednosti power](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Samozřejmě, mohou přejmenovat soubor z `Function1.cs` k `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testování funkce ze sady Visual Studio

Stisknutím klávesy **F5** ke spuštění funkce chování programu a testování. V tomto případě použijeme následující funkce k vyhledání dvěma entitami. Pomocí nástroje Postman nebo Fiddler vydat volání, například následující:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Text požadavku
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikování funkce Azure

Jakmile budete spokojeni s tím, chování funkce, můžete ho publikovat.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. Zvolte **vytvořit nový** > **publikovat**.

1. Pokud jste ještě nepřipojili Visual Studio ke svému účtu Azure, vyberte **přidat účet...**

1. Použijte na obrazovce zobrazí výzvu. Budete vyzváni k zadejte jedinečný název pro službu app service, předplatné Azure, skupinu prostředků, plán hostování a účet úložiště, který chcete použít. Pokud ještě nemáte tyto, můžete vytvořit novou skupinu prostředků, nový plán hostování a účet úložiště. Až budete hotovi, vyberte **Create**

1. Po dokončení nasazení, Všimněte si, že adresa URL webu. Je to adresa vaší aplikace function App v Azure. 

1. V [webu Azure portal](https://portal.azure.com), přejděte do skupiny prostředků a vyhledejte `EntitySearch` funkce, které jste publikovali. V části **spravovat** oddílu, měli byste vidět klíče hostitele. Vyberte **kopírování** ikonu *výchozí* klíč hostitele.  

## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Teď, když máte klíč hostitele výchozí, funkci otestovat následujícím způsobem:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Text žádosti
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

V tomto příkladu by měl mít stejný výsledek, který jste předtím viděli při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojit do kanálu
Teď, když máte nové vlastní dovednosti, přidáte jej do vaše dovednosti. Následující příklad ukazuje, jak volat se přidají popisy k organizace v dokumentu (to může rozšířit také pracovat na umístění a lidé). Nahraďte `[your-entity-search-app-name]` s názvem vaší aplikace.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Tady jsme už počítání předdefinované [dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md) nacházet v zkušenostech a rozšíření dokumentu se seznamem organizace. Pro informaci je zde konfiguraci dovednosti extrakce entity, která by byla dostatečná při generování dat, která potřebujeme:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Další postup
Blahopřejeme! Vaše první vlastní enricher jste vytvořili. Teď můžete použít stejný vzor pro přidání vlastních funkcí. 

+ [Přidání vlastních dovedností do kanálu kognitivního vyhledávání](cognitive-search-custom-skill-interface.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Vytvoření dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Způsob mapování polí bohatších možností](cognitive-search-output-field-mapping.md)
