---
title: 'Rychlý Start: Klientská knihovna QnA Maker pro .NET'
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou QnA Maker pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 0f1e779fea0fe4f3f3a88b91091d11442e2f958b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803396"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Pomocí klientské knihovny QnA Maker pro .NET:

 * Vytvoření znalostní báze
 * Aktualizace znalostní báze
 * Publikování znalostní báze
 * Získat klíč koncového bodu předpovědi za běhu
 * Počkat na dlouhodobě běžící úlohu
 * Stáhnout znalostní báze
 * Získat odpověď ze znalostní báze
 * Odstranění znalostní báze

[Referenční dokumentace](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1)  |  [Ukázky jazyka C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Pomocí klientské knihovny QnA Maker pro .NET:

 * Vytvoření znalostní báze
 * Aktualizace znalostní báze
 * Publikování znalostní báze
 * Počkat na dlouhodobě běžící úlohu
 * Stáhnout znalostní báze
 * Získat odpověď ze znalostní báze
 * Odstranění znalostní báze

[Referenční dokumentace](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1)  |  [Ukázky jazyka C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Předpoklady

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč pro vytváření a název prostředku. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a název prostředku z prostředku, který vytvoříte. Svůj klíč a název prostředku vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu.
    * Poznámka: Nezapomeňte vybrat **spravované** zaškrtávací políčko.
    * Po nasazení prostředků QnA Maker vyberte **Přejít k prostředku**. K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

---

## <a name="setting-up"></a>Nastavení

### <a name="cli"></a>Rozhraní příkazového řádku

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `qna-maker-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program. cs*.

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

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), který obsahuje příklady kódu v tomto rychlém startu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), který obsahuje příklady kódu v tomto rychlém startu.

---

### <a name="using-directives"></a>Direktivy using

Z adresáře projektu otevřete soubor *program. cs* a přidejte následující `using` direktivy:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Klíč předplatného a koncové body prostředků

V `Main` metodě aplikace přidejte proměnné a kód zobrazené v následující části, chcete-li použít běžné úkoly v rámci tohoto rychlého startu.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 

Používáme klíč předplatného a vytváření klíčů Key interchangably. Další informace o vytváření klíčů najdete [pod klíči v QnA maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Přejděte na prostředek QnA Maker v Azure Portal a klikněte na **klíče a koncový bod** , abyste našli klíč pro vytváření (předplatných) a QnA maker koncový bod.

 ![Koncový bod QnA Maker runtime](../media/keys-endpoint.png)

- Hodnota QNA_MAKER_RUNTIME_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.azurewebsites.net` .  
      
- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 

Používáme klíč předplatného a vytváření klíčů Key interchangably. Další informace o vytváření klíčů najdete [pod klíči v QnA maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Přejděte na prostředek QnA Maker v Azure Portal a klikněte na **klíče a koncový bod** , abyste našli klíč pro vytváření (předplatných) a QnA maker koncový bod.

 ![Koncový bod QnA Maker runtime](../media/keys-endpoint.png)

- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Objektové modely

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[QnA maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) používá dva různé objektové modely:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování a stažení znalostní báze Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** je objekt pro dotazování znalostní báze pomocí rozhraní GenerateAnswer API a posílání nových navrhovaných dotazů pomocí rozhraní API pro vlaky (jako součást [aktivního učení](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[QnA maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) používá následující objektový model:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování, stažení a dotazování znalostní báze.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Objektový model QnAMakerClient

Klient pro vytváření QnA Maker je objekt [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) k vytvoření, správě a publikování znalostní báze.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) metoda s ID operace k určení [stavu požadavku](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Objektový model QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Předpověď QnA Maker klient je objekt [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje klíč modulu runtime předpovědi, vrácený při volání klienta pro vytváření obsahu `client.EndpointKeys.GetKeys` po publikování znalostní báze.

Použijte metodu [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) k získání odpovědi z modulu runtime dotazu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

QnA Maker spravovaný prostředek nevyžaduje použití objektu **QnAMakerRuntimeClient** . Místo toho zavoláte [QnAMakerClient.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase)Knowledge Base. Metoda [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync)

---

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny QnA Maker pro .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

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

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

* [Ověření klienta pro vytváření obsahu](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Stáhnout znalostní bázi](#download-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)
* [Vygenerovat odpověď ze znalostní báze Knowledge Base](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověřování klienta pro vytváření znalostní báze

Vytvořte instanci objektu klienta s klíčem a použijte ho u svého prostředku k vytvoření koncového bodu pro vytvoření [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) s vaším koncovým bodem a klíčem. Vytvořte objekt [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) ze tří zdrojů:

* Pro **redakční obsah** použijte objekt [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) .
    * Chcete-li použít metadata a výzvy pro následné zpracování, použijte redakční kontext, protože tato data jsou přidána na jednotlivé úrovně páru QnA.
* Pro **soubory** použijte objekt [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) . FileDTO zahrnuje název souboru i veřejnou adresu URL pro přístup k souboru.
* V případě **adres URL** použijte seznam řetězců, které reprezentují veřejně dostupné adresy URL.

Krok vytvoření zahrnuje také vlastnosti pro znalostní báze:
* `defaultAnswerUsedForExtraction` – Co se vrátí, když se nenajde žádná odpověď
* `enableHierarchicalExtraction` -automaticky vytvářet relace výzvy mezi extrahovanými páry QnA
* `language` – Při vytváření první znalostní báze prostředků nastavte jazyk, který se má použít v indexu Azure Search.

Zavolejte metodu [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) a pak předejte vrácené ID operace do metody [MonitorOperation](#get-status-of-an-operation) pro dotazování na stav.

Poslední řádek následujícího kódu vrátí ID znalostní báze z odpovědi z MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že je zahrnutá [`MonitorOperation`](#get-status-of-an-operation) funkce, na kterou se odkazuje v kódu výše.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že předáte ID znalostní báze a [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) obsahující objekty pro [Přidání](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [aktualizaci](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)a [odstranění](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO do metody [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) . K určení, jestli se aktualizace zdařila, použijte metodu [MonitorOperation](#get-status-of-an-operation) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Aby [`MonitorOperation`](#get-status-of-an-operation) bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) ke stažení databáze jako seznamu [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho v rámci vašeho koncového bodu. Tento krok je nezbytný pro dotazování znalostní báze Knowledge Base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Získat klíč běhu dotazu

Po publikování znalostní báze budete pro dotaz na modul runtime potřebovat klíč runtime dotazů. To není stejný klíč, který se používá k vytvoření původního objektu klienta.

Použijte metodu [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) k získání třídy [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) .

Použijte jednu z klíčových vlastností vrácených v objektu k dotazování znalostní báze.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

K dotazování znalostní báze je potřeba klíč za běhu.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

Vytvořte [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Použijte QnAMakerRuntimeClient pro:
* získat odpověď ze znalostní báze
* k odeslání nových navrhovaných otázek do znalostní báze pro [aktivní učení](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze s použitím [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Metoda [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) Tato metoda přijímá ID znalostní báze a [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Získejte přístup k dalším vlastnostem QueryDTO, jako je například [začátek](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) a [kontext](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) , který se má použít v robotu chatu.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí [znalostní báze QnAMakerClient.](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase)Knowledge Base. Metoda [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) Tato metoda přijímá ID znalostní báze a [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Přihlaste se k dalším vlastnostem QueryDTO, jako je například [horní](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [kontext](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) a [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) , které se mají použít v robotu chatu.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Toto je jednoduchý příklad dotazování znalostní báze. Pokud chcete pochopit pokročilé scénáře dotazování, Projděte si [Další příklady dotazů](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Pomocí metody [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) s parametrem ID znalostní báze odstraňte znalostní bázi Knowledge Base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) . K určení stavu původní metody použijte [ID operace](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) z operace k dotazování (s logikou opakování).

_Smyčka_ a _úloha. zpoždění_ v následujícím bloku kódu slouží k simulaci logiky opakování. Ty by měly být nahrazeny vlastní logikou opakování.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `dotnet run` příkazu z adresáře aplikace.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
