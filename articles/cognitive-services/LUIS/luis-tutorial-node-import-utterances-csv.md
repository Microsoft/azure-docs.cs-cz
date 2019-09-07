---
title: Import projevy pomocí Node. js – LUIS
titleSuffix: Azure Cognitive Services
description: Informace o sestavení aplikace LUIS prostřednictvím kódu programu z dříve existující data ve formátu CSV pomocí rozhraní API pro vytváření LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1bee26dc57fd844703e2c9c97b38b9a433227fbf
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387942"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Sestavení aplikace LUIS programově pomocí Node.js

LUIS poskytuje programový rozhraní API, která provádí všechno, co, který [LUIS](luis-reference-regions.md) webu nepodporuje. To můžete ušetřit čas, pokud máte existující data a bude rychlejší k vytvoření aplikace LUIS programově než ručně zadáním informací. 

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k webu [Luis](luis-reference-regions.md) a vyhledejte svůj [klíč pro vytváření obsahu](luis-concept-keys.md#authoring-key) v nastavení účtu. Tento klíč použijete k volání rozhraní API pro vytváření.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Tento článek začíná na CSV pro soubory protokolu hypotetické společnosti pro požadavky uživatelů. Stáhněte si ho [tady](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Nainstalujte nejnovější Node.js pomocí NPM. Stáhněte si ji z [tady](https://nodejs.org/en/download/).
* **[Doporučuje]**  Visual Studio Code pro technologii IntelliSense a ladění, si ji stáhnout z [tady](https://code.visualstudio.com/) zdarma.

Veškerý kód v tomto článku je k dispozici v [úložišti Azure-samples Language Understanding GitHubu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Dříve existující data mapy a záměry a entity
I když máte systému, který nebyl vytvořen pomocí služby LUIS v úvahu, pokud obsahuje textová data, která mapuje se na různé věci uživatele chcete udělat, je možné a navrhněte mapování z existující kategorie záměry v LUIS vstup uživatele. Pokud můžete identifikovat důležité slova nebo fráze v co říci uživatelům, může tato slova namapovat na entity.

[`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) Otevřete soubor. Obsahuje protokol dotazy uživatelů hypotetické domácí automatizace služby, včetně jak byly do kategorií, co říká uživatele a některé sloupce s užitečnými informacemi získaných z nich. 

![Soubor CSV z již existujících dat](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Uvidíte, že **RequestType** sloupce může být záměrů a **žádosti** sloupci se zobrazuje příkladu utterance. Ostatní pole může být entity, když se vyskytují v utterance. Protože záměrů, entit a příklad projevy, máte požadavky na jednoduchý, ukázkovou aplikaci.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Kroky pro vygenerování aplikace LUIS z dat bez LUIS
Vytvoření nové aplikace LUIS ze souboru CSV:

* Analyzovat data ze souboru CSV:
    * Převeďte na formát, který můžete nahrát na LUIS pomocí rozhraní API pro vytváření obsahu. 
    * Z analyzovaných dat Shromážděte informace o záměrech a entitách. 
* Vytvářená volání rozhraní API:
    * Vytvořte aplikaci.
    * Přidejte záměry a entity shromážděné z analyzovaných dat. 
    * Po vytvoření aplikace LUIS můžete přidat projevy příklad z analyzovaných datech. 

Tento tok programu vidíte v poslední části `index.js` souboru. Kopírování nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) tento kód a uložte ho do `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analyzovat sdílený svazek clusteru

Sloupec položky, které obsahují projevy ve sdíleném svazku clusteru musí být získány do formátu JSON, který by rozuměla služba LUIS. Musí obsahovat tento formát JSON `intentName` pole, které identifikuje záměr utterance. Musí obsahovat také `entityLabels` pole, které může být prázdný, pokud neexistují žádné entity v utterance. 

Například zadání "Vypnul světla" mapuje na tento dokument JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

V tomto příkladu `intentName` pochází z uživatelského požadavku v rámci **požadavek** záhlaví sloupce v souboru CSV a `entityName` pochází z ostatních sloupců s informací o klíči. Například, pokud je záznam pro **operace** nebo **zařízení**a že v aktuálního požadavku dojde také k řetězec a pak mohou být označeny jako entity. Následující kód ukazuje, tento proces analýzy. Můžete zkopírovat nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) jej a uložit ho. tím `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Vytvoření aplikace LUIS
Jakmile se data má být do formátu JSON, můžete ho přidáte do aplikace LUIS. Následující kód vytvoří aplikaci služby LUIS. Kopírování nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) a uložte ho do `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Přidání záměrů
Jakmile budete mít aplikaci, budete muset záměry na ni. Následující kód vytvoří aplikaci služby LUIS. Kopírování nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) a uložte ho do `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Přidání entit
Následující kód přidá entity do aplikace LUIS. Kopírování nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) a uložte ho do `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Přidání projevů
Po entity a záměry byly definovány v aplikaci LUIS, můžete přidat projevy. Následující kód používá [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) rozhraní API, které vám umožní přidat až 100 projevy najednou.  Kopírování nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) a uložte ho do `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Spuštění kódu


### <a name="install-nodejs-dependencies"></a>Nainstalujte Node.js závislosti
Instalace Node.js závislosti z NPM, v terminálu nebo příkazového řádku.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Změna nastavení konfigurace
Pokud chcete používat tuto aplikaci, budete muset změnit hodnoty v souboru index.js na vlastní klíče koncového bodu a zadejte název, který chcete aplikaci. Můžete také nastavit jazykovou verzi aplikace nebo změňte číslo verze.

Otevřete soubor index.js a změnit tyto hodnoty v horní části souboru.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Spuštění skriptu
Spusťte skript z terminálu nebo příkazového řádku s využitím Node.js.

```console
> node index.js
```

nebo

```console
> npm start
```

### <a name="application-progress"></a>Průběh aplikace
Když je spuštěná aplikace příkazového řádku zobrazuje průběh. Výstup příkazového řádku obsahuje formátu odpovědi ze služby LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Otevřete aplikaci LUIS
Až se skript dokončí, můžete se přihlásit k [Luis](luis-reference-regions.md) a zobrazit aplikaci Luis, kterou jste vytvořili v části **Moje aplikace**. Byste měli vidět projevy, které jste přidali v rámci **TurnOn**, **vypnutí**, a **žádný** záměry.

![TurnOn záměr](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Testování a jejich trénování vaší aplikace na webu služby LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Další zdroje informací:

Tato ukázková aplikace používá následující rozhraní LUIS API:
- [Vytvoření aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Přidání záměrů](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Přidání entit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Přidání projevů](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
