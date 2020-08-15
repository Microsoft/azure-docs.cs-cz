---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file, devx-track-javascript
ms.date: 07/30/2020
ms.openlocfilehash: ba7885859adc1d9899c66917204306c8a0d0092f
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246227"
---
[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)  | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org) a npm.

## <a name="using-this-quickstart"></a>V tomto rychlém startu


V tomto rychlém startu se používá několik kroků:

* V Azure Portal vytvořte prostředek pro přizpůsobení.
* V Azure Portal u prostředku přizpůsobeného nástroji změňte na stránce **Konfigurace** četnost aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor s kódem.
* V příkazovém řádku nebo terminálu nainstalujte sadu SDK z příkazového řádku.
* V příkazovém řádku nebo terminálu spusťte soubor kódu.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init -y` příkazu vytvořte `package.json` soubor.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalace knihovny Node.js pro přizpůsobování

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

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte novou aplikaci Node.js v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `sample.js` .

## <a name="add-the-dependencies"></a>Přidat závislosti

Otevřete soubor **sample.js** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `requires` pro přidání balíčků npm:

[!code-javascript[Add module dependencies](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Upravte klíč a proměnné koncového bodu směrem k hornímu okraji souboru kódu pro klíč a koncový bod prostředku Azure. 

[!code-javascript[Add Personalizer resource information](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY` .

[!code-javascript[Create a Personalizer client](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí.

[!code-javascript[Create user features](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživateli požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, která prezentuje výběr zákazníkovi, který si zvolí ze seznamu, a pak pošle do přizpůsobené signalizaci, jak dobře služba vybrala.

[!code-javascript[Create the learning loop](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=mainLoop)]

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Požádat o nejlepší akci

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které jsou zobrazené jako `excludeActions` . Požadavek na řazení potřebuje [Akce](../concepts-features.md#actions-represent-a-list-of-options) a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události klasifikace, aby mohl přijímat seřazené odpovědi.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

[!code-javascript[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu


Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

[!code-javascript[The Personalizer learning loop sends a reward.](~/cognitive-services-quickstart-code/javascript/Personalizer/sample.js?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Node.js z adresáře aplikace.

```console
node sample.js
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
