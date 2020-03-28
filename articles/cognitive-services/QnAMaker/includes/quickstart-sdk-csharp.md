---
title: 'Úvodní příručka: Klientská knihovna QnA Maker pro rozhraní .NET'
description: Tento rychlý start ukazuje, jak začít s klientskou knihovnou QnA Maker pro rozhraní .NET. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946338"
---
Pomocí klientské knihovny QnA Maker pro rozhraní .NET:

* Vytvoření znalostní báze
* Správa znalostní báze
* Publikování znalostní báze
* Generovat odpověď ze znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C#](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure pro QnA Maker

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro QnA Maker pomocí [portálu Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači.

Po získání klíče a koncového bodu pro váš prostředek [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `QNAMAKER_SUBSCRIPTION_KEY`. Název prostředku se používá jako součást adresy URL koncového bodu.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve vašem upřednostňovaném editoru nebo ide.

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `qna-maker-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```dotnetcli
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu QnA Maker pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.


## <a name="object-model"></a>Objektový model

Klient QnA Maker je [qnAmakerklient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objekt, který se ověřuje do Azure pomocí Microsoft.Rest.ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [Znalostní báze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) k vytvoření, správě a publikování znalostní báze.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrátí objekt JSON označující stav. Pro dlouhotrvající operace je odpovědí ID operace. Zavolejte [klientovi. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metoda s ID operace k určení [stavu požadavku](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce v klientské knihovně QnA Maker pro rozhraní .NET:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Stažení znalostní báze](#download-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získání stavu operace](#get-status-of-an-operation)
* [Generovat odpověď ze znalostní báze](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu otevřete **soubor Program.cs** v upřednostňovaném editoru nebo rozhraní IDE. Nahraďte `using` existující kód `using` následujícími směrnicemi:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověření klienta pro vytváření znalostní báze

V **hlavní** metodě vytvořte proměnnou pro klíč Azure vašeho `QNAMAKER_SUBSCRIPTION_KEY`prostředku naváděnou z proměnné prostředí s názvem . Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

Dále vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte jej s koncovým bodem k vytvoření objektu [QnAMakerClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)

|Proměnná prostředí|proměnná|Příklad|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Klíč je řetězec 32 znaků a je k dispozici na portálu Azure, na prostředek QnA Maker, na stránce Rychlý start. To není stejné jako klíč koncového bodu předpověď.|
|`QNAMAKER_HOST`|`Endpoint`| Koncový bod pro vytváření ve `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátu aplikace obsahuje **název prostředku**. Toto není stejná adresa URL, která se používá k dotazování koncového bodu předpověď.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro generování odpovědi

V **hlavní** metodě vytvořte proměnnou pro ověřování prostředku vytaženou `QNAMAKER_ENDPOINT_HOSTNAME` `QNAMAKER_ENDPOINT_KEY`z proměnných prostředí s názvem a . Při publikování znalostní báze jsou tyto hodnoty vráceny. Po publikování najdete tato nastavení na stránce **Nastavení** portálu QnA Maker.

Vytvořte [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) pro dotaz znalostní báze pro generování odpovědi nebo trénování z aktivního učení.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá dvojice otázek a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
* Pro **soubory**použijte objekt [FileDTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet)
* Pro **adresy URL**použijte seznam řetězců.

Volání [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metoda pak předat id vrácené operace [na MonitorOperation](#get-status-of-an-operation) metoda dotazování na stav.

Poslední řádek následujícího kódu vrátí ID znalostní báze z odpovědi z MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Ujistěte se, [`MonitorOperation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně vytvořit znalostní báze.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat předáním ID znalostní bze a [updatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) obsahující [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)a [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO objekty [updateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metody. Pomocí metody [MonitorOperation určete,](#get-status-of-an-operation) zda byla aktualizace úspěšná.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Ujistěte se, [`MonitorOperation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně aktualizovat znalostní báze.

## <a name="download-a-knowledge-base"></a>Stažení znalostní báze

Pomocí metody [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) stáhněte databázi jako seznam [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). To _není_ ekvivalentní exportu portálu QnA Maker ze stránky **Nastavení,** protože výsledkem této metody není soubor TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) To trvá aktuální uložený a trénovaný model, na který odkazuje ID znalostní báze a publikuje jej v koncovém bodě.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generovat odpověď ze znalostní báze

Generovat odpověď z publikované znalostní báze pomocí [runtimeclient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) metoda. Tato metoda přijímá ID znalostní báze a [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Přístup k další vlastnosti QueryDTO, jako [je nahoře](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) a [kontextu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) pro použití v chatu robota.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) s parametrem ID znalostní báze.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získání stavu operace

Některé metody, například vytvořit a aktualizovat, může trvat tolik času, že namísto čekání na dokončení procesu je vrácena [operace.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) Pomocí [ID operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operace k dotazování (s logikou opakování) určete stav původní metody.

_Smyčka_ a _Task.Delay_ v následujícím bloku kódu se používají k simulaci logiky opakování. Ty by měly být nahrazeny vlastní logikou opakování.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) a lze je spustit jako jeden soubor.

```dotnetcli
dotnet run
```

[Zdrojový kód pro tento rychlý start](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) je k dispozici v QnA Maker C# ukázky Úložiště GitHub.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)