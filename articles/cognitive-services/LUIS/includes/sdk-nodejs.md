---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f81ec647eefd8b3bd59a60855c0383a32caf8acb
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574387"
---
K Node.js můžete použít klientské knihovny Language Understanding (LUIS):

* Vytvoření aplikace
* Přidejte záměr, počítačově získanou entitu s příkladem utterance
* Výuka a publikování aplikace
* Modul runtime předpovědi dotazů

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)  |   Zdrojový kód knihovny pro [vytváření](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) a [předpovědi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Vytváření](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) a [prediktivní](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) npm | [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek pro vytváření Language Understanding](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) , abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * Pro připojení aplikace k Language Understanding vytváření obsahu budete potřebovat klíč a koncový bod z prostředku, který [vytvoříte](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) . Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu. Službu můžete vyzkoušet pomocí bezplatné cenové úrovně ( `F0` ).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-javascript-application"></a>Vytvoření nové aplikace JavaScriptu

1. V okně konzoly vytvořte nový adresář pro aplikaci a přesuňte se do tohoto adresáře.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Inicializujte adresář jako aplikaci JavaScriptu vytvořením `package.json` souboru.

    ```console
    npm init -y
    ```

1. Vytvořte soubor s názvem `index.js` pro kód JavaScriptu.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Instalace knihoven NPM

V adresáři aplikace nainstalujte závislosti s následujícími příkazy, které byly spuštěny vždy po jednom řádku:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

Váš `package.json` vzhled by měl vypadat takto:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Vytváření objektového modelu

Klient pro vytváření Language Understanding (LUIS) je objekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) , který se ověřuje v Azure, který obsahuje váš klíč pro vytváření obsahu.

