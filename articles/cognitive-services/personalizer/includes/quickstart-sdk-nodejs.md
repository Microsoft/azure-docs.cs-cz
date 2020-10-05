---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/27/2020
ms.openlocfilehash: 03680a2a6b4792a2bf522eff1462e29439e0f61b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055370"
---
[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org) a npm.
* Jakmile budete mít předplatné Azure, vytvořte prostředek přizpůsobeného přizpůsobeného <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" "  target="_blank"> prostředku <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro přizpůsobení budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init -y` příkazu vytvořte `package.json` soubor.

```console
npm init -y
```

Vytvořte novou aplikaci Node.js v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) `sample.js` a vytvořte proměnné pro koncový bod prostředku a klíč předplatného. 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const uuidv1 = require('uuid/v1');
const Personalizer = require('@azure/cognitiveservices-personalizer');
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const readline = require('readline-sync');

// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const serviceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";

// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const baseUri = "https://<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>.cognitiveservices.azure.com";
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Instalace knihovny Node.js pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Node.js pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Nainstalujte zbývající balíčky NPM pro tento rychlý Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektový model

Klient pro přizpůsobování je objekt [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Pokud chcete požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)a pak ji předejte [klientovi. Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) – metoda Metoda Rank vrátí RankResponse.

Chcete-li odeslat odměnu do přizpůsobeného modulu, vytvořte [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)a předejte ji do metody [odměňování](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) pro třídu Events.

Stanovení odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](../concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. To by mělo být jedno z primárních rozhodnutí o návrhu v architektuře pro přizpůsobování.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro přizpůsobování pro Node.js:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#authenticate-the-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `PersonalizerClient` s a, `serviceKey` kterou `baseUri` jste vytvořili dříve.

```javascript
const credentials = new CognitiveServicesCredentials(serviceKey);

// Initialize Personalization client.
const personalizerClient = new Personalizer.PersonalizerClient(credentials, baseUri);
```

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí.

```javascript
function getContextFeaturesList() {
  const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
  const tasteFeatures = ['salty', 'sweet'];

  let answer = readline.question("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n");
  let selection = parseInt(answer);
  const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

  answer = readline.question("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet\n");
  selection = parseInt(answer);
  const taste = selection >= 1 && selection <= 2 ? tasteFeatures[selection - 1] : tasteFeatures[0];

  console.log("Selected features:\n");
  console.log("Time of day: " + timeOfDay + "\n");
  console.log("Taste: " + taste + "\n");

  return [
    {
      "time": timeOfDay
    },
    {
      "taste": taste
    }
  ];
}
```

```javascript
function getActionsList() {
  return [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ];
}
```

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživateli požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, která prezentuje výběr zákazníkovi, který si zvolí ze seznamu, a pak pošle do přizpůsobené signalizaci, jak dobře služba vybrala.

```javascript
let runLoop = true;

do {

  let rankRequest = {}

  // Generate an ID to associate with the request.
  rankRequest.eventId = uuidv1();

  // Get context information from the user.
  rankRequest.contextFeatures = getContextFeaturesList();

  // Get the actions list to choose from personalization with their features.
  rankRequest.actions = getActionsList();

  // Exclude an action for personalization ranking. This action will be held at its current position.
  rankRequest.excludedActions = getExcludedActionsList();

  rankRequest.deferActivation = false;

  // Rank the actions
  const rankResponse = await personalizerClient.rank(rankRequest);

  console.log("\nPersonalization service thinks you would like to have:\n")
  console.log(rankResponse.rewardActionId);

  // Display top choice to user, user agrees or disagrees with top choice
  const reward = getReward();

  console.log("\nPersonalization service ranked the actions with the probabilities as below:\n");
  for (let i = 0; i < rankResponse.ranking.length; i++) {
    console.log(JSON.stringify(rankResponse.ranking[i]) + "\n");
  }

  // Send the reward for the action based on user response.

  const rewardRequest = {
    value: reward
  }

  await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);

  runLoop = continueLoop();

} while (runLoop);
```

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Požádat o nejlepší akci

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které jsou zobrazené jako `excludeActions` . Požadavek na řazení potřebuje [Akce](../concepts-features.md#actions-represent-a-list-of-options) a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události klasifikace, aby mohl přijímat seřazené odpovědi.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

```javascript
let rankRequest = {}

// Generate an ID to associate with the request.
rankRequest.eventId = uuidv1();

// Get context information from the user.
rankRequest.contextFeatures = getContextFeaturesList();

// Get the actions list to choose from personalization with their features.
rankRequest.actions = getActionsList();

// Exclude an action for personalization ranking. This action will be held at its current position.
rankRequest.excludedActions = getExcludedActionsList();

rankRequest.deferActivation = false;

// Rank the actions
const rankResponse = await personalizerClient.rank(rankRequest);
```

## <a name="send-a-reward"></a>Poslat odměnu

Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

```javascript
const rewardRequest = {
  value: reward
}

await personalizerClient.events.reward(rankRequest.eventId, rewardRequest);
```

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Node.js z adresáře aplikace.

```console
node sample.js
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
