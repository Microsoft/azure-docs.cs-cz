---
title: 'Rychlý Start: Klientská knihovna QnA Maker pro .NET'
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou QnA Maker pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: e487783e506d16006231b07b9a86f93864f51903
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765131"
---
Pomocí klientské knihovny QnA Maker pro .NET:

 * Vytvoření znalostní báze
 * Aktualizace znalostní báze
 * Publikování znalostní báze, čekání na dokončení publikování
 * Získat klíč koncového bodu předpovědi za běhu
 * Stáhnout znalostní báze
 * Odstranění znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [Ukázky jazyka C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadované součásti

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení


#### <a name="visual-studio-ide"></a>[Integrované vývojové prostředí sady Visual Studio](#tab/visual-studio)

Pomocí sady Visual Studio vytvořte aplikaci .NET Core a nainstalujte knihovnu klienta tak, že kliknete pravým tlačítkem na řešení v **Průzkumník řešení** a vyberete **Spravovat balíčky NuGet**. Ve Správci balíčků, který se otevře, vyberte **Procházet**, zaškrtněte políčko **Zahrnout předprodejní**a vyhledejte `(package-name)` . Vyberte verzi `(version)` a pak **nainstalujte**.

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `qna-maker-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
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

V adresáři aplikace nainstalujte QnA Maker klientskou knihovnu pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.1.0
```


---

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), který obsahuje příklady kódu v tomto rychlém startu.

Z adresáře projektu otevřete soubor *program.cs* a přidejte následující `using` direktivy:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

V `Main` metodě aplikace přidejte proměnné a kód, které jsou uvedeny v následujících částech, aby bylo možné použít běžné úkoly v rámci tohoto rychlého startu.

## <a name="object-models"></a>Objektové modely

QnA Maker používá dva různé objektové modely:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování a stažení znalostní báze Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** je objekt pro dotazování znalostní báze pomocí rozhraní GenerateAnswer API a posílání nových navrhovaných dotazů pomocí rozhraní API pro vlaky (jako součást [aktivního učení](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>Objektový model QnAMakerClient

Klient pro vytváření QnA Maker je objekt [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) k vytvoření, správě a publikování znalostní báze.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metoda s ID operace k určení [stavu požadavku](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet).

### <a name="qnamakerruntimeclient-object-model"></a>Objektový model QnAMakerRuntimeClient

Předpověď QnA Maker klient je objekt [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje klíč modulu runtime předpovědi, vrácený při volání klienta pro vytváření obsahu `client.EndpointKeys.GetKeys` po publikování znalostní báze.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny QnA Maker pro .NET:

* [Ověření klienta pro vytváření obsahu](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Stáhnout znalostní bázi](#download-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získat klíč běhu dotazu](#get-query-runtime-key)
* [Získat stav operace](#get-status-of-an-operation)
* [Ověření klienta nástroje Query runtime](#authenticate-the-runtime-for-generating-an-answer)
* [Vygenerovat odpověď ze znalostní báze Knowledge Base](#generate-an-answer-from-the-knowledge-base)

## <a name="using-this-example-knowledge-base"></a>V tomto příkladu znalostní báze Knowledge Base

Znalostní báze v tomto rychlém startu začíná 2 QnA páry, to je prováděno za účelem zjednodušení příkladu a použití vysoce předvídatelných identifikátorů, které je možné použít v metodě Update. přiřadíte tak následné výzvy k dotazům na nové páry. Toto bylo plánováno a implementováno v konkrétním pořadí pro účely tohoto rychlého startu.

Pokud plánujete vyvíjet znalostní bázi v průběhu času s následnými výzvami, které jsou závislé na stávajících QnAch párů, můžete se rozhodnout:
* V případě větších knowledgebases spravujte znalostní bázi v textovém editoru nebo v nástroji TSV, který podporuje automatizaci, a potom zcela nahraďte znalostní bázi aktualizací.
* V případě menších knowledgebases Spravujte všechny následné výzvy úplně na portálu QnA Maker.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověřování klienta pro vytváření znalostní báze

V metodě **Main** Vytvořte proměnnou pro klíč Azure prostředku a název prostředku. Adresy URL pro vytváření obsahu a předpovědi používají název prostředku jako subdoménu.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.
> K vytvoření vaší znalostní báze budete potřebovat celý klíč. Z koncového bodu potřebujete jenom název prostředku. Formát je ' '.
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](https://docs.microsoft.com/azure/key-vault/key-vault-overview) poskytuje zabezpečené úložiště klíčů.

Potom vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) .

|proměnná|Příklad|
|--|--|
|`authoringKey`|Klíč je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA Maker na stránce **klíče a koncové body** . To není stejné jako klíč za běhu předpovědi.|
|`resourceName`| Název prostředku se používá ve formátu `https://{resourceName}.cognitiveservices.azure.com` . Nejedná se o stejnou adresu URL, která se používá k dotazování modulu runtime předpovědi.|
||||

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) .
    * Chcete-li použít metadata a výzvy pro následné zpracování, použijte redakční kontext, protože tato data jsou přidána na jednotlivé úrovně páru QnA.
* Pro **soubory**použijte objekt [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) .
* Pro **adresy URL**použijte seznam řetězců.

Zavolejte metodu [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) a pak předejte vrácené ID operace do metody [MonitorOperation](#get-status-of-an-operation) pro dotazování na stav.

Poslední řádek následujícího kódu vrátí ID znalostní báze z odpovědi z MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=37-38)]

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že je zahrnutá [`MonitorOperation`](#get-status-of-an-operation) funkce, na kterou se odkazuje v kódu výše.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že předáte ID znalostní báze a [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) obsahující objekty pro [Přidání](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [aktualizaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)a [odstranění](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO do metody [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) . K určení, jestli se aktualizace zdařila, použijte metodu [MonitorOperation](#get-status-of-an-operation) .

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Aby [`MonitorOperation`](#get-status-of-an-operation) bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) ke stažení databáze jako seznamu [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3,4)]

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho v rámci vašeho koncového bodu.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]

## <a name="get-query-runtime-key"></a>Získat klíč běhu dotazu

Po publikování znalostní báze budete pro dotaz na modul runtime potřebovat klíč runtime dotazů. To není stejný klíč, který se používá k vytvoření původního objektu klienta.

Použijte metodu [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) k získání třídy [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) .

Použijte jednu z klíčových vlastností vrácených v objektu k dotazování znalostní báze.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

Vytvořte [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Použijte QnAMakerRuntimeClient k získání odpovědi ze znalostí nebo k odeslání nových navrhovaných otázek do znalostní báze pro [aktivní učení](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Metoda [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) Tato metoda přijímá ID znalostní báze a [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Získejte přístup k dalším vlastnostem QueryDTO, jako je například [začátek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) a [kontext](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , který se má použít v robotu chatu.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Toto je jednoduchý příklad pro nás dotazování znalostní báze. Pokud chcete pochopit pokročilé scénáře dotazování, Projděte si [Další příklady dotazů](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) s parametrem ID znalostní báze.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) . K určení stavu původní metody použijte [ID operace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operace k dotazování (s logikou opakování).

_Smyčka_ a _úloha. zpoždění_ v následujícím bloku kódu slouží k simulaci logiky opakování. Ty by měly být nahrazeny vlastní logikou opakování.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `dotnet run` příkazu z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) a lze je spustit jako jeden soubor.

```dotnetcli
dotnet run
```

* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).