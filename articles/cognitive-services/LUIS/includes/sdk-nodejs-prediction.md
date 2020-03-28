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
ms.openlocfilehash: 05e668ff5b0ec19c5e380cf6bfee4b6e46900b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371857"
---
Pomocí runtime klientské knihovny Jazyka (LANGUAGE Understanding) pro soubor Node.js můžete:

* Predikce podle slotu
* Predikce podle verze

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [knihovny](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js) [Runtime Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | 

## <a name="prerequisites"></a>Požadavky

* Prostředek runtime language understanding: [Vytvoření na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-runtime-key"></a>Získejte klíč runtime jazyka (LUIS)

Získejte [klíč runtime](../luis-how-to-azure-subscription.md) vytvořením prostředku runtime LUIS. Udržujte klíč a koncový bod klíče pro další krok.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Vytvoření nového souboru javascriptu (Node.js)

Vytvořte nový soubor javascriptu v preferovaném editoru nebo ide s názvem `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instalace knihovny NPM pro zaběhu LUIS

V adresáři aplikace nainstalujte závislosti pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektový model

Autorský klient jazyka (LUIS) je objekt [LUISAuthoringClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) který se ověřuje v Azure, který obsahuje váš klíč pro vytváření.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* [Predikce](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) podle `staging` nebo `production` slot
* [Predikce podle verze](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s knihovnou runtime predikce jazyka (LUIS):

* [Predikce podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu `luis_prediction.js` otevřete soubor v upřednostňovaném editoru nebo ide. Přidejte následující závislosti:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro vlastní požadované informace luis:

    Přidejte proměnné pro správu klíče předpověď `LUIS_RUNTIME_KEY`vytáhl z proměnné prostředí s názvem . Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou, která `LUIS_RUNTIME_ENDPOINT`bude obsahovat název prostředku .

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Vytvořte proměnnou pro ID aplikace `LUIS_APP_ID`jako proměnnou prostředí s názvem . Nastavte proměnnou prostředí na veřejnou **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** aplikaci IoT . Vytvořte proměnnou `production` pro nastavení publikované patice.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Vytvořte objekt msRest.ApiKeyCredentials s klíčem a použijte jej s koncovým bodem k vytvoření [služby LUIS. Objekt SLUŽBY LUISRunTimeClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Získat předpověď z běhu

Přidejte následující metodu k vytvoření požadavku do běhu předpověď.

Projev uživatele je součástí [objektu predictionRequest.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest)

**[LuisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** metoda potřebuje několik parametrů, jako je například ID aplikace, název patice a objekt požadavku předpověď ke splnění požadavku. Ostatní možnosti, jako je například podrobné, zobrazit všechny záměry a protokol jsou volitelné.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Použijte následující hlavní metodu k provázání proměnných a metod dohromady, abyste získali předpověď.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node luis_prediction.js` z adresáře aplikace.

```console
node luis_prediction.js
```

Výsledek předpověď vrátí JSON objekt:

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

Po dokončení s vaše předpovědi, vyčistit práci z tohoto rychlého startu odstraněním souboru a jeho podadresáře.
