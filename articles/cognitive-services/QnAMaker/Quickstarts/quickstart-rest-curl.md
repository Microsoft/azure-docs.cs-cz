---
title: 'Úvodní příručka: Ke správě znalostní báze cURL & REST použijte – QnA Maker'
description: Tento rychlý start ukazuje, jak vytvořit, publikovat a dotazovat znalostní báze pomocí rest API.
ms.date: 02/27/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 00ec52fe20fb0e6a976f3e7142386e835713c98c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78851201"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Úvodní příručka: Správa znalostní báze cURL a REST pomocí cURL a REST

Tento rychlý začátek vás provede vytvářením, publikováním a dotazováním znalostní báze. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Aktuální verze [cURL](https://curl.haxx.se/). V rychlých startech se používá několik přepínačů příkazového řádku, které jsou uvedeny v [dokumentaci cURL](https://curl.haxx.se/docs/manpage.html).
* Musíte mít [prostředek QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst název klíče a prostředku, vyberte **rychlý start** pro svůj prostředek na webu Azure Portal. Název prostředku je první část adresy URL koncového bodu:

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Následující příklady BASH `\` používají znak pokračování řádku. Pokud konzolová nebo terminál používá jiný znak pokračování řádku, použijte tento znak.

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Chcete-li vytvořit znalostní bázi s rest API a cURL, musíte mít následující informace:

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Klíč prostředku QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` pro záhlaví|Ověření služby QnA Maker|
|JSON popisující znalostní bázi|`-d`Param|[Příklady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) JSON|
|Velikost JSON v bajtů|`-h`param `Content-Size` pro záhlaví||

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz s vlastním názvem prostředku, klíčem prostředku a hodnotami JSON a velikostí JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Odpověď cURL od QnA `operationId` Maker obsahuje , který je nutný pro [získání stavu operace](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Získat stav operace

Při vytváření znalostní báze, protože operace je asynchronní, odpověď obsahuje informace k určení stavu.

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Id operace|Trasa adresy URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Klíč prostředku QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` pro záhlaví|Ověření služby QnA Maker|

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz pomocí vlastního názvu prostředku, klíče prostředku a ID operace.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Odpověď cURL zahrnuje stav. Pokud je stav operace úspěšný, `resourceLocation` zahrnuje ID znalostní báze.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publikování znalostní báze

Před dotazem na znalostní bázi je třeba:
* Publikování znalostní báze
* Získání klíče koncového bodu runtime

Tento úkol publikuje znalostní bázi. Získání klíče koncového bodu runtime je [samostatná úloha](#get-published-knowledge-bases-runtime-endpoint-key).

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Klíč prostředku QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` pro záhlaví|Ověření služby QnA Maker|
|Id znalostní báze|Trasa adresy URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz pomocí vlastního názvu prostředku, klíče prostředků a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Stav odpovědi je 204 bez výsledků. Pomocí `-v` parametru příkazového řádku zobrazíte podrobný výstup příkazu cURL. To bude zahrnovat stav HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Získání publikovaného klíče koncového bodu runpoint znalostní báze Knowledge Base

Před dotazem na znalostní bázi je třeba:
* Publikování znalostní báze
* Získání klíče koncového bodu runtime

Tato úloha získá klíč koncového bodu za běhu. Publikování znalostní báze je [samostatný úkol](#publish-knowledge-base).

Klíč koncového bodu runtime je stejný klíč pro všechny znalostní báze pomocí prostředku QnA Maker.

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Klíč prostředku QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` pro záhlaví|Ověření služby QnA Maker|

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz pomocí vlastního názvu prostředku, klíče prostředku.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Odpověď cURL obsahuje klíče koncového bodu runtime. Při dotazování použijte pouze jeden z klíčů, abyste získali odpověď ze znalostní báze.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Dotaz na odpověď z publikované znalostní báze

Získání odpovědi ze znalostí se provádí ze samostatného běhu než správa znalostní báze. Vzhledem k tomu, že se jedná o samostatný běhový čas, je třeba ověřit pomocí klíče runtime.

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Klíč runtime QnA Maker|`-h`param `Authorization` pro záhlaví|Klíč je součástí řetězce, který `Endpointkey `obsahuje slovo . Ověření služby QnA Maker|
|Id znalostní báze|Trasa adresy URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON popisující dotaz|`-d`Param|[Parametry těla požadavku](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) a [příklady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) JSON|
|Velikost JSON v bajtů|`-h`param `Content-Size` pro záhlaví||

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz pomocí vlastního názvu prostředku, klíče prostředků a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Úspěšná odpověď obsahuje nejvyšší odpověď spolu s dalšími informacemi, které klientská aplikace, jako je například chatovací robot, potřebuje zobrazit odpověď uživateli.

## <a name="delete-knowledge-base"></a>Odstranění znalostní báze

Až budete hotovi se znalostí, odstraňte ji.

|Informace|konfigurace cURL|Účel|
|--|--|--|
|Název prostředku qnA makeru|zprostředkovatele identity|slouží k vytvoření adresy URL|
|Klíč prostředku QnA Maker|`-h`param `Ocp-Apim-Subscription-Key` pro záhlaví|Ověření služby QnA Maker|
|Id znalostní báze|Trasa adresy URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Příkaz cURL je proveden z prostředí BASH. Upravte tento příkaz pomocí vlastního názvu prostředku, klíče prostředků a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Stav odpovědi je 204 bez výsledků. Pomocí `-v` parametru příkazového řádku zobrazíte podrobný výstup příkazu cURL. To bude zahrnovat stav HTTP.

## <a name="additional-resources"></a>Další zdroje

* [Vytváření](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referenční dokumentace
* [Doba běhu](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referenční dokumentace
* [Ukázkové skripty BASH pomocí cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
