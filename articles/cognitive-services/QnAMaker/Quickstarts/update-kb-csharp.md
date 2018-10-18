---
title: 'Rychlý start: Aktualizace znalostní báze – REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý start vás provede programovou aktualizací ukázkové znalostní báze služby QnA Maker. Definice JSON, kterou použijete k aktualizaci znalostní báze, umožňuje přidávat, měnit nebo odstraňovat páry otázek a odpovědí.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: 9fa57a02f11461a3ea9a08b7b1b101b143924037
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801689"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-c"></a>Rychlý start: Aktualizace znalostní báze služby QnA Maker v jazyce C#

Tento rychlý start vás provede programovou aktualizací existující znalostní báze služby QnA Maker.  Tento kód JSON umožňuje aktualizovat znalostní bázi přidáním nových zdrojů dat, změnou zdrojů dat nebo jejich odstraněním.

Toto rozhraní API je ekvivalentní úpravě a následnému použití tlačítka **Save and train** (Uložit a natrénovat) na portálu služby QnA Maker.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) – Model pro znalostní bázi je definován v kódu ve formátu JSON poslaném v těle požadavku rozhraní API. 
* [Get Operation Details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze sady [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**.
* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Vytvoření projektu znalostní báze

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Přidání požadovaných závislostí

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Přidání požadovaných konstant

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-knowledge-base-id"></a>Přidání ID znalostní báze

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-the-kb-update-definition"></a>Přidání definice aktualizace znalostní báze

Za konstanty přidejte následující definici aktualizace znalostní báze. Definice aktualizace má tři části:

* add
* update
* delete

Jednotlivé části je možné použít ve stejném požadavku na rozhraní API. 

```csharp
static string new_kb = @"
{
    'add': {
        'qnaList': [
            {
            'id': 1,
            'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
            'source': 'Custom Editorial',
            'questions': [
                'How can I change the default message from QnA Maker?'
            ],
            'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'QnA Maker FAQ from quickstart - updated'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Přidání podpůrných funkcí a struktur

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-patch-request-to-update-kb"></a>Přidání požadavku PATCH, který aktualizuje znalostní bázi

Následující kód odešle do rozhraní API služby QnA Maker požadavek HTTPS, který aktualizuje skupiny otázek a odpovědí ve znalostní bázi. Potom kód přijme odpověď:

```csharp
async static Task<Response> PatchUpdateKB(string kb, string new_kb)
{
    string uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = new HttpMethod("PATCH");
        request.RequestUri = new Uri(uri);

        request.Content = new StringContent(new_kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Přidání požadavku GET, který určí stav vytváření

Aktualizace znalostní báze umožňuje přidat, aktualizovat a odstranit páry otázek a odpovědí. 

```csharp
async static Task<Response> GetStatus(string operation)
{
    string uri = host + service + operation;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i stav operace: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Volání opakujte, dokud neskočí úspěchem nebo neúspěchem: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Přidání metody UpdateKB
Následující metoda aktualizuje znalostní bázi a opakovaně kontroluje stav. Protože vytvoření znalostní báze může nějakou dobu trvat, je třeba volání na kontrolu stavu opakovat, dokud vrácený stav nebude úspěch nebo neúspěch.

```csharp
async static void UpdateKB(string kb, string new_kb)
{
    try
    {
        // Starts the QnA Maker operation to update the knowledge base.
        var response = await PatchUpdateKB(kb, new_kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostUpdateKB(string, string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation updating the
        // knowledge base. Once the operation state is something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);
            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still updating the knowledge base. The thread is
                // paused for a number of seconds equal to the Retry-After
                // header value, and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed updating the knowledge base.
                done = true;
            }
        }
    }
    catch(Exception ex)
    {
        // An error occurred while updating the knowledge base. Ensure that
        // you included your QnA Maker subscription key and knowledge base ID
        // where directed in the sample.
        Console.WriteLine("An error occurred while updating the knowledge base." + ex.InnerException);
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }
}
```

## <a name="add-the-updatekb-method-to-main"></a>Přidání metody UpdateKB do metody Main

Změňte metodu Main, aby volala metodu UpdateKB:

```csharp
static void Main(string[] args)
{
    // Invoke the UpdateKB() method to update a knowledge base, periodically
    // checking the status of the QnA Maker operation until the
    // knowledge base is updated.
    UpdateKB(kb, new_kb);

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```


## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program. Program automaticky do rozhraní API služby QnA Maker odešle požadavek na aktualizaci znalostní báze a pak se bude každých 30 sekund dotazovat na výsledky. Každá odpověď se zobrazí v okně konzoly.

Jakmile se znalostní báze aktualizuje, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)