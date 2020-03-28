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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372139"
---
Pomocí klientské knihovny pro vytváření jazyka Node.js použijte jazykovou knihovnu pro principy jazyka (LUIS) k použití:

* Vytvořte aplikaci.
* Přidejte záměry, entity a příklad projevy.
* Přidejte funkce, například seznam frází.
* Trénování a publikování aplikace.
* Odstranit aplikaci

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Authoring Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring),[Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js) [runtime balíčku (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | 

## <a name="prerequisites"></a>Požadavky

* Zdroj vytváření language understanding: [Vytvoření na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-starter-key"></a>Získejte počáteční klíč luis (Language Understanding" (LUIS)

Získejte [počáteční klíč](../luis-how-to-azure-subscription.md#starter-key) vytvořením vývojového prostředku LUIS. Udržujte klíč a koncový bod klíče pro další krok.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a oblasti pro klíč vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_AUTHORING_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `LUIS_AUTHORING_ENDPOINT`- Koncový bod přidružený k vašemu klíči.

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalace knihovny NPM pro vytváření služby LUIS

V adresáři aplikace nainstalujte závislosti pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Objektový model

Autorský klient jazyka (LUIS) je objekt [LUISAuthoringClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) který se ověřuje v Azure, který obsahuje váš klíč pro vytváření.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* Aplikace - [přidat](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [odstranit](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publikovat](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Příklad y utterances - [add by batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), delete by [ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funkce - správa [seznamů frází](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Model - správa [záměrů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) a entit
* Vzor - správa [vzorů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Vlak - [vlak](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) aplikace a anketa pro [stav školení](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - správa pomocí klonování, exportu a odstranění


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí knihovny klientů pro vytváření jazyka Pro Node.js(Luis):

* [Vytvoření aplikace](#create-a-luis-app)
* [Přidání entit](#create-entities-for-the-app)
* [Přidání záměrů](#create-intent-for-the-app)
* [Přidat ukázkové promluvy](#add-example-utterance-to-intent)
* [Trénování aplikace](#train-the-app)
* [Publikování aplikace](#publish-a-language-understanding-app)
* [Odstranění aplikace](#delete-a-language-understanding-app)
* [Seznam aplikací](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte nový textový soubor v upřednostňovaném `luis_authoring_quickstart.js`editoru nebo editoru IDE s názvem . Pak přidejte následující závislosti.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) s klíčem a použijte jej s koncovým bodem k vytvoření objektu [LUISAuthoringClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Vytvořte aplikaci LUIS, která bude obsahovat model zpracování přirozeného jazyka (NLP), který obsahuje záměry, entity a ukázkové projevy.

1. Vytvořte metodu [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objektu [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) k vytvoření aplikace. Název a jazyková verze jsou požadované vlastnosti.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Vytvoření záměru pro aplikaci
Primární objekt v modelu aplikace LUIS je záměr. Záměr zarovná se seskupí _záměry projevy_uživatele . Uživatel může položit otázku, nebo učinit prohlášení hledá konkrétní _zamýšlené_ odpovědi od robota (nebo jiné klientské aplikace). Příklady záměrů jsou rezervace letu, dotazování se na počasí v cílovém městě a dotazování se na kontaktní informace pro služby zákazníkům.

Použijte metodu [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) s názvem jedinečného záměru a pak předaj ID aplikace, ID verze a nový název záměru.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

Zatímco entity nejsou vyžadovány, nacházejí se ve většině aplikací. Entita extrahuje informace z promluvy uživatele, nezbytné k fullfil záměruživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) a vlastních entit, z nichž každý má vlastní modely objektu transformace dat (DTO).  Běžné předem vytvořené entity, které chcete přidat do aplikace, zahrnují [číslo](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinální](../luis-reference-prebuilt-ordinal.md).

Tato **metoda** add_entities `Location` vytvořila jednoduchou entitu se dvěma rolemi, jednoduchou `Class` entitou, složenou entitou `Flight` a přidala několik předem sestavených entit.

Je důležité vědět, že entity nejsou označeny záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkové projevy uživatelů jsou označeny pro konkrétní, jeden záměr.

Metody vytváření entit jsou součástí třídy [Model.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) Každý typ entity má svůj vlastní model objektu transformace dat (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Přidat ukázkový projev záměru

Chcete-li určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a měly by označit všechny vlastní entity. Předem sestavené entity není nutné označovat.

Přidejte ukázkové projevy vytvořením seznamu objektů [ExampleLabelObject,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) jednoho objektu pro každý příklad utterance. Každý příklad by měl označit všechny entity slovníkem dvojic název/hodnota názvu entity a hodnoty entity. Hodnota entity by měla být přesně tak, jak je uvedena v textu promluvy příkladu.

Volání [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) s ID aplikace, ID verze a seznam příkladů. Volání odpoví seznamem výsledků. Je třeba zkontrolovat výsledek každého příkladu a ujistěte se, že byl úspěšně přidán do modelu.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu aplikace LUIS musí být trénované pro tuto verzi modelu. Trénovaný model lze použít v [kontejneru](../luis-container-howto.md)nebo [publikovat](../luis-how-to-publish-app.md) do pracovních nebo produktových slotů.

Metoda [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) potřebuje ID aplikace a ID verze.

Velmi malý model, jako je tento rychlý start ukazuje, bude trénovat velmi rychle. Pro aplikace na úrovni produkčního prostředí by školení aplikace měla zahrnovat volání dotazování na metodu [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) k určení, kdy nebo zda školení proběhlo úspěšně. Odpověď je seznam [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) objekty se samostatným stavem pro každý objekt. Všechny objekty musí být úspěšné, aby školení bylo považováno za úplné.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Školení všech modelů nějakou dobu trvá. Pomocí operaceResult zkontrolujte stav školení.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publikování aplikace Language Understanding

Publikujte aplikaci LUIS pomocí metody [app.publish.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) To publikuje aktuální trénované verze do zadaného slotu v koncovém bodě. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakce entity.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Odstranění aplikace Language Understanding

Odstraňte aplikaci LUIS pomocí metody [app.deleteMethod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) Tím odstraníte aktuální aplikaci.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Seznam aplikací pro porozumění jazykům

Získání seznamu aplikací přidružených ke klíči Language understanding

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node luis_authoring_quickstart.js` v souboru quickstart.

```console
node luis_authoring_quickstart.js
```

Výstup příkazového řádku aplikace je:

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
