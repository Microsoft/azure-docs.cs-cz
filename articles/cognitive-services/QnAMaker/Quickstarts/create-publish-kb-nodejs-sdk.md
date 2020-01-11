---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Node. js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s knihovnou klienta QnA Maker pro Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/10/2020
ms.author: diberry
ms.openlocfilehash: 45917e9e44535261fcafb8d53a150356bb1757fb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890304"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Rychlý Start: QnA Maker klientskou knihovnu pro Node. js

Začněte s klientskou knihovnou QnA Maker pro Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.

Pomocí klientské knihovny QnA Maker pro Node. js:

* Vytvoření znalostní báze
* Správa znalostní báze
* Publikování znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) |  | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [ukázkách Node. js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure QnA Maker

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro QnA Maker pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači.

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro prostředek s názvem `QNAMAKER_SUBSCRIPTION_KEY` a `QNAMAKER_HOST`. Použijte hodnoty klíč a koncový bod nalezené v **klíčích** prostředku a stránkách **přehledu** v Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `npm init -y` pro vytvoření aplikace uzlu se souborem `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ms-rest-azure` a balíčky `azure-cognitiveservices-qnamaker` NPM:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Soubor `package.json` vaší aplikace se aktualizuje pomocí závislostí.


## <a name="object-model"></a>Objektový model

Klient QnA Maker je objekt [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) , který se ověřuje v Azure pomocí ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) vytvořit, spravovat a publikovat znalostní bázi.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) – metoda s ID operace, která určuje [stav žádosti](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující s QnA Maker klientské knihovny pro Node. js:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranit znalostní bázi](#delete-a-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidat závislosti

Vytvořte soubor s názvem `index.js`. Do souboru přidejte knihovnu QnA Maker a knihovnu Azure REST.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

|Proměnná prostředí|Proměnná Node. js|Příklad:|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|Klíč je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA Maker na stránce rychlý Start. To není totéž jako klíč koncového bodu předpovědi.|
|`QNAMAKER_HOST`|`endpoint`| Váš koncový bod pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`obsahuje název vašeho **prostředku**. Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Ověření klienta

Potom vytvořte objekt ServiceClientCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) . K získání objektu [znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) použijte objekt klienta.


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) .
* Pro **soubory**použijte objekt [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) .
* Pro **adresy URL**použijte seznam řetězců.

Zavolejte metodu [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) a potom předejte vrácené ID operace do metody [Operations. GetDetails](#get-status-of-an-operation) pro dotazování na stav.

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že zahrnuje funkci [`wait_for_operation`](#get-status-of-an-operation) , na kterou se odkazuje ve výše uvedeném kódu.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že do metody [aktualizace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) předáte ID znalostní báze a [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) obsahující objekty pro [Přidání](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [aktualizaci](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)a [odstranění](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO. K určení, jestli se aktualizace zdařila, použijte metodu [Operational detail](#get-status-of-an-operation) .

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

Aby bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že zahrnuje funkci [`wait_for_operation`](#get-status-of-an-operation) , na kterou se odkazuje ve výše uvedeném kódu.

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu.

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) s parametrem ID znalostní báze.

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) . K určení stavu původní metody použijte [ID operace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operace k dotazování (s logikou opakování).

Volání _setTimeout_ v následujícím bloku kódu slouží k simulaci asynchronního kódu. Nahraďte logikou opakování.

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node index.js` z adresáře aplikace.


Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) a lze je spustit jako jeden soubor.

```console
node index.js
```

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