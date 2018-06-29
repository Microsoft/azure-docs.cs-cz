---
title: Kurz přidávání promluv do aplikace LUIS pomocí Node.js | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264222"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>Kurz: Přidávání promluv do aplikace pomocí Node.js 
V tomto kurzu napíšete program, který do záměru přidá promluvu pomocí rozhraní API pro vytváření v Node.js.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření projektu konzoly v sadě Visual Studio 
> * Přidání metody pro volání rozhraní API služby LUIS za účelem přidání promluvy a trénování aplikace
> * Přidání souboru JSON s ukázkovými promluvami pro záměr BookFlight (Rezervovat let)
> * Spuštění konzoly a zobrazení stavu trénování pro promluvy

Další informace najdete v technické dokumentaci k rozhraním API pro [přidání ukázkové promluvy do záměru](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a [stav trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [**Node.js**](https://nodejs.org/en/download/) s NPM.
* Závislosti NPM pro tento článek: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* **[Doporučeno]** [Visual Studio Code](https://code.visualstudio.com/) pro IntelliSense a ladění.
* **[Klíč pro vytváření](luis-concept-keys.md#authoring-key)** pro službu LUIS. Tento klíč najdete v části Account Settings (Nastavení účtu) na webu [LUIS](luis-reference-regions.md).
* [**ID stávající aplikace**](./luis-get-started-create-app.md) LUIS. ID aplikace je uvedené na řídicím panelu aplikace. Před spuštěním kódu v souboru `add-utterances.js` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytvoří záměry ani entity. Pouze přidá promluvy pro existující záměry a entity. 
* **ID verze** aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* Ve VSCode vytvořte nový soubor `add-utterances.js`.

> [!NOTE] 
> Kompletní soubor `add-utterances.js` je dostupný v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs).


## <a name="write-the-nodejs-code"></a>Psaní kódu Node.js

Přidejte do souboru závislosti NPM.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

Přidejte do souboru konstanty LUIS. Zkopírujte následující kód a použijte vlastní klíč pro vytváření, ID aplikace a ID verze.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

Přidejte název a umístění nahraného souboru obsahujícího promluvy. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

Přidejte proměnné s hodnotami příkazového řádku.

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


Přidejte funkci `sendUtteranceToApi` pro odesílání a příjem volání HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

Přidejte objekt JSON konfigurace, který používá funkce `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

Přidejte funkci `addUtterance` pro správu požadavku a odpovědi rozhraní API, kterou používá funkce `SendUtteranceToApp`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

Přidejte objekt JSON konfigurace, který používá funkce `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

Přidejte funkci `train` pro zahájení procesu trénování. 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

Přidejte kód, který na základě proměnných příkazového řádku zvolí, jaká akce se má provést (přidání promluvy nebo trénování).

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>Určení promluv, které se mají přidat
Vytvořte soubor `utterances.json` a upravte ho tím, že určíte **pole promluv**, které chcete přidat do aplikace LUIS. Záměr a entity již **musí** být v aplikaci LUIS.

> [!NOTE]
> Před spuštěním kódu v souboru `add-utterances.js` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytvoří záměry ani entity. Pouze přidá promluvy pro existující záměry a entity.

Pole `text` obsahuje text promluvy. Pole `intentName` musí odpovídat názvu záměru v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete označovat žádné entity, zadejte prázdný seznam, jak je znázorněno v následujícím příkladu.

Pokud seznam entityLabels není prázdný, hodnoty `startCharIndex` a `endCharIndex` musí označovat entitu, na kterou odkazuje pole `entityName`. Oba indexy se počítají od nuly, což znamená, že číslo 6 v horním příkladu označuje písmeno S ve slově Seattle, a ne mezeru před velkým S.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Přidání promluvy z příkazového řádku

Spusťte aplikaci z příkazového řádku s Node.js.

Zavoláním add-utterance bez argumentů se do aplikace přidá promluva, ale nepoužije se k trénování.
````
> node add-utterances.js
````

Tato ukázka vytvoří soubor `results.json`, který bude obsahovat výsledky volání rozhraní API pro přidávání promluv. Pole `response` obsahuje promluvy, které se přidaly, v následujícím formátu. Hodnota `hasError` je false, což značí, že se promluva přidala.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Přidání promluvy a trénování z příkazového řádku
Zavoláním add-utterance s argumentem `-train` odešlete požadavek na trénování a pak si vyžádejte stav trénování. Stav se zařadí do fronty okamžitě po zahájení trénování. Podrobnosti o stavu se zapíší do souboru.

````
> node add-utterances.js -train
````

> [!NOTE]
> Duplicitní promluvy se nepřidají znovu, ale ani nezpůsobí chybu. Pole `response` bude obsahovat ID původní promluvy.

Když zavoláte ukázku s argumentem `-train`, vytvoří se soubor `training-results.json`, který značí úspěšné zařazení požadavku na trénování aplikace LUIS do fronty. 

Následující příklad ukazuje výsledek úspěšného požadavku na trénování:
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

Po zařazení požadavku na trénování do fronty může dokončení trénování chvíli trvat.

## <a name="get-training-status-from-the-command-line"></a>Získání stavu trénování z příkazového řádku
Zavoláním ukázky s argumentem `-status` zkontrolujte stav trénování a zapište podrobnosti o stavu do souboru.

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s kurzem hotovi, odeberte sadu Visual Studio a konzolovou aplikaci, pokud už je nepotřebujete. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
