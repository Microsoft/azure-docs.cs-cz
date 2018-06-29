---
title: Kurz přidávání promluv do aplikace LUIS pomocí Javy | Microsoft Docs
description: V tomto kurzu zjistíte, jak volat aplikaci LUIS pomocí Javy.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266035"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Kurz: Přidávání promluv do aplikace pomocí Javy 
V tomto kurzu napíšete program, který do záměru přidá promluvu pomocí rozhraní API pro vytváření v Javě.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření projektu konzoly v sadě Visual Studio 
> * Přidání metody pro volání rozhraní API služby LUIS za účelem přidání promluvy a trénování aplikace
> * Přidání souboru JSON s ukázkovými promluvami pro záměr BookFlight (Rezervovat let)
> * Spuštění konzoly a zobrazení stavu trénování pro promluvy

Další informace najdete v technické dokumentaci k rozhraním API pro [přidání ukázkové promluvy do záměru](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a [stav trénování](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html) Oracle.
* [Knihovna GSON JSON od Googlu](https://github.com/google/gson).
* **[Klíč pro vytváření](luis-concept-keys.md#authoring-key)** pro službu LUIS. Tento klíč najdete v části Account Settings (Nastavení účtu) na webu [LUIS](luis-reference-regions.md).
* [**ID stávající aplikace**](./luis-get-started-create-app.md) LUIS. ID aplikace je uvedené na řídicím panelu aplikace. Před spuštěním kódu v souboru `AddUtterances.java` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytvoří záměry ani entity. Pouze přidá promluvy pro existující záměry a entity. 
* **ID verze** aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* Vytvořte nový textový soubor `AddUtterances.java`.

> [!NOTE] 
> Kompletní soubor `add-utterances.cs` a ukázkový soubor `utterances.json` jsou dostupné v [úložišti **LUIS-Samples** na GitHubu](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>Psaní kódu Java

Přidejte do souboru závislosti Java.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

Vytvořte třídu `AddUtterances`.

```Java
public class AddUtterances {
    // Insert code here
}
```

Tato třída bude obsahovat všechny následující fragmenty kódu.

Přidejte do třídy konstanty LUIS. Zkopírujte následující kód a použijte vlastní klíč pro vytváření, ID aplikace a ID verze.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Přidejte metodu pro volání rozhraní API služby LUIS. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Přidejte metodu pro odpověď HTTP z rozhraní API služby LUIS.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Přidejte zpracování výjimek. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Přidejte zpracování výstupu/výpisu.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Přidejte hlavní funkci.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>Určení promluv, které se mají přidat
Vytvořte soubor `utterances.json` a upravte ho tím, že určíte **pole promluv**, které chcete přidat do aplikace LUIS. Záměr a entity již **musí** být v aplikaci LUIS.

> [!NOTE]
> Před spuštěním kódu v souboru `AddUtterances.java` musí existovat aplikace LUIS se záměry a entitami použitými v souboru `utterances.json`. Kód v tomto článku nevytvoří záměry ani entity. Pouze přidá promluvy pro existující záměry a entity.

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

Zkompilujte AddUtterance se závislostmi.

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Zavoláním `AddUtterance` bez argumentů se do aplikace přidají promluvy LUIS, ale nepoužijí se k trénování.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Zavoláním `add-utterance` s argumentem `-train` odešlete požadavek na trénování. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Duplicitní promluvy se nepřidají znovu, ale ani nezpůsobí chybu. Pole `response` bude obsahovat ID původní promluvy.

Když zavoláte aplikaci s argumentem `-train`, vytvoří se soubor `training-results.json`, který značí úspěšné zařazení požadavku na trénování aplikace LUIS do fronty. 

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
Zavoláním aplikace s argumentem `-status` zkontrolujte stav trénování a zapište podrobnosti o stavu do souboru.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Vyčištění prostředků
Jakmile budete s kurzem hotovi, odeberte sadu Visual Studio a konzolovou aplikaci, pokud už je nepotřebujete. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Sestavení aplikace LUIS prostřednictvím kódu programu](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website