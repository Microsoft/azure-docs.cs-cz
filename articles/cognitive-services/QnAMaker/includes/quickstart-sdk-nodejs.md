---
title: 'Úvodní příručka: Klientská knihovna QnA Maker pro soubor Node.js'
description: Tento rychlý start ukazuje, jak začít s klientskou knihovnou QnA Maker pro Node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021228"
---
Pomocí klientské knihovny QnA Maker pro soubor Node.js můžete:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat publikovaný klíč koncového bodu
* Čekání na dlouhotrvající úlohu
* Odstranění znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Vzorové soubory dat knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [souboru Node.js](https://nodejs.org).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure pro QnA Maker

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro QnA Maker pomocí [portálu Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači.

Po získání klíče a koncového bodu z vašeho prostředku, získat hodnoty z portálu Azure, pro váš nový prostředek, na stránce Rychlý start.

[Vytvořte proměnné](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)prostředí `QNAMAKER_AUTHORING_KEY` `QNAMAKER_ENDPOINT`s názvem a . Ukázkový soubor [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) `.env` můžete zkopírovat do tohoto souboru a použít proměnné prostředí.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Spuštěním `npm init -y` příkazu vytvořte aplikaci `package.json` uzlu se souborem.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte požadované a volitelné balíčky NPM:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Soubor aplikace `package.json` se aktualizuje o závislosti. Je `dotenv` volitelný a slouží k nastavení proměnných prostředí v textovém souboru. Nezaškrtávejte `.env` do správy zdrojového kódu.


## <a name="object-model"></a>Objektový model

Klient QnA Maker je [qnAmakerklient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) objekt, který se ověřuje do Azure pomocí ServiceClientCredentials, který obsahuje váš klíč.

Po vytvoření klienta použijte vlastnost [Znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) Vytvořit, spravovat a publikovat znalostní bázi.

Spravujte znalostní bázi odesláním objektu JSON. Pro okamžité operace metoda obvykle vrátí objekt JSON označující stav. Pro dlouhotrvající operace je odpovědí ID operace. Zavolejte [klientovi. Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) metoda s ID operace k určení [stavu požadavku](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny QnA Maker pro soubor Node.js:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získání publikovaného koncového bodu](#get-published-endpoint)
* [Získání stavu operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidání závislostí

Vytvořte soubor s názvem `index.js`. Přidejte do souboru knihovnu QnA Maker a závislosti.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

|Proměnná prostředí|Proměnná Node.js|Příklad|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|Klíč je řetězec 32 znaků a je k dispozici na portálu Azure, na prostředek QnA Maker, na stránce Rychlý start. To není stejné jako klíč koncového bodu předpověď.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Koncový bod pro vytváření ve `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátu aplikace obsahuje **název prostředku**. Toto není stejná adresa URL, která se používá k dotazování koncového bodu předpověď.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Ověření klienta

Dále vytvořte objekt ApiKeyCredentials s klíčem a použijte jej s koncovým bodem k vytvoření objektu [QnAMakerClient.](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) Pomocí objektu klienta získáte objekt [klienta znalostní báze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) Knowledge Base.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá dvojice otázek a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)
* Pro **soubory**použijte objekt [FileDTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest)
* Pro **adresy URL**použijte seznam řetězců.

Volání metody [vytvoření](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) s informacemi znalostní báze. Informace znalostní báze je v podstatě objekt JSON.

Když se vrátí metoda create, předaj ID vrácené operace [metodě wait_for_operation](#get-status-of-an-operation) k dotazování stavu. Metoda wait_for_operation vrátí po dokončení operace. Analyzovat hodnotu `resourceLocation` záhlaví vrácené operace získat nové ID znalostní báze.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Ujistěte se, [`wait_for_operation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně vytvořit znalostní báze.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat předáním ID znalostní b-boperačního [souboru](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) obsahujícího [objekty add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)a [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO do metody [aktualizace.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) DTO jsou také v podstatě JSON objekty. Pomocí [metody wait_for_operation](#get-status-of-an-operation) určete, zda byla aktualizace úspěšná.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Ujistěte se, [`wait_for_operation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně aktualizovat znalostní báze.

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [publikování.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) To trvá aktuální uložený a trénovaný model, na který odkazuje ID znalostní báze a publikuje jej v koncovém bodě. Zkontrolujte kód odpovědi HTTP, chcete-li ověřit publikování proběhlo úspěšně.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Získání publikovaného koncového bodu

Po publikování znalostní báze přístup k publikované znalostní bázi prostřednictvím rozhraní generateAnswer API prognózy dotazu. Chcete-li to provést, potřebujete klíč koncového bodu runtime. To se liší od autorské klíče.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Z volání jsou vráceny dva klíče koncového bodu. Pouze jeden je nutný pro přístup ke koncovému bodu runtime.

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní báze pomocí metody [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) s parametrem ID znalostní báze.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Získání stavu operace

Některé metody, například vytvořit a aktualizovat, může trvat tolik času, že namísto čekání na dokončení procesu je vrácena [operace.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) Pomocí [ID operace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) z operace k dotazování (s logikou opakování) určete stav původní metody.

_DelayTimer_ volání v následujícím bloku kódu se používá k simulaci logiky opakování. Nahraďte ji vlastní logikou opakování.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte `node index.js` aplikaci s příkazem z adresáře aplikace.

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

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)