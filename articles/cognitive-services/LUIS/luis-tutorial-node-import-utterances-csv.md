---
title: Vytvořit aplikaci LEOŠ programově pomocí Node.js | Microsoft Docs
titleSuffix: Azure
description: Naučte se vytvářet aplikace LEOŠ prostřednictvím kódu programu z dříve existující data ve formátu CSV pomocí rozhraní API pro tvorbu LEOŠ.
services: cognitive-services
author: DeniseMak
manager: rstand
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: 09c9d4da835b7b30fd132770f9d13b33fa80a3f5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268327"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Vytvořit aplikaci LEOŠ programově pomocí Node.js

LEOŠ poskytuje programovací rozhraní API, které nemá všechno, která [LEOŠ] [ LUIS] nemá webu. To můžete ušetřit čas, pokud máte stávající data a je rychlejší k vytvoření aplikace LEOŠ prostřednictvím kódu programu než zadáním informace ručně. 

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k [LEOŠ] [ LUIS] webu a najít váš [vytváření klíč](luis-concept-keys.md#authoring-key) v nastavení účtu. Tento klíč použijete k volání rozhraní API pro vytváření obsahu.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* V tomto kurzu začíná sdílený svazek clusteru pro soubory protokolů hypotetický společnosti uživatelských požadavků. Stažení [zde](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Nainstalujte nejnovější Node.js pomocí NPM. Stažení z [zde](https://nodejs.org/en/download/).
* **[Doporučeno]**  Technologie IntelliSense a ladění, visual Studio Code stažení z [sem](https://code.visualstudio.com/) zdarma.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapování dříve existující dat tříd Intent a entit
I v případě, že máte systém, který nebyl vytvořen s LEOŠ pamatovat, pokud obsahuje textové data, která mapuje uživatele na různých věcí chcete udělat, je možné spolu s mapování z existující kategorie vstupu uživatele na záměry v LEOŠ. V případě, že můžete identifikovat důležité slova nebo fráze v co uvedená uživatele, může tato slova mapování entity.

Otevřete soubor `IoT.csv`. Obsahuje protokolu uživatelských dotazů hypotetický domácí automatizace služby, včetně jak byly klasifikovány, co uživatel uvedená a některé sloupce s vyžádat mimo je užitečné informace. 

![Soubor CSV](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Uvidíte, že **RequestType** sloupec může být záměry a **požadavku** sloupci se zobrazuje utterance příklad. Pokud se vyskytují v utterance, může být v ostatních polích entity. Protože jsou záměry, entit a příklad utterances, máte požadavky na jednoduchý, ukázkovou aplikaci.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Kroky pro vygenerování LEOŠ aplikace z jiných LEOŠ dat
Pokud chcete vygenerovat novou aplikaci LEOŠ ze zdrojového souboru, nejprve můžete analyzovat data ze souboru CSV a tato data převést do formátu, který nahrajete do LEOŠ pomocí rozhraní API pro vytváření obsahu. Z analyzovaná data shromáždit informace o co tříd Intent a entity existují. Potom můžete provádět volání rozhraní API pro vytvoření aplikace a přidat záměry a entitami, které byly získány z analyzovaných datech. Po vytvoření aplikace LEOŠ, můžete přidat utterances příklad z analyzovaných datech. Zobrazí se tento tok v poslední části následující kód. Kopírování nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js) tento kód a uložit ho v `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analyzovat sdílený svazek clusteru

Položky sloupce, které obsahují utterances ve sdíleném svazku clusteru musí být získány do formátu JSON, který můžete porozumět LEOŠ. Musí obsahovat tento formát JSON `intentName` pole, které identifikují záměr utterance. Musí obsahovat také `entityLabels` pole, které může být prázdný, pokud nejsou žádné entity v utterance. 

Například zadání "Zapnout indikátory" mapy tento formát JSON:

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

V tomto příkladu `intentName` pochází z požadavku uživatele v části **požadavku** v souboru CSV záhlaví sloupce a `entityName` pochází z ostatních sloupců s informací o klíči. Například, pokud je položka pro **operace** nebo **zařízení**a že řetězec dochází také v skutečné požadavku, a potom může být označený jako entity. Následující kód ukazuje, to analýzu procesu. Můžete zkopírovat nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js) ho a uložte ho do `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Vytvoření aplikace LEOŠ
Po analýze dat do formátu JSON, přidejte ji do aplikace LEOŠ. Následující kód vytvoří LEOŠ aplikaci. Kopírování nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js) a uložte ho do `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Přidání záměrů
Jakmile máte aplikace, budete muset záměry k němu. Následující kód vytvoří LEOŠ aplikaci. Kopírování nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js) a uložte ho do `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Přidání entit
Následující kód přidá do aplikace LEOŠ entity. Kopírování nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js) a uložte ho do `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Přidání projevů
Jakmile entit a tříd Intent byly definovány v aplikaci LEOŠ, můžete přidat utterances. Následující kód používá [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) rozhraní API, které umožňuje přidat až 100 utterances najednou.  Kopírování nebo [Stáhnout](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js) a uložte ho do `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Spuštění kódu


### <a name="install-nodejs-dependencies"></a>Nainstalovat Node.js závislosti
Nainstalujte Node.js závislosti z NPM v terminálu nebo příkazového řádku.

````
> npm install
````

### <a name="change-configuration-settings"></a>Změňte nastavení konfigurace
Chcete-li používat tuto aplikaci, musíte změnit hodnoty v souboru index.js na svůj vlastní klíč předplatného a zadejte název, chcete mít aplikaci. Můžete také nastavit jazykovou verzi aplikace nebo změnit číslo verze.

Otevřete soubor index.js a změňte tyto hodnoty v horní části souboru.


````JavaScript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
````
### <a name="run-the-script"></a>Spusťte skript
Spusťte skript z terminálu nebo příkazového řádku s Node.js.

````
> node index.js
````
nebo
````
> npm start
````

### <a name="application-progress"></a>Průběh aplikace
Když aplikace běží, příkazového řádku zobrazí průběh. Výstup příkazového řádku zahrnuje formát odpovědi z LEOŠ.

````
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
````




## <a name="open-the-luis-app"></a>Otevřete aplikaci LEOŠ
Po dokončení skriptu, se můžete se přihlásit k [LEOŠ] [ LUIS] a LEOŠ aplikace jste vytvořili v části **Moje aplikace**. Nyní byste měli mít zobrazíte utterances jste přidali v části **TurnOn**, **vypnutí**, a **žádné** tříd Intent.

![Záměr TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Testování a cvičení aplikace v rámci LEOŠ webu](interactive-test.md)

## <a name="additional-resources"></a>Další zdroje informací:

Tato ukázková aplikace používá následující LEOŠ rozhraní API:
- [Vytvoření aplikace](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Přidat záměry](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Přidání entity](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Přidat utterances](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

