---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372139"
---
Pomocí klientské knihovny pro vytváření Language Understanding (LUIS) pro Node. js proveďte tyto kroky:

* Vytvořte aplikaci.
* Přidejte záměry, entity a příklad projevy.
* Přidejte funkce, jako je například seznam frází.
* Výuka a publikování aplikace
* Odstranit aplikaci

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [vytváření obsahu (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [balíček runtime (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Požadavky

* Language Understanding prostředek pro vytváření: [vytvořte ho v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-starter-key"></a>Získání spouštěcího klíče pro Language Understanding (LUIS)

Získejte [počáteční klíč](../luis-how-to-azure-subscription.md#starter-key) vytvořením prostředku pro vytváření Luis. Zachovejte klíč a koncový bod klíče pro další krok.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a oblasti pro klíč vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_AUTHORING_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `LUIS_AUTHORING_ENDPOINT` – koncový bod přidružený k vašemu klíči.

Použijte pokyny pro váš operační systém.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalace knihovny NPM pro vytváření obsahu LUIS

V adresáři aplikace nainstalujte závislosti pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objektový model

Klient pro vytváření Language Understanding (LUIS) je objekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) , který se ověřuje v Azure, který obsahuje váš klíč pro vytváření obsahu.

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* Aplikace – [Přidání](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [odstranění](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publikování](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Příklad projevy – [Přidání v dávce](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [odstranění podle ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funkce – Správa [seznamů frází](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Model – Správa [záměrů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) a entit
* Vzor – Správa [vzorů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Výuka aplikace [](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) a dotazování na [stav školení](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) – Správa pomocí klonování, exportu a odstranění


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro vytváření Language Understanding (LUIS) pro Node. js:

* [Vytvoření aplikace](#create-a-luis-app)
* [Přidání entit](#create-entities-for-the-app)
* [Přidání záměrů](#create-intent-for-the-app)
* [Přidat příklad projevy](#add-example-utterance-to-intent)
* [Výuka aplikace](#train-the-app)
* [Publikování aplikace](#publish-a-language-understanding-app)
* [Odstranit aplikaci](#delete-a-language-understanding-app)
* [Seznam aplikací](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte nový textový soubor v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `luis_authoring_quickstart.js`. Pak přidejte následující závislosti.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) .

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Vytvořte aplikaci LUIS, která bude obsahovat pracovní záměry modelu NLP (přirozený jazyk Processing), entity a příklad projevy.

1. Vytvořte metodu [Add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objektu [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) k vytvoření aplikace. Název a jazyková verze jazyka jsou požadované vlastnosti.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Vytvořit záměr pro aplikaci
Primárním objektem v modelu aplikace LUIS je záměr. Záměr se zarovnává se seskupením _záměrů_uživatelů utterance. Uživatel může požádat o dotaz nebo vytvořit příkaz, který hledá konkrétní _zamýšlenou_ odpověď z robota (nebo jiné klientské aplikace). Příklady záměrů je vyúčtování letu, dotazování na počasí v cílovém městě a dotazování na kontaktní informace pro zákaznické služby.

Použijte metodu [model. add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) s názvem jedinečného záměru a potom předejte ID aplikace, ID verze a název nového záměru.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

I když se entity nevyžadují, najdete je ve většině aplikací. Entita extrahuje informace z utterance uživatele, která je nutná k zaúmyslnému záměru uživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) a vlastních entit, z nichž každý má vlastní modely DTO (Data Transformation Object).  Mezi běžné předem připravené entity, které se mají přidat do vaší aplikace, patří [číslo](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md)a [Ordinal](../luis-reference-prebuilt-ordinal.md).

Tato metoda **add_entities** vytvořila `Location` jednoduchou entitu se dvěma rolemi, `Class` jednoduchou entitou, `Flight` složenou entitou a přidává několik předem sestavených entit.

Je důležité, abyste věděli, že entity nejsou označené záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkový uživatel projevy je označen pro konkrétní, jednotlivý záměr.

Metody vytváření entit jsou součástí třídy [modelu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) . Každý typ entity má svůj vlastní model objektu pro transformaci dat (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Přidat příklad utterance k záměru

Aby bylo možné určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a označit všechny vlastní entity. Předem připravené entity není nutné označit.

Přidejte příklad projevy vytvořením seznamu objektů [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) , jeden objekt pro každý příklad utterance. Každý příklad by měl označit všechny entity pomocí slovníku dvojic název-hodnota a hodnota entity. Hodnota entity by měla být přesně tak, jak se zobrazuje v textu příkladu utterance.

[Příklady volání. Batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) s ID aplikace, ID verze a seznam příkladů. Volání odpoví seznamem výsledků. Je potřeba zkontrolovat výsledek každého příkladu, abyste se ujistili, že se úspěšně přidal do modelu.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu musí být aplikace LUIS vyškolená pro tuto verzi modelu. Vyškolený model se dá použít v [kontejneru](../luis-container-howto.md)nebo ho můžete [publikovat](../luis-how-to-publish-app.md) na pracovní nebo výrobní sloty.

Metoda [vlak. trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) potřebuje ID aplikace a ID verze.

Velmi malý model, jako je například tento rychlý Start, bude vlak velmi rychlý. Pro aplikace na úrovni produkčního prostředí by mělo školení aplikace zahrnovat volání metody [get_Status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) k určení, kdy nebo jestli se školení podařilo. Odpověď je seznam objektů [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) s odděleným stavem pro každý objekt. Aby bylo školení považovat za dokončené, musí být všechny objekty úspěšné.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Školení všech modelů trvá určitou dobu. Pomocí výsledek operace uvnitř můžete zjistit stav školení.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publikování aplikace Language Understanding

Publikujte aplikaci LUIS pomocí metody [App. Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) . Tím se publikuje aktuální vyškolená verze v zadaném slotu na koncovém bodu. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakci entit.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Odstranění aplikace Language Understanding

Odstraňte aplikaci LUIS pomocí metody [App. deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) . Tím se odstraní aktuální aplikace.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Seznam aplikací pro porozumění jazyku

Získat seznam aplikací přidružených k klíči pro porozumění jazyku

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node luis_authoring_quickstart.js` v souboru rychlého startu.

```console
node luis_authoring_quickstart.js
```

Výstup z příkazového řádku aplikace je:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
