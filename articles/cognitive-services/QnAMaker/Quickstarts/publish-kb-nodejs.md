---
title: 'Úvodní příručka: QnA Maker s rest API pro Node.js'
description: Tento rychlý start ukazuje, jak začít s QnA Maker REST API pro Node.js. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851671"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Úvodní příručka: QnA Maker REST API pro Node.js

Začínáme s rened.js makeru QnA Maker. Chcete-li vyzkoušet ukázkový kód pro základní úkoly, postupujte takto.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.

Pomocí api REST qnA makeru pro soubor Node.js můžete:

* Vytvoření znalostní báze
* Nahrazení znalostní báze
* Publikování znalostní báze
* Odstranění znalostní báze
* Stažení znalostní báze
* Získání stavu operace

[Ukázky referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [souboru Node.js](https://nodejs.org).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure pro QnA Maker

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro QnA Maker pomocí [portálu Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači.

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `QNAMAKER_RESOURCE_KEY` pro `QNAMAKER_AUTHORING_ENDPOINT`prostředek s názvem a . Použijte hodnoty klíče a koncového bodu, které se nacházejí na stránce **Rychléspuštění** prostředku na webu Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spusťte `npm init -y` příkaz a `package.json` vytvořte soubor uzlu.

```console
npm init -y
```

Přidejte `reqeuestretry` `request` balíčky a balíčky NPM:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí řešení REST API QnA Maker pro soubor Node.js:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Nahrazení znalostní báze](#replace-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Stažení znalostní báze](#download-the-knowledge-base)
* [Získání stavu operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidání závislostí

Vytvořte soubor `rest-apis.js` s názvem a přidejte následující _příkaz vyžaduje,_ aby požadavky HTTP.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Přidání informací o prostředcích Azure

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

Nastavte následující hodnoty prostředí:

* `QNAMAKER_RESOURCE_KEY`- **Klíč** je řetězec 32 znaků a je k dispozici na portálu Azure, na prostředek QnA Maker, na stránce **rychlý start.** To není stejné jako klíč koncového bodu předpověď.
* `QNAMAKER_AUTHORING_ENDPOINT`- Váš koncový bod pro vytváření `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`ve formátu , obsahuje **název zdroje**. Toto není stejná adresa URL, která se používá k dotazování koncového bodu předpověď.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá dvojice otázek a odpovědí vytvořené z objektu JSON:

* **Redakční obsah**.
* **Soubory** - místní soubory, které nevyžadují žádná oprávnění.
* **Adresy URL** – veřejně dostupné adresy URL.

Pomocí [rozhraní REST API vytvořte znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Nahrazení znalostní báze

Pomocí [rozhraní REST API nahraďte znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi. Tento proces zpřístupní znalostní bázi z koncového bodu předpovědi dotazu HTTP.

Pomocí [rozhraní REST API publikujte znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>Stažení znalostní báze

Ke [stažení znalostní báze](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)použijte rozhraní REST API .

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Po dokončení používání znalostní báze ji odstraňte.

Pomocí [rozhraní REST API odstraňte znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Získání stavu operace

Dlouho běžící procesy, jako je například proces vytváření vrátí ID operace, které je třeba zkontrolovat pomocí samostatného volání rozhraní REST API. Tato funkce přebírá tělo vytvořit odpověď. Důležitým klíčem je `operationState`, který určuje, zda je třeba pokračovat dotazování.

Pomocí [rozhraní REST API můžete sledovat operace ve znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Spuštění aplikace

Spusťte `node rest-apis.js` aplikaci s příkazem z adresáře aplikace.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Kurz: Vytvoření a odpověď KB](../tutorials/create-publish-query-in-portal.md)

* [Co je rozhraní API qnA makeru?](../Overview/overview.md)
* [Úprava znalostní báze](../how-to/edit-knowledge-base.md)
* [Získejte analýzy využití](../how-to/get-analytics-knowledge-base.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).