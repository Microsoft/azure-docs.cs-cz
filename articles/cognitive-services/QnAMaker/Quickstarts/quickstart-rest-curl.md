---
title: 'Rychlý Start: použití &u REST pro správu znalostní báze Knowledge Base – QnA Maker'
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní REST API vytvořit, publikovat a dotazovat znalostní bázi.
ms.date: 04/13/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: facc45ab8f916181f7eeceb65c5102a60ae7d7e9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "81261699"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Rychlý Start: použití kudrlinkou a REST ke správě znalostní báze

Tento rychlý Start vás provede vytvořením, publikováním a dotazem znalostní báze. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Předpoklady

* Aktuální verze sady [kudrlinkou](https://curl.haxx.se/). V rychlých startech se používá několik přepínačů příkazového řádku, které jsou uvedeny v [dokumentaci k kudrlinkou](https://curl.haxx.se/docs/manpage.html).
* Abyste mohli použít klíč a název prostředku, musíte mít [QnA maker Resource](../How-To/set-up-qnamaker-service-azure.md). Při vytváření prostředku jste zadali **název** prostředku, klíč byl proto vytvořen za vás. Název prostředku se používá jako subdoména pro koncový bod. Pokud chcete načíst svůj klíč a název prostředku, vyberte pro svůj prostředek v Azure Portal **rychlý Start** . Název prostředku je první subdoménou adresy URL koncového bodu:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Následující příklady BASH používají znak pro `\` pokračování řádku. Pokud konzola nebo terminál používá jiný znak pro pokračování řádku, použijte tento znak.

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

Chcete-li vytvořit znalostní bázi rozhraní REST API a oblé, je nutné mít následující informace:

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|Klíč prostředku QnA Maker|`-h` param pro `Ocp-Apim-Subscription-Key` záhlaví|Ověřování pro QnA Maker službu|
|JSON popisující znalostní bázi Knowledge Base|`-d` bajty|[Příklady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) formátu JSON|
|Velikost JSON v bajtech|`-h` param pro `Content-Size` záhlaví||

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte pomocí vlastního názvu prostředku, klíče prostředku a hodnot JSON a velikosti JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Odpověď složené z QnA Maker zahrnuje `operationId` , který je požadován k [získání stavu operace](#get-status-of-operation).

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

Když vytvoříte znalostní bázi, protože operace je asynchronní, odpověď obsahuje informace o tom, jak určit stav.

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|ID operace|Trasa URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Klíč prostředku QnA Maker|`-h` param pro `Ocp-Apim-Subscription-Key` záhlaví|Ověřování pro QnA Maker službu|

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte s vlastním názvem prostředku, klíčem prostředku a ID operace.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Odpověď složeného pole zahrnuje stav. Pokud je stav operace úspěšný, pak `resourceLocation` zahrnuje ID znalostní báze.

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

Před dotazem znalostní báze musíte:
* Publikování znalostní báze
* Získat klíč koncového bodu modulu runtime

Tato úloha publikuje znalostní bázi. Získání klíče běhového koncového bodu je [samostatný úkol](#get-published-knowledge-bases-runtime-endpoint-key).

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|Klíč prostředku QnA Maker|`-h` param pro `Ocp-Apim-Subscription-Key` záhlaví|Ověřování pro QnA Maker službu|
|ID znalostní báze|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte s vlastním názvem prostředku, klíčem prostředku a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Stav odpovědi je 204 bez výsledků. Použijte `-v` parametr příkazového řádku pro zobrazení podrobného výstupu příkazu složeného. To bude zahrnovat stav HTTP.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Získat klíč koncového bodu běhové služby znalostní báze

Před dotazem znalostní báze musíte:
* Publikování znalostní báze
* Získat klíč koncového bodu modulu runtime

Tato úloha Získá klíč koncového bodu modulu runtime. Publikování znalostní báze je [samostatná úloha](#publish-knowledge-base).

Klíč běhového koncového bodu je stejný klíč pro všechny znalostní báze pomocí prostředku QnA Maker.

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|Klíč prostředku QnA Maker|`-h` param pro `Ocp-Apim-Subscription-Key` záhlaví|Ověřování pro QnA Maker službu|

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte s vlastním názvem prostředku a klíčem prostředku.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Odezva na kudrlinkou zahrnuje klíče koncového bodu modulu runtime. Při dotazování na získání odpovědi ze znalostní báze použijte pouze jeden z těchto klíčů.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Dotaz na odpověď z publikované znalostní báze

Získání odpovědi od znalostní báze se provádí od samostatného modulu runtime, než správy znalostní báze. Vzhledem k tomu, že se jedná o samostatný modul runtime, je nutné ověřit klíč za běhu.

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|QnA Maker klíč modulu runtime|`-h` param pro `Authorization` záhlaví|Klíč je součástí řetězce, který obsahuje slovo `Endpointkey ` . Ověřování pro QnA Maker službu|
|ID znalostní báze|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|Popis dotazu JSON|`-d` bajty|[Parametry těla požadavku](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) a [Příklady](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) JSON|
|Velikost JSON v bajtech|`-h` param pro `Content-Size` záhlaví||

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte s vlastním názvem prostředku, klíčem prostředku a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Úspěšná odpověď zahrnuje horní odpověď spolu s dalšími informacemi, které klientská aplikace, jako je například robotka chatu, musí zobrazit odpověď uživateli.

## <a name="delete-knowledge-base"></a>Odstranit znalostní bázi

Až se znalostní báze dokončí, odstraňte ji.

|Informace|Konfigurace kudrlinkou|Účel|
|--|--|--|
|Název prostředku QnA Maker|URL|slouží k vytvoření adresy URL.|
|Klíč prostředku QnA Maker|`-h` param pro `Ocp-Apim-Subscription-Key` záhlaví|Ověřování pro QnA Maker službu|
|ID znalostní báze|Trasa URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Příkaz kudrlinkou se spustí z prostředí BASH. Tento příkaz upravte s vlastním názvem prostředku, klíčem prostředku a ID znalostní báze.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Stav odpovědi je 204 bez výsledků. Použijte `-v` parametr příkazového řádku pro zobrazení podrobného výstupu příkazu složeného. To bude zahrnovat stav HTTP.

## <a name="additional-resources"></a>Další zdroje informací

* [Vytváření obsahu](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referenční dokumentace
* [Modul runtime](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referenční dokumentace
* [Ukázkové skripty BASH využívající kudrlinkou](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
