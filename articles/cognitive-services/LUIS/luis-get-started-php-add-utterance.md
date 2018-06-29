---
title: Kurz přidávání promluv do aplikace LUIS pomocí PHP | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí PHP.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 59150b7ed6782c28f243041be2ed6aa17e69cc01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263776"
---
# <a name="tutorial-add-utterances-to-app-using-php"></a>Kurz: Přidávání promluv do aplikace pomocí PHP 
V tomto kurzu napíšete program, který do záměru přidá promluvu pomocí rozhraní API pro vytváření v PHP.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření projektu konzoly v sadě Visual Studio 
> * Přidání metody pro volání rozhraní API služby LUIS za účelem přidání promluvy a trénování aplikace
> * Přidání souboru JSON s ukázkovými promluvami pro záměr BookFlight (Rezervovat let)
> * Spuštění konzoly a zobrazení stavu trénování pro promluvy

Další informace najdete v technické dokumentaci k rozhraním API pro [přidání ukázkové promluvy do záměru](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a [stav trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [**PHP**](http://php.net/).
* Ujistěte se, že je k dispozici openssl jako závislost pro PHP.  
* **[Klíč pro vytváření](luis-concept-keys.md#authoring-key)** pro službu LUIS. Tento klíč najdete v části Account Settings (Nastavení účtu) na webu [LUIS](luis-reference-regions.md).
* [**ID stávající aplikace**](./luis-get-started-create-app.md) LUIS. ID aplikace je uvedené na řídicím panelu aplikace. Před spuštěním kódu v souboru `add-utterances.php` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytváří záměry ani entity. Pouze přidává promluvy pro existující záměry a entity. 
* **ID verze** aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* Ve VSCode vytvořte nový soubor `add-utterances.php`.

> [!NOTE] 
> Kompletní soubor `add-utterances.cs` a ukázkový soubor `utterances.json` jsou dostupné v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/php/).


## <a name="write-the-php-code"></a>Psaní kódu PHP

Přidejte do souboru závislosti.

   [!code-php[PHP and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=10-29 "PHP and LUIS Dependencies")]

Přidejte požadavek GET sloužící k získání stavu trénování.

   [!code-php[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=31-50 "SendGet")]

Přidejte požadavek POST sloužící k vytváření promluv nebo zahájení trénování. 

   [!code-php[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=52-72 "SendPost")]

Přidejte funkci `AddUtterances`.

   [!code-php[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=74-79 "AddUtterances method")]


Přidejte funkci `Train`. 

   [!code-php[Train](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=81-88 "Train")]

Přidejte funkci `Status`.

   [!code-php[Status](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=90-94 "Status")]

Pokud chcete spravovat argumenty příkazového řádku, přidejte hlavní blok kódu.

   [!code-php[Main code](~/samples-luis/documentation-samples/authoring-api-samples/php/add-utterances.php?range=96-120 "Main code")]

## <a name="specify-utterances-to-add"></a>Určení promluv, které se mají přidat
Vytvořte soubor `utterances.json` a upravte ho tím, že určíte **pole promluv**, které chcete přidat do aplikace LUIS. Záměr a entity již **musí** být v aplikaci LUIS.

> [!NOTE]
> Před spuštěním kódu v souboru `add-utterances.php` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytváří záměry ani entity. Pouze přidává promluvy pro existující záměry a entity.

Pole `text` obsahuje text promluvy. Pole `intentName` musí odpovídat názvu záměru v aplikaci LUIS. Pole `entityLabels` je povinné. Pokud nechcete označovat žádné entity, zadejte prázdný seznam, jak je znázorněno v následujícím příkladu:

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

Spusťte aplikaci z příkazového řádku s PHP.

Zavoláním souboru `add-utterances.php` pouze s argumentem utterance.json se přidají nové promluvy, ale nepoužijí se k trénování služby LUIS.
````
> php add-utterances.php ./utterances.json
````

Z volání rozhraní API pro přidávání promluv se vrátí následující JSON. Pole `response` obsahuje promluvy, které se přidaly, v následujícím formátu. Hodnota `hasError` je false, což značí, že se promluva přidala.  

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
Zavoláním souboru `add-utterance.php` s argumentem `-train` odešlete požadavek na trénování. 

````
> php add-utterances.php ./utterances.json -train
````

> [!NOTE]
> Duplicitní promluvy se nepřidají znovu, ale ani nezpůsobí chybu. Pole `response` bude obsahovat ID původní promluvy.

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
Zavoláním aplikace s argumentem `-status` zkontrolujte stav trénování a zobrazte podrobnosti o stavu.

````
> php add-utterances.php -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s kurzem hotovi, odeberte sadu Visual Studio a konzolovou aplikaci, pokud už je nepotřebujete. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website