## <a name="code-examples-for-authoring"></a>Příklady kódu pro vytváření obsahu

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* Aplikace – [Přidání](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [odstranění](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publikování](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Příklad projevy – [Přidání v dávce](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [odstranění podle ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funkce – Správa [seznamů frází](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Model – Správa [záměrů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) a entit
* Vzor – Správa [vzorů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Výuka aplikace [train](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) a dotazování na [stav školení](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) – Správa pomocí klonování, exportu a odstranění

## <a name="prediction-object-model"></a>Model objektu předpovědi

Klient pro vytváření Language Understanding (LUIS) je objekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) , který se ověřuje v Azure, který obsahuje váš klíč pro vytváření obsahu.

## <a name="code-examples-for-prediction-runtime"></a>Příklady kódu pro předpověď za běhu

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* [Předpověď](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) podle `staging` nebo `production` slotu
* [Předpověď podle verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Přidat závislosti

Otevřete `index.js` soubor v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) a pak přidejte následující závislosti.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Přidat často používaný kód

1. Přidejte `quickstart` metodu a její volání. Tato metoda uchovává většinu zbývajícího kódu. Tato metoda je volána na konci souboru.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Pokud není uvedeno jinak, přidejte zbývající kód v metodě rychlý Start.

## <a name="create-variables-for-the-app"></a>Vytvoření proměnných pro aplikaci

Vytvořte dvě sady proměnných: první sada, kterou změníte, druhá sada ponechá, jak se zobrazí v ukázce kódu. 

1. Vytvořte proměnné pro uložení klíče pro vytváření a názvů prostředků.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Vytvořte proměnné pro uložení koncových bodů, názvu aplikace, verze a názvu záměru.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) .

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

Aplikace LUIS obsahuje model NLP (přirozený jazyk Processing), včetně záměrů, entit a příkladu projevy.

Vytvořte metodu [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objektu [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) k vytvoření aplikace. Název a jazyková verze jazyka jsou požadované vlastnosti.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Vytvořit záměr pro aplikaci
Primárním objektem v modelu aplikace LUIS je záměr. Záměr se zarovnává se seskupením _záměrů_ uživatelů utterance. Uživatel může požádat o dotaz nebo vytvořit příkaz, který hledá konkrétní _zamýšlenou_ odpověď z robota (nebo jiné klientské aplikace). Příklady záměrů je vyúčtování letu, dotazování na počasí v cílovém městě a dotazování na kontaktní informace pro zákaznické služby.

Použijte metodu [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) s názvem jedinečného záměru a potom předejte ID aplikace, ID verze a název nového záměru.

`intentName`Hodnota je pevně zakódována `OrderPizzaIntent` jako součást proměnných v oddílu [Vytvoření proměnných pro aplikaci](#create-variables-for-the-app) .

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

I když se entity nevyžadují, najdete je ve většině aplikací. Entita extrahuje informace z utterance uživatele, která je nutná k zaúmyslnému záměru uživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) a vlastních entit, z nichž každý má vlastní modely DTO (Data Transformation Object).  Mezi běžné předem připravené entity, které se mají přidat do vaší aplikace, patří [číslo](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md)a [Ordinal](../luis-reference-prebuilt-ordinal.md).

Je důležité, abyste věděli, že entity nejsou označené záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkový uživatel projevy je označen pro konkrétní, jednotlivý záměr.

Metody vytváření entit jsou součástí třídy [modelu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) . Každý typ entity má svůj vlastní model objektu pro transformaci dat (DTO).

Kód pro vytvoření entity vytvoří entitu strojového učení s podentitami a funkcemi použitými u `Quantity` subentit.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Částečná obrazovka z portálu znázorňující vytvořenou entitu, entitu strojového učení s podentitami a funkcemi použitou pro subentity &quot;množství&quot;.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Vložte následující metodu nad `quickstart` metodu a vyhledejte ID dílčí entity, aby bylo možné přiřadit funkce dané subentitě.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Přidat příklad utterance k záměru

Aby bylo možné určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a označit všechny vlastní entity. Předem připravené entity není nutné označit.

Přidejte příklad projevy vytvořením seznamu objektů [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) , jeden objekt pro každý příklad utterance. Každý příklad by měl označit všechny entity pomocí slovníku dvojic název-hodnota a hodnota entity. Hodnota entity by měla být přesně tak, jak se zobrazuje v textu příkladu utterance.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Částečný snímek obrazovky ukazující vzorový utterance na portálu ":::

[Příklady volání. Přidejte](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-) s ID aplikace, ID verze a příkladem.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu musí být aplikace LUIS vyškolená pro tuto verzi modelu. Vyškolený model se dá použít v [kontejneru](../luis-container-howto.md)nebo ho můžete [publikovat](../luis-how-to-publish-app.md) na pracovní nebo výrobní sloty.

Metoda [vlak. trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) potřebuje ID aplikace a ID verze.

Velmi malý model, jako je například tento rychlý Start, bude vlak velmi rychlý. Pro aplikace na úrovni produkčního prostředí by mělo školení aplikace zahrnovat volání metody [get_Status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) k určení, kdy nebo jestli se školení podařilo. Odpověď je seznam objektů [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) s odděleným stavem pro každý objekt. Aby bylo školení považovat za dokončené, musí být všechny objekty úspěšné.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publikovat aplikaci do produkčního slotu

Publikujte aplikaci LUIS pomocí metody [App. Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Tím se publikuje aktuální vyškolená verze v zadaném slotu na koncovém bodu. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakci entit.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Ověření běhového klienta předpovědi

Použijte pro svůj klíč objekt msRest. ApiKeyCredentials a použijte ho u svého koncového bodu k vytvoření [Luis. Objekt LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Získat předpovědi z modulu runtime

Přidejte následující kód, který vytvoří požadavek na modul runtime předpovědi. Uživatel utterance je součástí objektu [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) .

Metoda **[luisRuntimeClient. předpověď. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** potřebuje několik parametrů, jako je ID aplikace, název slotu a objekt žádosti o předpověď, který požadavek splní. Další možnosti jako verbose, Zobrazit všechny záměry a protokol jsou volitelné.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node index.js` příkazu v souboru rychlého startu.

```console
node index.js
```
