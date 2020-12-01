---
title: 'Rychlý start: Vytvoření znalostní báze – REST, Python – QnA Maker'
description: Tento rychlý start založený na REST a Pythonu vás provede procesem vytvoření ukázkové znalostní báze služby QnA Maker prostřednictvím kódu programu. Tato znalostní báze se zobrazí na řídicím panelu Azure účtu rozhraní API služeb Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 9f3f433742ec25a1ee1abb2ede32a38e6b611f14
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352282"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Rychlý start: Vytvoření znalostní báze ve službě QnA Maker pomocí Pythonu

Tento rychlý start vás provede vytvořením a publikováním ukázkové znalostní báze služby QnA Maker pomocí kódu programu. Služba QnA Maker automaticky extrahuje otázky a odpovědi z částečně strukturovaného obsahu, jako jsou třeba časté otázky, ze [zdrojů dat](../index.yml). Model pro znalostní bázi je definovaný v kódu ve formátu JSON poslaném v těle požadavku rozhraní API.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Create KB](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Získat podrobnosti operace](/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Referenční dokumentace](/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Ukázka Pythonu](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Python 3.7](https://www.python.org/downloads/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .

## <a name="create-a-knowledge-base-python-file"></a>Vytvoření souboru Pythonu pro znalostní bázi

Vytvořte soubor s názvem `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `create-new-knowledge-base-3x.py` přidejte následující řádky k přidání potřebných závislostí do projektu:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant
Za předcházející požadované závislosti přidejte požadované konstanty pro přístup ke službě QnA Maker. Nahraďte hodnotu `<your-qna-maker-subscription-key>` a `<your-resource-name>` vlastním QnA maker klíč a názvem prostředku.

V horní části třídy program přidejte požadované konstanty pro přístup k QnA Maker.

Nastavte následující hodnoty:

* `<your-qna-maker-subscription-key>` - **Klíč** je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA maker na stránce rychlý Start. To není totéž jako klíč koncového bodu předpovědi.
* `<your-resource-name>` – **Název prostředku** se používá k vytvoření adresy URL koncového bodu pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>Přidejte definici modelu znalostní báze

Za konstanty přidejte následující definici modelu znalostní báze. Model po definici provádí převod na řetězec.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>Přidání podpůrné funkce

Přidejte následující funkci na tisk JSON v čitelném formátu:

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>Přidání funkce pro vytvoření znalostní báze

Přidejte následující funkci pro vytvoření požadavku HTTP POST na vytvoření znalostní báze.
Toto volání rozhraní API vrátí odpověď JSON, která v poli hlavičky **Location** obsahuje ID operace. Toto ID operace použijte ke zjištění toho, jestli se znalostí báze úspěšně vytvořila. `Ocp-Apim-Subscription-Key` je klíč služby QnA Maker používaný k ověřování.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

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

## <a name="add-function-to-check-creation-status"></a>Přidání funkce pro kontrolu stavu vytváření

Následující funkce přebírá adresu URL, která má na konci ID operace, a kontroluje stav vytváření. Volání funkce `check_status` je uvnitř hlavní smyčky _while_.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

Toto volání rozhraní API vrátí odpověď ve formátu JSON, která obsahuje i stav operace:

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-main-code-block"></a>Přidání hlavního bloku kódu
Následující smyčka se pravidelně dotazuje na stav operace vytváření, dokud se operace nedokončí.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Program pošle požadavek na vytvoření znalostní báze do rozhraní API služby QnA Maker a pak se bude dotazovat na výsledky každých 30 sekund. Každá odpověď se zobrazí v okně konzoly.

```bash
python create-new-knowledge-base-3x.py
```

Jakmile se znalostní báze vytvoří, můžete se na ni podívat na portálu služby QnA Maker na stránce [vašich znalostních bází](https://www.qnamaker.ai/Home/MyServices). Vyberte název vaší znalostní báze, například QnA Maker FAQ, a zobrazte ji.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)