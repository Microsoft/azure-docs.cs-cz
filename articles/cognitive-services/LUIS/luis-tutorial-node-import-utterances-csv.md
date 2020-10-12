---
title: Import projevy pomocí Node.js-LUIS
titleSuffix: Azure Cognitive Services
description: Naučte se vytvářet aplikace LUIS programově z existujících dat ve formátu CSV pomocí rozhraní API pro vytváření LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-js
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.openlocfilehash: 58eb92f4d0bc3de4671ca2ece14a178a876e4a6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541033"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Programové vytvoření aplikace v LUIS pomocí Node.js

LUIS poskytuje programové rozhraní API, které provádí všechny weby [Luis](luis-reference-regions.md) . To může ušetřit čas i v případě, že máte již existující data a je rychlejší vytvořit aplikaci LUIS programově, než zadáním informací ručně.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k webu [Luis](luis-reference-regions.md) a vyhledejte svůj [klíč pro vytváření obsahu](luis-how-to-azure-subscription.md#authoring-key) v nastavení účtu. Tento klíč použijete k volání rozhraní API pro vytváření obsahu.
* Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).
* Tento článek začíná na CSV pro soubory protokolu hypotetické společnosti pro požadavky uživatelů. Stáhněte si ho [sem](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Nainstalujte nejnovější Node.js pomocí nástroje NPM. Stáhněte [si ho odsud.](https://nodejs.org/en/download/)
* **[Doporučeno]** Visual Studio Code pro IntelliSense a ladění, Stáhněte si ho [odsud zdarma](https://code.visualstudio.com/) .

Veškerý kód v tomto článku je k dispozici v [úložišti Azure-samples Language Understanding GitHubu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv).

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapování stávajících dat k záměrům a entitám
I v případě, že máte systém, který se nevytvořil s LUIS, pokud obsahuje textová data, která se mapují na různé věci, které chtějí uživatelé udělat, možná budete moct vytvořit mapování z existujících kategorií vstupu uživatele na záměry v LUIS. Pokud můžete identifikovat důležitá slova nebo fráze v tom, co uživatelé uvedli, můžou být tato slova namapována na entity.

Otevřete [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) soubor. Obsahuje protokol uživatelských dotazů na hypotetickou službu pro domácnost, včetně toho, jak byly zařazeny do kategorií, podle toho, co uživatel uvedli, a některých sloupců s užitečnými informacemi z nich.

![Soubor CSV s již existujícími daty](./media/luis-tutorial-node-import-utterances-csv/csv.png)

Vidíte, že sloupec **RequestType** by mohl být záměrem a sloupec **Request** zobrazuje příklad utterance. Ostatní pole mohou být entity, pokud se vyskytují v utterance. Vzhledem k tomu, že existují záměry, entity a příklady projevy, máte požadavky na jednoduchou ukázkovou aplikaci.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Postup generování aplikace LUIS z jiných než LUIS dat
Vytvoření nové aplikace LUIS ze souboru CSV:

* Analyzovat data ze souboru CSV:
    * Převeďte na formát, který můžete nahrát na LUIS pomocí rozhraní API pro vytváření obsahu.
    * Z analyzovaných dat Shromážděte informace o záměrech a entitách.
* Vytvářená volání rozhraní API:
    * Vytvořte aplikaci.
    * Přidejte záměry a entity shromážděné z analyzovaných dat.
    * Po vytvoření aplikace LUIS můžete přidat příklad projevy z analyzovaných dat.

Tento tok programu vidíte v poslední části `index.js` souboru. Zkopírujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) tento kód a uložte ho do souboru `index.js` .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analyzovat sdílený svazek clusteru

Položky sloupců obsahující projevy ve sdíleném svazku clusteru musí být analyzovány do formátu JSON, který LUIS dokáže pochopit. Tento formát JSON musí obsahovat `intentName` pole, které určuje záměr utterance. Musí obsahovat také `entityLabels` pole, které může být prázdné, pokud utterance žádné entity.

Například položka pro "zapnout světla" namapuje na tento kód JSON:

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

V tomto příkladu `intentName` pochází z požadavku uživatele v záhlaví sloupce **žádosti** v souboru CSV a `entityName` z dalších sloupců obsahuje klíčové informace. Pokud je například položka pro **operaci** nebo **zařízení**a k tomuto řetězci dojde také v samotné žádosti, může být označena jako entita. Následující kód demonstruje tento proces analýzy. Můžete ho zkopírovat nebo [Stáhnout](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) a Uložit do `_parse.js` .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Vytvoření aplikace LUIS
Jakmile se data analyzují do formátu JSON, přidejte je do aplikace LUIS. Následující kód vytvoří aplikaci LUIS. Zkopírujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) a uložte ho do `_create.js` .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Přidání záměrů
Jakmile máte aplikaci, je nutné k ní přizáměrit. Následující kód vytvoří aplikaci LUIS. Zkopírujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) a uložte ho do `_intents.js` .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Přidání entit
Následující kód přidá entity do aplikace LUIS. Zkopírujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) a uložte ho do `_entities.js` .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>Přidání projevů
Po definování entit a záměrů v aplikaci LUIS můžete přidat projevy. Následující kód používá rozhraní [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, které umožňuje přidat až 100 projevy v čase.  Zkopírujte nebo [Stáhněte](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) a uložte ho do `_upload.js` .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Spuštění kódu


### <a name="install-nodejs-dependencies"></a>Nainstalovat Node.js závislosti
Nainstalujte Node.js závislosti z NPM do terminálu nebo příkazového řádku.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Změnit nastavení konfigurace
Aby bylo možné tuto aplikaci používat, je třeba změnit hodnoty v souboru index.js na vlastní klíč koncového bodu a zadat název, který má aplikace mít. Můžete také nastavit jazykovou verzi aplikace nebo změnit číslo verze.

Otevřete soubor index.js a změňte hodnoty v horní části souboru.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Spuštění skriptu
Spusťte skript z terminálu nebo příkazového řádku s Node.js.

```console
> node index.js
```

nebo

```console
> npm start
```

### <a name="application-progress"></a>Průběh aplikace
Když je aplikace spuštěná, zobrazí se v příkazovém řádku průběh. Výstup příkazového řádku zahrnuje formát odpovědí z LUIS.

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
Až se skript dokončí, můžete se přihlásit k [Luis](luis-reference-regions.md) a zobrazit aplikaci Luis, kterou jste vytvořili v části **Moje aplikace**. Měli byste být schopni zobrazit projevy, které jste přidali pod **TurnOn**, **TurnOff**a **žádné** záměry.

![Záměr TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Testování a výuka aplikace na webu LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Další zdroje

Tato ukázková aplikace používá následující LUIS rozhraní API:
- [vytvořit aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Přidat záměry](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Přidat entity](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [Přidat projevy](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
