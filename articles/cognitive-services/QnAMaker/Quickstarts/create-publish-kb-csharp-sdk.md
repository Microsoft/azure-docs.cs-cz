---
title: 'Rychlý Start: Klientská knihovna QnA Maker pro .NET'
titleSuffix: Azure Cognitive Services
description: Začněte s klientskou knihovnou QnA Maker pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 1c403d4d9b5c95c1cb1079b951a26dcaabb805fe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405870"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Rychlý Start: Klientská knihovna QnA Maker pro .NET

Začněte s klientskou knihovnou QnA Maker pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům. 

Pomocí klientské knihovny QnA Maker pro .NET:

* Vytvoření znalostní báze 
* Správa znalostní báze
* Publikování znalostní báze
* Vygenerovat odpověď ze znalostní báze Knowledge Base

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [ C# ukázka | ukázek](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp) [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [balíčků (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure QnA Maker

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro QnA Maker pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. 

Po získání klíče a koncového bodu pro váš prostředek [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `QNAMAKER_SUBSCRIPTION_KEY`. Název prostředku se používá jako součást adresy URL koncového bodu.

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz `dotnet new` a vytvořte novou konzolovou aplikaci s názvem `qna-maker-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*. 

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

### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte QnA Maker klientskou knihovnu pro .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.


## <a name="object-model"></a>Objektový model

Klient QnA Maker je objekt [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) k vytvoření, správě a publikování znalostní báze. 

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metoda s ID operace k určení [stavu požadavku](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny QnA Maker pro .NET:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Stáhnout znalostní bázi](#download-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranit znalostní bázi](#delete-a-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)
* [Vygenerovat odpověď ze znalostní báze Knowledge Base](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **program.cs** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující kód `using` následujícími direktivami `using`:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověřování klienta pro vytváření znalostní báze

V metodě **Main** Vytvořte proměnnou pro klíč Azure prostředku načtenou z proměnné prostředí s názvem `QNAMAKER_SUBSCRIPTION_KEY`. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

Potom vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) .

Změňte proměnnou **koncového bodu** `<your-custom-domain>`na název vaší vlastní domény. Toto umístění najdete na stránce **Přehled** pro prostředek QnA Maker v Azure Portal.

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

V metodě **Main** Vytvořte proměnnou pro ověřování prostředku z proměnných prostředí s názvem `QNAMAKER_ENDPOINT_HOSTNAME` a `QNAMAKER_ENDPOINT_KEY`. Když publikujete znalostní bázi, vrátí se tyto hodnoty. Po publikování můžete tato nastavení najít na stránce **Nastavení** na portálu QnA maker. 

Vytvořte [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) .
* Pro **soubory**použijte objekt [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) . 
* Pro **adresy URL**použijte seznam řetězců.

Zavolejte metodu [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) a pak předejte vrácené ID operace do metody [MonitorOperation](#get-status-of-an-operation) pro dotazování na stav. 

Poslední řádek následujícího kódu vrátí ID znalostní báze z odpovědi z MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že zahrnuje funkci [`MonitorOperation`](#get-status-of-an-operation) , na kterou se odkazuje ve výše uvedeném kódu. 

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že předáte ID znalostní báze a [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) obsahující objekty pro [Přidání](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [aktualizaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)a [odstranění](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO do metody [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) . K určení, jestli se aktualizace zdařila, použijte metodu [MonitorOperation](#get-status-of-an-operation) .

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Aby bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že zahrnuje funkci [`MonitorOperation`](#get-status-of-an-operation) , na kterou se odkazuje ve výše uvedeném kódu. 

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) ke stažení databáze jako seznamu [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Metoda [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) Tato metoda přijímá ID znalostní báze a [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Získejte přístup k dalším vlastnostem QueryDTO, jako je například [začátek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) a [kontext](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , který se má použít v robotu chatu. 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) s parametrem ID znalostní báze. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) . K určení stavu původní metody použijte [ID operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operace k dotazování (s logikou opakování). 

_Smyčka_ a _úloha. zpoždění_ v následujícím bloku kódu slouží k simulaci logiky opakování. Ty by měly být nahrazeny vlastní logikou opakování. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) a lze je spustit jako jeden soubor.

```dotnetcli
dotnet run
```

[Zdrojový kód pro tento rychlý Start](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) je k dispozici v C# úložišti GitHub QnA maker Samples.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Kurz: vytvoření a odpověď na databázi znalostí](../tutorials/create-publish-query-in-portal.md)

* [Co je rozhraní API služby QnA Maker?](../Overview/overview.md)
* [Úprava znalostní báze](../how-to/edit-knowledge-base.md)
* [Získat analýzu využití](../how-to/get-analytics-knowledge-base.md)
