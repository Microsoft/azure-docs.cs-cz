---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Python'
description: V tomto rychlém startu se dozvíte, jak začít s knihovnou klienta QnA Maker pro Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 39eeb00ba8b59219054c7743f19f6f378d941d5d
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104469"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Použití klientské knihovny QnA Maker pro Python pro:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat klíč koncového bodu předpovědi za běhu
* Počkat na dlouhodobě běžící úlohu
* Stáhnout znalostní báze
* Získat odpověď ze znalostní báze
* Odstranit znalostní bázi

[Referenční dokumentace](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/)  |  [Ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Použití klientské knihovny QnA Maker pro Python pro:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Počkat na dlouhodobě běžící úlohu
* Stáhnout znalostní báze
* Získat odpověď ze znalostní báze
* Odstranit znalostní bázi

[Referenční dokumentace](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu.
    * Poznámka: Nezapomeňte vybrat **spravované** zaškrtávací políčko.
    * Po nasazení prostředků QnA Maker vyberte **Přejít k prostředku**. K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

---

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte nový soubor Pythonu s názvem `quickstart-file.py` a importujte následující knihovny.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.

- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Přejděte na prostředek QnA Maker v Azure Portal a klikněte na **klíče a koncový bod** , abyste našli klíč pro vytváření (předplatných) a QnA maker koncový bod.

 ![Koncový bod pro vytváření QnA Maker](../media/keys-endpoint.png)
 
- Hodnota QNA_MAKER_RUNTIME_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
   
- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

> [!IMPORTANT]
> Přejít na Azure Portal a vyhledat klíč a koncový bod pro prostředek QnA Maker, který jste vytvořili v části požadavky. Budou umístěny na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.

- Hodnota QNA_MAKER_ENDPOINT má formát `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Přejděte na prostředek QnA Maker v Azure Portal a klikněte na **klíče a koncový bod** , abyste našli klíč pro vytváření (předplatných) a QnA maker koncový bod.

 ![Koncový bod pro vytváření QnA Maker](../media/keys-endpoint.png)
 
- V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Azure Key trezor](../../../key-vault/general/overview.md) poskytuje zabezpečené úložiště klíčů.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Objektové modely

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[QnA maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) používá dva různé objektové modely:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování a stažení znalostní báze Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** je objekt pro dotazování znalostní báze pomocí rozhraní GenerateAnswer API a posílání nových navrhovaných dotazů pomocí rozhraní API pro vlaky (jako součást [aktivního učení](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[QnA maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) používá následující objektový model:
* **[QnAMakerClient](#qnamakerclient-object-model)** je objekt, který slouží k vytvoření, správě, publikování, stažení a dotazování znalostní báze.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Objektový model QnAMakerClient

Klient pro vytváření QnA Maker je objekt [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) k vytvoření, správě a publikování znalostní báze.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Chcete-li určit [stav žádosti](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype), zavolejte metodu [Operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-) s ID operace.

### <a name="qnamakerruntimeclient-object-model"></a>Objektový model QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Předpověď QnA Maker klient je `QnAMakerRuntimeClient` objekt, který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje klíč runtime předpovědi, který se vrátí z klientského volání pro vytváření [. EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) po publikování znalostní báze.

Použijte `generate_answer` metodu k získání odpovědi z modulu runtime dotazu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

QnA Maker spravovaný prostředek nevyžaduje použití objektu QnAMakerRuntimeClient. Místo toho zavoláte [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) přímo na objekt [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) .

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Ověřování klienta pro vytváření znalostní báze

Vytvořte instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt CognitiveServicesCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Objekt klienta použijte k získání objektu [operací znalostní báze](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) .

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) ze tří zdrojů:

* Pro **redakční obsah** použijte objekt [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto) .
    * Chcete-li použít metadata a výzvy pro následné zpracování, použijte redakční kontext, protože tato data jsou přidána na jednotlivé úrovně páru QnA.
* Pro **soubory** použijte objekt [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto) . FileDTO zahrnuje název souboru i veřejnou adresu URL pro přístup k souboru.
* V případě **adres URL** použijte seznam řetězců, které reprezentují veřejně dostupné adresy URL.

Zavolejte metodu [Create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) a potom předejte vrácené ID operace do metody [Operations. GetDetails](#get-status-of-an-operation) pro dotazování na stav.

Poslední řádek následujícího kódu vrátí ID znalostní báze z odpovědi z MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že je zahrnutá [`_monitor_operation`](#get-status-of-an-operation) funkce, na kterou se odkazuje v kódu výše.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že do metody [aktualizace](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) předáte ID znalostní báze a [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) obsahující objekty pro [Přidání](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [aktualizaci](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)a [odstranění](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO. K určení, jestli se aktualizace zdařila, použijte metodu [Operational detail](#get-status-of-an-operation) .

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Aby [`_monitor_operation`](#get-status-of-an-operation) bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [Download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) ke stažení databáze jako seznamu [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [Publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Dotazování znalostní báze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Získat klíč běhu dotazu

Po publikování znalostní báze budete pro dotaz na modul runtime potřebovat klíč runtime dotazů. To není stejný klíč, který se používá k vytvoření původního objektu klienta.

K získání třídy [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) použijte metodu [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations#get-keys-custom-headers-none--raw-false----operation-config-) .

Použijte jednu z klíčových vlastností vrácených v objektu k dotazování znalostní báze.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Ověření modulu runtime pro vygenerování odpovědi

Vytvořte [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient) pro dotazování znalostní báze a vygenerujte odpověď nebo vlak z aktivního učení.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Použijte QnAMakerRuntimeClient k získání odpovědi ze znalostí nebo k odeslání nových navrhovaných otázek do znalostní báze pro [aktivní učení](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí metody QnAMakerRuntimeClient.runtime.generate_answer. Tato metoda přijímá ID znalostní báze a [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Získejte přístup k dalším vlastnostem QueryDTO, jako je například začátek a kontext, který se má použít v robotu chatu.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Vygenerovat odpověď ze znalostní báze Knowledge Base

Vygenerujte odpověď z publikované znalostní báze pomocí metody [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) . Tato metoda přijímá ID znalostní báze a [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Získejte přístup k dalším vlastnostem QueryDTO, jako je například začátek a kontext, který se má použít v robotu chatu.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Toto je jednoduchý příklad dotazování znalostní báze. Pokud chcete pochopit pokročilé scénáře dotazování, Projděte si [Další příklady dotazů](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [Delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-) s parametrem ID znalostní báze.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) . K určení stavu původní metody použijte ID operace z operace k dotazování (s logikou opakování).

Volání _setTimeout_ v následujícím bloku kódu slouží k simulaci asynchronního kódu. Nahraďte logikou opakování.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu Pythonu v souboru pro rychlý Start.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/version-1)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/version-2)

Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---
