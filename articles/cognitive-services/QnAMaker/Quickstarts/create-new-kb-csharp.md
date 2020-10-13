---
title: 'Rychlý start: Vytvoření znalostní báze – REST, C# – QnA Maker'
description: Tento rychlý start založený na REST a jazyce C# vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 6b64c29e6a26f2f9585e7f2c737f98e4646b1332
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777713"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>Rychlý Start: vytvoření znalostní báze v QnA Maker pomocí jazyka C# s REST

Tento rychlý start vás provede vytvořením a publikováním ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Získat podrobnosti operace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Ukázka jazyka C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Musíte mít [QnA maker prostředek](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE).

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `qna-maker-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části souboru Program.cs nahraďte samostatný příkaz using následujícími řádky, které do projektu přidají potřebné závislosti:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části třídy program přidejte požadované konstanty pro přístup k QnA Maker.

Nastavte následující hodnoty v proměnných prostředí:

* `QNA_MAKER_SUBSCRIPTION_KEY` - **Klíč** je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA maker na stránce rychlý Start. To není totéž jako klíč koncového bodu předpovědi.
* `QNA_MAKER_ENDPOINT` – **Koncový bod** je adresa URL pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>Přidání definice znalostní báze

Za konstanty přidejte následující definici znalostní báze:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>Přidání podpůrných funkcí a struktur
Přidejte následující blok kódu do třídy Program:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>Přidání požadavku POST na vytvoření znalostní báze

Následující kód odešle do rozhraní API služby QnA Maker požadavek HTTPS, který vytvoří znalostní bázi. Potom kód přijme odpověď:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Přidání požadavku GET, který určí stav vytváření

Zkontrolujte stav operace.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

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

## <a name="add-createkb-method"></a>Přidání metody CreateKB

Následující metoda vytvoří znalostní bázi a opakovaně zkontroluje stav.  **ID operace** _Vytvoření_ se vrátí v **umístění**pole hlavičky odpovědi post a pak se použije jako součást trasy v žádosti o získání. Protože vytvoření znalostní báze může nějakou dobu trvat, je třeba volání na kontrolu stavu opakovat, dokud vrácený stav nebude úspěch nebo neúspěch. Pokud operace proběhne úspěšně, ve vlastnosti **resourceLocation** se vrátí ID znalostní báze.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>Přidání metody CreateKB do metody Main

Změňte metodu Main, aby volala metodu CreateKB:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program. Program automaticky odešle do rozhraní API služby QnA Maker požadavek na vytvoření znalostní báze a dál se bude každých 30 sekund dotazovat na výsledky. Každá odpověď se zobrazí v okně konzoly.

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices).


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
