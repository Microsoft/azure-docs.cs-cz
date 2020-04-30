---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Node. js'
description: V tomto rychlém startu se dozvíte, jak začít s knihovnou klienta QnA Maker pro Node. js.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 48038c8e7e8250190d79aba7901567e18881e912
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204003"
---
Pomocí klientské knihovny QnA Maker pro Node. js:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat publikovaný klíč koncového bodu
* Počkat na dlouhodobě běžící úlohu
* Odstranit znalostní bázi

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)balíčku | [zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | knihovny dokumentace[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node. js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org).
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň (`F0`) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure QnA Maker

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro QnA Maker pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači.

Po získání klíče a koncového bodu z prostředku Získejte hodnoty z Azure Portal pro nový prostředek na stránce rychlý Start.

[Vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)s názvem `QNAMAKER_AUTHORING_KEY` a `QNAMAKER_ENDPOINT`. Můžete zkopírovat soubor [. env. Sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) `.env` a použít proměnné prostředí v tomto souboru.

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

Nainstalujte požadované a volitelné balíčky NPM:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

`package.json` Soubor vaší aplikace je aktualizovaný pomocí závislostí. `dotenv` Je volitelná a slouží k nastavení proměnných prostředí v textovém souboru. Nekontrolujte `.env` do správy zdrojového kódu.


## <a name="object-model"></a>Objektový model

Klient QnA Maker je objekt [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , který se ověřuje v Azure pomocí ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) vytvořit, spravovat a publikovat znalostní bázi.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) – metoda s ID operace, která určuje [stav žádosti](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující s QnA Maker klientské knihovny pro Node. js:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získat publikovaný koncový bod](#get-published-endpoint)
* [Získat stav operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidat závislosti

Vytvořte soubor s názvem `index.js`. Přidejte knihovnu QnA Maker a závislosti do souboru.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

|Proměnná prostředí|Proměnná Node. js|Příklad|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Klíč je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA Maker na stránce rychlý Start. To není totéž jako klíč koncového bodu předpovědi.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Váš koncový bod pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`obsahuje název vašeho **prostředku**. Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Ověření klienta

Potom vytvořte objekt ApiKeyCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . K získání objektu [klienta znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) použijte objekt klienta.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Pro **soubory**použijte objekt [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) .
* Pro **adresy URL**použijte seznam řetězců.

Pomocí informací o znalostní bázi volejte metodu [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) . Informace o znalostní bázi jsou v podstatě objekt JSON.

Když se metoda Create vrátí, předejte vrácené ID operace do metody [wait_for_operation](#get-status-of-an-operation) k dotazování na stav. Metoda wait_for_operation vrátí po dokončení operace. Analyzuje `resourceLocation` hodnotu záhlaví vrácené operace za účelem získání nového ID znalostní báze.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Aby bylo možné úspěšně vytvořit [`wait_for_operation`](#get-status-of-an-operation) znalostní bázi, ujistěte se, že je zahrnutá funkce, na kterou se odkazuje v kódu výše.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že do metody [aktualizace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) předáte ID znalostní báze a [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) obsahující objekty pro [Přidání](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [aktualizaci](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)a [odstranění](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO. DTO jsou také v podstatě objekty JSON. K určení, jestli se aktualizace zdařila, použijte metodu [wait_for_operation](#get-status-of-an-operation) .

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Aby bylo možné úspěšně aktualizovat [`wait_for_operation`](#get-status-of-an-operation) znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu. Zkontrolujte kód odpovědi HTTP a ověřte, že publikování bylo úspěšné.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Získat publikovaný koncový bod

Po publikování znalostní báze získáte přístup k publikované znalostní bázi prostřednictvím rozhraní generateAnswer API pro předpověď dotazů za běhu. K tomu budete potřebovat klíč koncového bodu modulu runtime. To se liší od klíčového obsahu.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Volání vrátí dva klíče koncového bodu. Pro přístup ke koncovému bodu runtime je potřeba jenom jeden.

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) s parametrem ID znalostní báze.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . K určení stavu původní metody použijte [ID operace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operace k dotazování (s logikou opakování).

Volání _delayTimer_ v následujícím bloku kódu slouží k simulaci logiky opakování. Nahraďte vlastní logikou opakování.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node index.js` příkazu z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) a lze je spustit jako jeden soubor.

```console
node index.js
```

Program vytiskne stav do konzoly:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)