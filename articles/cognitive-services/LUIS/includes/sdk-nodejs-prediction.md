---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 0539f4a8080056f96319a7a75a355782ee80018d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772411"
---
Použijte klientskou knihovnu modulu runtime Language Understanding (LUIS) pro Node. js na:

* Předpověď podle slotu
* Předpověď podle verze

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [běhového balíčku (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Požadavky

* Language Understanding prostředek modulu runtime: [vytvořte ho v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-runtime-key"></a>Získat klíč běhu Language Understanding (LUIS)

Získejte [klíč za běhu](../luis-how-to-azure-subscription.md) vytvořením prostředku modulu runtime Luis. Zachovejte klíč a koncový bod klíče pro další krok.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Vytvořit nový soubor JavaScriptu (Node. js)

Vytvořte nový soubor JavaScriptu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instalace knihovny NPM pro modul runtime LUIS

V adresáři aplikace nainstalujte závislosti pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektový model

Klient pro vytváření Language Understanding (LUIS) je objekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) , který se ověřuje v Azure, který obsahuje váš klíč pro vytváření obsahu.

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* [Předpověď](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) podle `staging` nebo `production` slotu
* [Předpověď podle verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Language Understanding (LUIS) předpovědi modulu runtime:

* [Předpověď podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor `luis_prediction.js` v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující závislosti:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro vlastní požadované informace LUIS:

    Přidejte proměnné pro správu vašeho klíč předpovědi načtený z proměnné prostředí s názvem `LUIS_RUNTIME_KEY`. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou pro uložení názvu prostředku `LUIS_RUNTIME_ENDPOINT`.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Vytvořte proměnnou pro ID aplikace jako proměnnou prostředí s názvem `LUIS_APP_ID`. Nastavte proměnnou prostředí na veřejnou aplikaci IoT **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Vytvořte proměnnou pro nastavení `production` publikovaný slot.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Vytvořte objekt msRest. ApiKeyCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření [Luis. Objekt LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Získat předpovědi z modulu runtime

Přidejte následující metodu pro vytvoření žádosti pro modul runtime předpovědi.

Uživatel utterance je součástí objektu [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) .

Metoda **[luisRuntimeClient. předpověď. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** potřebuje několik parametrů, jako je ID aplikace, název slotu a objekt žádosti o předpověď, který požadavek splní. Další možnosti jako verbose, Zobrazit všechny záměry a protokol jsou volitelné.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Pomocí následující metody Main spojíte proměnné a metody dohromady a získáte předpověď.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node luis_prediction.js` z adresáře aplikace.

```console
node luis_prediction.js
```

Výsledek předpovědi vrátí objekt JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s předpovědi hotovi, vyčistěte práci z tohoto rychlého startu tak, že odstraníte soubor a jeho podadresáře.
