---
title: 'Rychlý Start: QnA Maker s rozhraním REST API pro Node.js'
description: V tomto rychlém startu se dozvíte, jak začít s rozhraními REST API QnA Maker pro Node.js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 67c4711d74bdeb970a706eabf795e8f9bdcbbded
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324599"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Rychlý Start: QnA Maker rozhraní REST API pro Node.js

Začínáme s rozhraními REST API QnA Maker pro Node.js. Pomocí těchto kroků můžete vyzkoušet ukázkový kód pro základní úlohy.  QnA Maker umožňuje provozovat službu otázek a odpovědí na základě částečně strukturovaného obsahu, jako jsou dokumenty s nejčastějšími dotazy, adresy URL a příručky k produktům.

Pro Node.js použijte QnA Maker rozhraní REST API:

* Vytvoření znalostní báze
* Výměna znalostní báze
* Publikování znalostní báze
* Odstranění znalostní báze
* Stáhnout znalostní bázi
* Získat stav operace

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [UkázkyNode.js](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org).
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure QnA Maker

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro QnA Maker pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači.

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro prostředek s názvem `QNAMAKER_RESOURCE_KEY` a `QNAMAKER_AUTHORING_ENDPOINT` . Použijte hodnoty klíč a koncový bod, které najdete na stránce **rychlý Start** prostředku v Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init -y` příkazu vytvořte `package.json` soubor uzlu.

```console
npm init -y
```

Přidejte `reqeuestretry` balíčky a `request` npm:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující s QnA Maker rozhraní REST API pro Node.js:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Výměna znalostní báze](#replace-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Stáhnout znalostní bázi](#download-the-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Přidat závislosti

Vytvořte soubor s názvem `rest-apis.js` a přidejte následující závislosti.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Přidat funkce nástrojů

Přidejte následující funkce nástroje.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Přidat informace o prostředcích Azure

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

Nastavte následující hodnoty prostředí:

* `QNAMAKER_RESOURCE_KEY` - **Klíč** je řetězec znaků 32 a je k dispozici v Azure Portal na prostředku QnA maker na stránce **rychlý Start** . To není totéž jako klíč koncového bodu předpovědi.
* `QNAMAKER_AUTHORING_ENDPOINT` – Váš koncový bod pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` obsahuje název vašeho **prostředku**. Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Znalostní báze ukládá páry dotazů a odpovědí, které byly vytvořeny z objektu JSON:

* **Redakční obsah**.
* **Soubory** – místní soubory, které nevyžadují žádná oprávnění.
* **Adresy URL** – veřejně dostupné adresy URL

[K vytvoření znalostní báze použijte REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Výměna znalostní báze

[K nahrazení znalostní báze použijte REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi. Díky tomuto procesu je znalostní báze dostupný z koncového bodu předpovědi dotazu HTTP.

[K publikování znalostní báze použijte REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>Stáhnout znalostní bázi Knowledge Base

Ke [stažení znalostní báze použijte REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Po dokončení používání znalostní báze ho odstraňte.

[K odstranění znalostní báze použijte REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Získat stav operace

Dlouhotrvající procesy, jako je například proces vytváření, vrací ID operace, které je nutné zkontrolovat pomocí samostatného REST API volání. Tato funkce přijímá tělo odpovědi Create. Důležitý klíč je `operationState` , který určuje, jestli je potřeba pokračovat v dotazování.

Pomocí [REST API můžete monitorovat operace ve znalostní bázi](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Přidat metodu Main

Přidejte následující `main` metodu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node rest-apis.js` příkazu z adresáře aplikace.

```console
node rest-apis.js
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
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js).
