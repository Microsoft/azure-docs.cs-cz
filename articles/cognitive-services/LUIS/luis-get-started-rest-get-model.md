---
title: 'Rychlý Start: získání modelu pomocí rozhraní REST API – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu REST API k přidání projevy a výukového modelu použijte kudrlinkou.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: e5cf3160e6592a48e3a81480480ad8f01cc6af3a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "86206260"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Rychlý Start: použití kudrlinkou a REST ke změně modelu

V tomto rychlém startu se dozvíte, jak přidat příklad projevy do aplikace Pizza a vytvořit výuku aplikace. Ukázkové promluvy jsou konverzačním textem uživatele namapovaným na záměr. Tím, že poskytnete ukázkové promluvy pro záměry, naučíte službu LUIS, které typy uživatelem zadaného textu patří do kterého záměru.

Podle tohoto rychlého startu budete provádět tři volání REST v pořadí.

- Nejdřív nahrajete dávku ukázkového projevy, který se bude používat ke školení modelu aplikace Pizza, pomocí volání metody [Add Labels v dávce](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) .
- V dalším kroku zahájíte výukovou relaci pro aplikaci Pizza, a to pomocí volání rozhraní [REST pro použití verze](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) .
- Nakonec obdržíte stav výukové relace pro aplikaci Pizza s použitím volání [stavu služby Get Version Train](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) .

[Referenční dokumentace](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Předpoklady

* Bezplatný účet [Luis](https://www.luis.ai) .
* Textový editor, jako je například [Visual Studio Code](https://code.visualstudio.com/).
* Program příkazového řádku kudrlinkou. Oblý program je už nainstalovaný v macOS, většinu distribucí pro Linux a Windows 10 Build 1803 a novější.

    Je-li nutné nainstalovat soubor kudrlinkou, můžete ze [stránky pro stažení ze složené](https://curl.haxx.se/download.html)aplikace Stáhnout soubor.

## <a name="create-pizza-app"></a>Vytvoření aplikace Pizza

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Vytvoření souboru JSON pro výuku aplikace Pizza

Chcete-li vytvořit soubor JSON se třemi vzorovými projevy, uložte následující data JSON do souboru s názvem `ExampleUtterances.JSON` :

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Vzorový projevy JSON sleduje konkrétní formát.

Pole `text` obsahuje text ukázkové promluvy. Pole `intentName` musí odpovídat názvu existujícího záměru v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete označovat žádné entity, zadejte prázdné pole.

Není-li pole entityLabels prázdné, hodnoty `startCharIndex` a `endCharIndex` musí označovat entitu, na kterou odkazuje pole `entityName`. Index je založený na nule. Pokud je popisek začínat nebo končit v prostoru v textu, volání rozhraní API pro přidání projevy se nezdařilo.

## <a name="add-example-utterances"></a>Přidat ukázkové promluvy

1. Pokud chcete nahrát dávku ukázkového projevy, zkopírujte tento příkaz do textového editoru:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Nahraďte hodnoty začínající `***YOUR-` vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| Váš koncový bod adresy URL pro vytváření Například https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/. Název prostředku se nastaví při vytváření prostředku.|
    |`***YOUR-APP-ID***`| Vaše ID aplikace LUIS |
    |`***YOUR-APP-VERSION***`| Verze aplikace LUIS V případě aplikace Pizza je číslo verze "0,1" bez uvozovek.|
    |`***YOUR-AUTHORING-KEY***`|Klíč pro vytváření znaků 32.|

    Přiřazené klíče a prostředky jsou zobrazené na portálu LUIS v části Správa na stránce **prostředky Azure** . ID aplikace je k dispozici ve stejné části pro správu na stránce **nastavení aplikace** .

1. Spusťte příkazový řádek (Windows) nebo terminál (macOS a Linux) a změňte adresáře do stejného adresáře, kam jste `ExampleUtterances.JSON` soubor uložili.

1. Z editoru zkopírujte příkaz kudrlinkou a vložte ho do příkazového řádku (Windows) nebo terminálu (macOS a Linux). Stiskněte klávesu ENTER a spusťte příkaz.

    Měla by se zobrazit následující odpověď:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Tady je výstup formátovaný pro čitelnost:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Výuka modelu aplikace Pizza

1. Pokud chcete zahájit výukovou relaci pro aplikaci Pizza, zkopírujte tento příkaz do textového editoru:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Stejně jako dříve nahraďte hodnoty začínající `***YOUR-` vlastními hodnotami.

1. Z editoru zkopírujte příkaz kudrlinkou a vložte ho do příkazového řádku (Windows) nebo terminálu (macOS a Linux). Stiskněte klávesu ENTER a spusťte příkaz.

    Měla by se zobrazit následující odpověď:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Tady je výstup formátovaný pro čitelnost:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Získání stavu školení

1. Chcete-li získat stav školení pro cvičení cvičení, zkopírujte tento příkaz do textového editoru:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Stejně jako dříve nahraďte hodnoty začínající `***YOUR-` vlastními hodnotami.

1. Z editoru zkopírujte příkaz kudrlinkou a vložte ho do příkazového řádku (Windows) nebo terminálu (macOS a Linux). Stiskněte klávesu ENTER a spusťte příkaz.

    Měla by se zobrazit následující odpověď:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Tady je výstup formátovaný pro čitelnost:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte `ExampleUtterances.JSON` soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](luis-concept-best-practices.md)
