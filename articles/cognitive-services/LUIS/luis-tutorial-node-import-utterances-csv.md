---
title: Import projevů pomocí souboru Node.js – LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak programově vytvořit aplikaci LUIS z již existujících dat ve formátu CSV pomocí rozhraní API pro vytváření luis.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499469"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Vytvoření aplikace LUIS programově pomocí souboru Node.js

Služba LUIS poskytuje programové rozhraní API, které provádí vše, co dělá web [LUIS.](luis-reference-regions.md) To může ušetřit čas, pokud máte již existující data a bylo by rychlejší vytvořit aplikaci LUIS programově než zadáním informací ručně. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k webu [LUIS](luis-reference-regions.md) a vyhledejte [svůj klíč pro vytváření](luis-concept-keys.md#authoring-key) v nastavení účtu. Tento klíč slouží k volání vytváření api.
* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
* Tento článek začíná csv pro hypotetické soubory protokolu společnosti uživatelských požadavků. Stáhněte si ji [zde](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Nainstalujte nejnovější soubor Node.js pomocí služby NPM. Stáhněte si ji [zde](https://nodejs.org/en/download/).
* **[Doporučeno]** Visual Studio Kód pro IntelliSense a ladění, stáhněte si jej [zde](https://code.visualstudio.com/) zdarma.

Všechny kód v tomto článku je k dispozici na [azure ukázky jazyk porozumění GitHub úložiště](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapování již existujících dat na záměry a entity
I v případě, že máte systém, který nebyl vytvořen s LUIS v mysli, pokud obsahuje textová data, která mapuje na různé věci, které uživatelé chtějí dělat, můžete přijít s mapování z existujících kategorií vstupu uživatele na záměry v LUIS. Pokud můžete identifikovat důležitá slova nebo fráze v tom, co uživatelé řekli, tato slova mohou mapovat na entity.

Otevřete [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) soubor. Obsahuje protokol uživatelských dotazů na hypotetickou službu domácí automatizace, včetně toho, jak byly rozděleny do kategorií, co uživatel řekl, a některé sloupce s užitečnými informacemi vytaženými z nich. 

![CSV soubor již existujících dat](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Uvidíte, že **RequestType** sloupec může být záměry a **Request** sloupec zobrazuje příklad utterance. Ostatní pole mohou být entity, pokud k nim dojde v utterance. Vzhledem k tomu, že existují záměry, entity a příklad projevy, máte požadavky na jednoduché, ukázkové aplikace.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Postup generování aplikace LUIS z dat, která nejsou luis
Jak generovat novou aplikaci LUIS ze souboru CSV:

* Analyzovat data ze souboru CSV:
    * Převeďte do formátu, který můžete odeslat do služby LUIS pomocí rozhraní API pro vytváření. 
    * Z analyzovaných dat shromážděte informace o záměrech a entitách. 
* Vytváření volání rozhraní API pro:
    * Vytvořte aplikaci.
    * Přidejte záměry a entity, které byly shromážděny z analyzovaných dat. 
    * Po vytvoření aplikace LUIS můžete přidat ukázkové projevy z analyzovaných dat. 

Tento tok programu můžete vidět v `index.js` poslední části souboru. Zkopírujte nebo [stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) tento `index.js`kód a uložte jej do .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analyzovat CSV

Položky sloupce, které obsahují projevy v CSV musí být analyzovány do formátu JSON, který luis může pochopit. Tento formát JSON `intentName` musí obsahovat pole, které identifikuje záměr utterance. Musí také obsahovat `entityLabels` pole, které může být prázdné, pokud neexistují žádné entity v utterance. 

Například položka pro "Zapnout světla" mapuje na tento JSON:

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

V tomto příkladu `intentName` pochází z požadavku uživatele pod **záhlavím sloupec Požadavek** v souboru CSV a `entityName` pochází z ostatních sloupců s klíčovými informacemi. Například pokud je položka pro **operace** nebo **zařízení**a tento řetězec se vyskytuje také ve skutečném požadavku, pak může být označen jako entita. Následující kód ukazuje tento proces analýzy. Můžete jej [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) zkopírovat nebo stáhnout `_parse.js`a uložit do .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Vytvoření aplikace LUIS
Po analýzy dat do JSON, přidejte je do aplikace LUIS. Následující kód vytvoří aplikaci LUIS. Zkopírujte nebo [stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) a `_create.js`uložte jej do .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Přidání záměrů
Jakmile máte aplikaci, musíte ji vnímat. Následující kód vytvoří aplikaci LUIS. Zkopírujte nebo [stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) a `_intents.js`uložte jej do .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Přidání entit
Následující kód přidá entity do aplikace LUIS. Zkopírujte nebo [stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) a `_entities.js`uložte jej do .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Přidání projevů
Jakmile entity a záměry byly definovány v aplikaci LUIS, můžete přidat projevy. Následující kód používá [rozhraní API Utterances_AddBatch,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) které umožňuje přidat až 100 projevy najednou.  Zkopírujte nebo [stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) a `_upload.js`uložte jej do .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Spuštění kódu


### <a name="install-nodejs-dependencies"></a>Instalace závislostí node.js
Nainstalujte závislosti Node.js z npm do terminálu/příkazového řádku.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Změna nastavení konfigurace
Chcete-li použít tuto aplikaci, musíte změnit hodnoty v souboru index.js na vlastní klíč koncového bodu a zadat název, který má mít aplikace. Můžete také nastavit jazykovou verzi aplikace nebo změnit číslo verze.

Otevřete soubor index.js a změňte tyto hodnoty v horní části souboru.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Spuštění skriptu
Spusťte skript z terminálu/příkazového řádku pomocí souboru Node.js.

```console
> node index.js
```

– nebo –

```console
> npm start
```

### <a name="application-progress"></a>Průběh aplikace
Při spuštění aplikace příkazový řádek zobrazuje průběh. Výstup příkazového řádku obsahuje formát odpovědí z LUIS.

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




## <a name="open-the-luis-app"></a>Otevření aplikace LUIS
Po dokončení skriptu se můžete přihlásit ke [službě LUIS](luis-reference-regions.md) a zobrazit aplikaci LUIS, kterou jste vytvořili v části **Moje aplikace**. Měli byste být schopni zobrazit projevy, které jste přidali pod **TurnOn**, **TurnOff**a **None** záměry.

![Záměr turnonu](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování a trénování aplikace na webu LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Další zdroje

Tato ukázková aplikace používá následující pravidla API LUIS:
- [vytvoření aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [přidání záměrů](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [přidání entit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [přidání promluv](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
