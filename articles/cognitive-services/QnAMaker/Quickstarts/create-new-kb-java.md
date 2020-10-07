---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Java – QnA Maker'
description: Tento rychlý Start založený na REST Java vás provede vytvořením ukázkové QnA Maker znalostní bázi prostřednictvím kódu programu, který se zobrazí na řídicím panelu Azure vašeho účtu Cognitive Services API.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 275b1976e51789d4a458e7bd9f7cfa3240cc81d5
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777666"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý start vás provede vytvořením ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../Concepts/knowledge-base.md). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Získat podrobnosti operace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Ukázka Java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Předpoklady

* [Go 1.10.1](https://golang.org/dl/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

[Vzorový kód](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java) je k dispozici v úložišti GitHub pro QnA maker v jazyce Java.

## <a name="create-a-knowledge-base-file"></a>Vytvoření souboru pro znalostní bázi

Vytvořte soubor s názvem `CreateKB.java`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `CreateKB.java` přidejte následující řádky k přidání potřebných závislostí do projektu:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant
Za předcházející požadované závislosti přidejte do třídy `CreateKB` požadované konstanty pro přístup ke službě QnA Maker.

Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a název prostředku, vyberte v Azure Portal pro prostředek QnA Maker možnost **rychlý Start** .

Nastavte následující hodnoty:

* `<your-qna-maker-subscription-key>` - **Klíč** je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA maker na stránce rychlý Start. Tento klíč není stejný jako klíč koncového bodu předpovědi.
* `<your-resource-name>` – **Název prostředku** se používá k vytvoření adresy URL koncového bodu pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Tento název prostředku není stejný jako ten, který se používá k dotazování koncového bodu předpovědi.

Na konec třídy není potřeba přidávat ukončovací složenou závorku, ta je v konečném fragmentu kódu na konci tohoto rychlého startu.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>Přidání tříd definice modelu znalostní báze
Za konstanty přidejte do třídy `CreateKB` následující třídy a funkce pro serializaci objektu definice modelu do formátu JSON.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>Přidejte podpůrné funkce

Dále do třídy `CreateKB` přidejte následující podpůrné funkce.

1. Přidejte následující funkci na tisk JSON v čitelném formátu:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. Přidejte následující třídu pro správu odpovědi HTTP:

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. Přidejte následující metodu pro vytvoření požadavku POST na rozhraní API služby QnA Maker. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. Přidejte následující metodu pro vytvoření požadavku GET na rozhraní API služby QnA Maker.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>Přidání metody pro vytvoření znalostní báze
Přidejte následující metodu pro vytvoření znalostní báze zavoláním metody Post.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>Přidání metody pro získání stavu
Přidejte následující metodu pro kontrolu stavu vytváření.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

Volání opakujte, dokud neskočí úspěchem nebo neúspěchem:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>Přidání metody main
Metoda main vytvoří znalostní bázi a pak se dotazuje na stav. ID operace se vrátí v **umístění**pole HLAVIČKY odpovědi post a pak se použije jako součást trasy v žádosti o získání. `while`Smyčka opakuje stav, pokud není dokončen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>Kompilace a spuštění programu

1. Ujistěte se, že adresář `./libs` obsahuje knihovnu gson. Na příkazovém řádku zkompilujte soubor `CreateKB.java` :

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. Na příkazovém řádku zadejte následující příkaz pro spuštění programu. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
