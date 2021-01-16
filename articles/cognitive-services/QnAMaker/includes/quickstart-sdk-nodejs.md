---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Node.js'
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou QnA Maker pro Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 02dc36fb388135c31f639a26264579ab9de75b8c
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256482"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Pomocí klientské knihovny QnA Maker Node.js:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat klíč koncového bodu předpovědi za běhu
* Počkat na dlouhodobě běžící úlohu
* Stáhnout znalostní báze
* Získat odpověď ze znalostní báze
* Odstranit znalostní bázi

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [UkázkyNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Pomocí klientské knihovny QnA Maker Node.js:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Počkat na dlouhodobě běžící úlohu
* Stáhnout znalostní báze
* Získat odpověď ze znalostní báze
* Odstranit znalostní bázi

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [UkázkyNode.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Předpoklady

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč pro vytváření obsahu a prostředek. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a název prostředku z prostředku, který vytvoříte. Svůj klíč a název prostředku vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu.
    * Poznámka: Nezapomeňte vybrat **spravované** zaškrtávací políčko.
    * Po nasazení prostředků QnA Maker vyberte **Přejít k prostředku**. K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

---

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Spuštěním `npm init -y` příkazu vytvořte aplikaci uzlu se `package.json` souborem.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Nainstalujte následující balíčky NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Nainstalujte následující balíčky NPM:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

Soubor vaší aplikace `package.json` je aktualizovaný pomocí závislostí.

Vytvořte soubor s názvem index.js a importujte následující knihovny:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Vytvořte proměnnou pro klíč Azure prostředku a název prostředku.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.

- Vytvořte proměnné prostředí s názvem QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT a QNA_MAKER_RUNTIME_ENDPOINT k uložení těchto hodnot.
- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- Hodnota QNA_MAKER_RUNTIME_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.

- Vytvořte proměnné prostředí s názvem QNA_MAKER_SUBSCRIPTION_KEY a QNA_MAKER_ENDPOINT k uložení těchto hodnot.
- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Objektové modely

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[QnA maker](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) používá dva různé objektové modely:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování a stažení znalostní báze Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** je objekt pro dotazování znalostní báze pomocí rozhraní GenerateAnswer API a posílání nových navrhovaných dotazů pomocí rozhraní API pro vlaky (jako součást [aktivního učení](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[QnA maker](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) používá následující objektový model:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování, stažení a dotazování znalostní báze.

---

### <a name="qnamakerclient-object-model"></a>Objektový model QnAMakerClient

QnA Maker klient pro vytváření obsahu je objekt [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , který se ověřuje v Azure pomocí vašich přihlašovacích údajů, které obsahují váš klíč.

Po vytvoření klienta použijte [znalostní](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) bázi Knowledge Base k vytvoření, správě a publikování znalostní báze.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Chcete-li určit [stav žádosti](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest), zavolejte metodu [Client. Operations. GetDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) s ID operace.

### <a name="qnamakerruntimeclient-object-model"></a>Objektový model QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Předpověď QnA Maker klient je objekt QnAMakerRuntimeClient, který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje klíč modulu runtime předpovědi, který se vrátí z klientského volání pro vytváření, [klient. EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) po publikování znalostní báze.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

QnA Maker spravovaný prostředek nevyžaduje použití objektu QnAMakerRuntimeClient. Místo toho zavoláte [generateAnswer](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) přímo na objekt [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) .

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

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt ServiceClientCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) ze tří zdrojů:

* Pro **redakční obsah** použijte objekt [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
    * Chcete-li použít metadata a výzvy pro následné zpracování, použijte redakční kontext, protože tato data jsou přidána na jednotlivé úrovně páru QnA.
* Pro **soubory** použijte objekt [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) . FileDTO zahrnuje název souboru i veřejnou adresu URL pro přístup k souboru.
* V případě **adres URL** použijte seznam řetězců, které reprezentují veřejně dostupné adresy URL.

Krok vytvoření zahrnuje také vlastnosti pro znalostní báze:
* `defaultAnswerUsedForExtraction` – Co se vrátí, když se nenajde žádná odpověď
* `enableHierarchicalExtraction` -automaticky vytvářet relace výzvy mezi extrahovanými páry QnA
* `language` – Při vytváření první znalostní báze prostředků nastavte jazyk, který se má použít v indexu Azure Search.

Pomocí informací o znalostní bázi volejte metodu [Create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) . Informace o znalostní bázi jsou v podstatě objekt JSON.

Když se metoda Create vrátí, předejte vrácené ID operace do metody [wait_for_operation](#get-status-of-an-operation) k dotazování na stav. Metoda wait_for_operation vrátí po dokončení operace. Analyzuje `resourceLocation` hodnotu záhlaví vrácené operace za účelem získání nového ID znalostní báze.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Aby [`wait_for_operation`](#get-status-of-an-operation) bylo možné úspěšně vytvořit znalostní bázi, nezapomeňte zahrnout funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že do metody [aktualizace](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) předáte ID znalostní báze a [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) obsahující objekty pro [Přidání](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [aktualizaci](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)a [odstranění](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO. DTO jsou také v podstatě objekty JSON. K určení, jestli se aktualizace zdařila, použijte metodu [wait_for_operation](#get-status-of-an-operation) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Aby [`wait_for_operation`](#get-status-of-an-operation) bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [Download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) ke stažení databáze jako seznamu [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [Publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu. Zkontrolujte kód odpovědi HTTP a ověřte, že publikování bylo úspěšné.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Dotazování znalostní báze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Získat klíč běhu dotazu

Po publikování znalostní báze budete pro dotaz na modul runtime potřebovat klíč runtime dotazů. To není stejný klíč, který se používá k vytvoření původního objektu klienta.

K získání třídy [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) použijte metodu [EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) .

Použijte jednu z klíčových vlastností vrácených v objektu k dotazování znalostní báze.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

Vytvořte QnAMakerRuntimeClient pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Použijte QnAMakerRuntimeClient k získání odpovědi ze znalostí nebo k odeslání nových navrhovaných otázek do znalostní báze pro [aktivní učení](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí metody RuntimeClient. Runtime. generateAnswer. Tato metoda přijímá ID znalostní báze a [QueryDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Získejte přístup k dalším vlastnostem QueryDTO, jako je například začátek a kontext, který se má použít v robotu chatu.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí metody QnAMakerClient. knowledgeers. generateAnswer. Tato metoda přijímá ID znalostní báze a [QueryDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Získejte přístup k dalším vlastnostem QueryDTO, jako je například začátek a kontext, který se má použít v robotu chatu.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Toto je jednoduchý příklad dotazování znalostní báze. Pokud chcete pochopit pokročilé scénáře dotazování, Projděte si [Další příklady dotazů](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [Delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) s parametrem ID znalostní báze.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . K určení stavu původní metody použijte [ID operace](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operace k dotazování (s logikou opakování).

Volání _delayTimer_ v následujícím bloku kódu slouží k simulaci logiky opakování. Nahraďte vlastní logikou opakování.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node index.js` příkazu z adresáře aplikace.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---
