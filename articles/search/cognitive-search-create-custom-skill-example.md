---
title: Příklad vlastní dovednosti pomocí rozhraní API Bingu pro vyhledávání entit
titleSuffix: Azure Cognitive Search
description: Ukazuje použití služby Vyhledávání entit Bingu ve vlastní dovednosti mapované na kanál pro indexování s obohaceným AI ve službě Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020180"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Příklad: Vytvoření vlastní dovednosti pomocí rozhraní API Bingu pro vyhledávání entit

V tomto příkladu se dozvíte, jak vytvořit vlastní dovednost webového rozhraní API. Tato dovednost bude přijímat umístění, veřejné údaje a organizace a vrátí popisy pro ně. Tento příklad používá [funkci Azure](https://azure.microsoft.com/services/functions/) k zabalení [rozhraní API Bingu pro vyhledávání entit](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) tak, aby implementovala vlastní rozhraní dovedností.

## <a name="prerequisites"></a>Požadavky

+ Přečtěte si o vlastním článku o [dovednostech rozhraní](cognitive-search-custom-skill-interface.md) , pokud neznáte vstupní/výstupní rozhraní, které by měla vlastní dovednost implementovat.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/vs/) nebo novější, včetně úlohy vývoj pro Azure.

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure Function

I když tento příklad používá funkci Azure k hostování webového rozhraní API, nevyžaduje se.  Pokud splňujete [požadavky na rozhraní pro vnímání znalostí](cognitive-search-custom-skill-interface.md), je přístup, který převezmete, nemateriálný. Azure Functions však usnadňuje vytváření vlastních dovedností.

### <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

1. V aplikaci Visual Studio vyberte v nabídce soubor možnost **Nový**  >  **projekt** .

1. V dialogovém okně Nový projekt vyberte **instalovat**, rozbalte Cloud **Visual C#**  >  **Cloud**, vyberte **Azure Functions**, zadejte název projektu a vyberte **OK**. Název aplikace funkcí musí být platný jako obor názvů jazyka C#, proto nepoužívejte podtržítka, spojovníky nebo žádné jiné jiné než alfanumerické znaky.

1. Vyberte **Azure Functions v2 (.NET Core)**. Můžete to také provést s verzí 1, ale kód napsaný níže je založen na šabloně v2.

1. Vyberte typ, který se má **aktivovat protokolem HTTP** .

1. V případě účtu úložiště můžete vybrat možnost **žádné**, protože pro tuto funkci nebudete potřebovat žádné úložiště.

1. Výběrem **OK** vytvořte projekt funkce a funkci AKTIVOVANou protokolem HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Upravte kód pro volání služby Vyhledávání entit Bingu

Visual Studio vytvoří projekt a v něm třídu, která obsahuje často používaný kód pro zvolený typ funkce. Atribut *FunctionName* metody nastavuje název funkce. Atribut *HttpTrigger* určuje, že je funkce aktivována požadavkem HTTP.

Nyní nahraďte veškerý obsah souboru *function1.cs* následujícím kódem:

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
    /// AI enrichment pipeline.
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

Nezapomeňte zadat hodnotu vlastního *klíče* v `key` konstantě na základě klíče, který jste získali při registraci rozhraní API Bingu pro vyhledávání entit.

Tato ukázka zahrnuje veškerý potřebný kód v jednom souboru pro usnadnění práce. V [úložišti dovedností pro napájení](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)najdete poněkud více strukturované verze stejné dovednosti.

Tento soubor samozřejmě můžete přejmenovat z `Function1.cs` na `BingEntitySearch.cs` .

## <a name="test-the-function-from-visual-studio"></a>Testování funkce ze sady Visual Studio

Stiskněte klávesu **F5** ke spuštění programu a chování funkce testu. V tomto případě použijeme níže uvedenou funkci k vyhledání dvou entit. Použijte post nebo Fiddler k vydání volání jako na následujícím obrázku:

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
Měla by se zobrazit odpověď podobná následujícímu příkladu:

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

## <a name="publish-the-function-to-azure"></a>Publikování funkce do Azure

Až budete s chováním funkce spokojeni, můžete ho publikovat.

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. Vyberte **vytvořit nové**  >  **publikování**.

1. Pokud jste ještě nepřipojili Visual Studio k účtu Azure, vyberte **Přidat účet....**

1. Postupujte podle výzev na obrazovce. Budete požádáni o zadání jedinečného názvu pro službu App Service, předplatné Azure, skupinu prostředků, plán hostování a účet úložiště, který chcete použít. Můžete vytvořit novou skupinu prostředků, nový plán hostování a účet úložiště, pokud je ještě nemáte. Po dokončení vyberte **vytvořit** .

1. Po dokončení nasazení si všimněte adresy URL webu. Je to adresa vaší aplikace Function App v Azure. 

1. V [Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyhledejte `EntitySearch` funkci, kterou jste publikovali. V části **Spravovat** byste měli vidět klíče hostitele. Vyberte ikonu **kopírování** pro *výchozí* klíč hostitele.  

## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Teď, když máte výchozí klíč hostitele, otestujte funkci následujícím způsobem:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
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

Tento příklad by měl mít stejný výsledek, který jste viděli dříve při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojení k vašemu kanálu
Teď, když máte novou vlastní dovednost, ji můžete přidat do svého dovednostiu. Následující příklad ukazuje, jak volat dovednost pro přidání popisů do organizací v dokumentu (může být rozšířena tak, aby fungovala také na místech a na lidech). Nahraďte `[your-entity-search-app-name]` názvem vaší aplikace.

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

Tady se počítáme s předdefinovanými [dovednostmi pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md) , která se mají prezentovat v dovednosti a rozšířit dokument seznamem organizací. V tomto článku najdete konfiguraci dovedností pro extrakci entit, která by byla dostatečná pro vytváření dat, která potřebujeme:

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

## <a name="next-steps"></a>Další kroky
Gratulujeme! Vytvořili jste svou první vlastní dovednost. Teď můžete postupovat podle stejného vzoru, abyste mohli přidat vlastní funkce. Další informace získáte kliknutím na následující odkazy.

+ [Dovednosti v Power: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Přidání vlastní dovednosti do kanálu pro obohacení AI](cognitive-search-custom-skill-interface.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)