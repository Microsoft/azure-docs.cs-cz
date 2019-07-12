---
title: 'Rychlý start: Nástroj QnA Maker Klientská knihovna pro .NET'
titlesuffix: Azure Cognitive Services
description: Začínáme s QnA Maker Klientská knihovna pro .NET. Postupujte podle následujících kroků instalace balíčku a vyzkoušejte si ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828117"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Rychlý start: Nástroj QnA Maker Klientská knihovna pro .NET

Začínáme s QnA Maker Klientská knihovna pro .NET. Postupujte podle následujících kroků instalace balíčku a vyzkoušejte si ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům. 

Použití nástroje QnA Maker klientské knihovny pro .NET:

* Vytvoření znalostní báze 
* Správa znalostní báze
* Publikování znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [balíčku (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# ukázky](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořit zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku QnA Maker Azure

Azure Cognitive Services jsou reprezentovány prostředky Azure, které se přihlásíte k odběru. Vytvoření prostředku pro použití nástroje QnA Maker [webu Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. 

Po získání klíče z vašeho prostředku [vytvořit proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) klíč s názvem `QNAMAKER_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Vytvořte nový C# aplikace

Vytvořte novou aplikaci .NET Core v preferovaného editoru nebo prostředí IDE. 

V okně konzoly (jako je cmd, PowerShell nebo Bash), použijte dotnet `new` příkaz pro vytvoření nových konzolovou aplikaci s názvem `qna-maker-quickstart`. Tento příkaz vytvoří jednoduchý "Hello World" C# projekt s jeden zdrojový soubor: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Změňte váš adresář na složku nově vytvořené aplikace. Můžete vytvářet aplikace pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```


### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte nástroje QnA Maker Klientská knihovna pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Pokud používáte Visual Studio IDE, je k dispozici ke stažení balíčku NuGet klientské knihovny.


## <a name="object-model"></a>Objektový model

Klient nástroje QnA Maker [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objekt, který ověřuje do Azure s využitím Microsoft.Rest.ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta pomocí [znalostní báze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) vlastnost vytvářet, spravovat a publikovat znalostní báze. 

Správa znalostní báze odesláním objekt JSON. Pro okamžité operace vrátí metoda obvykle označující stav objektu JSON. Pro dlouho běžící operace odpovědi je ID operace. Volání [klienta. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metodu s ID operace k určení [stav žádosti o](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující při používání nástroje QnA Maker klientské knihovny pro .NET:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Stáhněte si znalostní báze](#download-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranit znalostní báze](#delete-a-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidat závislosti

Otevřete z adresáře projektu **Program.cs** souboru do preferovaného editoru nebo prostředí IDE. Nahraďte existující `using` kód následujícím kódem `using` direktivy:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

V **hlavní** metody vytvoření proměnné pro váš prostředek Azure klíč načtený z proměnné prostředí s názvem `QNAMAKER_SUBSCRIPTION_KEY`. Pokud vytvoříte proměnné prostředí po spuštění aplikace, editor, integrovaném vývojovém prostředí nebo prostředí, kterých je spuštěná muset zavřít a znovu načíst pro přístup k proměnné. Později se vytvoří metody.

Dále vytvořte [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) objekt pomocí klíče a jeho použití s váš koncový bod pro vytvoření [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektu.

Pokud váš klíč není v `westus` oblasti, jako tento ukázkový kód ukazuje, změňte umístění **koncový bod** proměnné. Toto umístění se nachází na **přehled** stránky pro prostředek nástroje QnA Maker na webu Azure Portal.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Páry otázek a odpovědí pro ukládá znalostní báze [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objekt z tři zdroje:

* Pro **redakční obsah**, použijte [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objektu.
* Pro **soubory**, použijte [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objektu. 
* Pro **adresy URL**, použijte seznam řetězců.

Volání [asynchronně](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metoda pak předejte vrácené operace ID se má [MonitorOperation](#get-status-of-an-operation) metody pro dotazování na stav. 

Poslední řádek následující kód vrátí z odpovědi z MonitorOoperation ID znalostní báze knowledge base. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní báze můžete aktualizovat předáním ID znalostní báze knowledge base a [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) obsahující [přidat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [aktualizovat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet), a [odstranit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet)DTO objektů [metod UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metody. Použití [MonitorOperation](#get-status-of-an-operation) metodou ke zjištění, pokud aktualizace byla úspěšná.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Stáhněte si znalostní báze

Použití [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metoda stažení databáze jako seznam [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Toto je _není_ ekvivalentní k exportu portál QnA Maker z **nastavení** stránce vzhledem k tomu, že výsledek této metody není na soubor TSV.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikování znalostní báze pomocí [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metody. To trvá aktuální uložené a trénovaného modelu, odkazuje ID znalostní báze knowledge base a, který publikuje na koncový bod. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstranit pomocí znalostní báze knowledge base [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metodu s parametrem ID znalostní báze knowledge base. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jak vytvořit a aktualizovat, může trvat dostatek času, který čekat na dokončení procesu [operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) je vrácena. Použití [ID operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operace dotazování (spolu s logikou opakování) k určení stavu původní metodu. 

_Smyčky_ a _Task.Delay_ v následující bloku kódu, které se používají pro simulaci logika opakovaných pokusů. To mělo být nahrazeno s vlastní logikou opakování. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Spuštění aplikace

Spustit aplikaci s dotnet `run` příkaz z adresáře aplikace.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny prostředky, které s ním spojená.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Kurz: Vytvoření a odpovězte na znalostní bázi](../tutorials/create-publish-query-in-portal.md)

* [Co je API nástroje QnA Maker?](../Overview/overview.md)
* [Úprava znalostní báze](../how-to/edit-knowledge-base.md)
* [Použití analýz](../how-to/get-analytics-knowledge-base.md)
* Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).