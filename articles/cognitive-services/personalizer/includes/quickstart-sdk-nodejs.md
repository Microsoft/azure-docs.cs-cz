---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122877"
---
[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [knihovny](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [Node.js](https://nodejs.org) a NPM.

## <a name="using-this-quickstart"></a>Pomocí tohoto rychlého startu


Tento rychlý start lze použít několika kroky:

* Na webu Azure Portal vytvořte prostředek personalisty
* Na webu Azure Portal pro prostředek personalikátoru na stránce **Konfigurace** změňte frekvenci aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor kódu
* V příkazovém řádku nebo terminálu nainstalujte sdk z příkazového řádku
* V příkazovém řádku nebo terminálu spusťte soubor kódu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spusťte `npm init -y` příkaz `package.json` a vytvořte soubor.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalace knihovny Node.js pro personalizátor

Nainstalujte klientskou knihovnu personalisty pro soubor Node.js pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Nainstalujte zbývající balíčky NPM pro tento rychlý start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Objektový model

Klient Personalista je objekt [PersonalistaClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) který se ověřuje v Azure pomocí Microsoft.Rest.ServiceClientCredentials, který obsahuje váš klíč.

Chcete-li požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)a předejte jej [klientovi. Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) metoda. Rank Metoda vrátí RankResponse.

Chcete-li odeslat odměnu personalistovi, vytvořte [požadavek na odměnu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)a poté ji předejte metodě [Odměna](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) ve třídě Události.

Určení odměny, v tomto rychlém startu je triviální. Ve výrobním systému, určení toho, co má vliv na [skóre odměny](../concept-rewards.md) a o kolik může být složitý proces, který se můžete rozhodnout změnit v průběhu času. To by mělo být jedním z primárních rozhodnutí o návrhu v architektuře personalisty.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny Personalizátor pro Soubor Node.js:

* [Vytvoření klienta personalisty](#create-a-personalizer-client)
* [Pořadí API](#request-the-best-action)
* [Rozhraní API pro odměny](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte novou aplikaci Node.js v upřednostňovaném editoru nebo editoru IDE s názvem `sample.js`.

## <a name="add-the-dependencies"></a>Přidání závislostí

Otevřete soubor **sample.js** v upřednostňovaném editoru nebo rozhraní IDE. Přidejte `requires` následující balíčky NPM:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidání informací o prostředcích personalisty

Vytvořte proměnné pro klíč Azure a koncový bod vašeho prostředku, `PERSONALIZER_KEY` `PERSONALIZER_ENDPOINT`který je vytažen z proměnných prostředí s názvem a . Pokud jste vytvořili proměnné prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta personalisty

Dále vytvořte metodu pro vrácení klienta personalisty. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat volby obsahu reprezentované jako akce

Akce představují volby obsahu, ze kterých má personalikátor vybrat nejlepší položku obsahu. Přidejte do třídy Program následující metody, které představují sadu akcí a jejich funkcí.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Učební smyčka personalisty je cyklus volání [hodnosti](#request-the-best-action) a [odměny.](#send-a-reward) V tomto rychlém startu následuje každé volání hodnosti pro přizpůsobení obsahu po výzvě odměny, která personalistovi sdělí, jak si služba vedla.

Následující kód smyčky přes cyklus dotazování uživatele jejich preference na příkazovém řádku, odesílání těchto informací personalista vybrat nejlepší akci, předložení výběru zákazníkovi vybrat ze seznamu, pak odeslání odměny na Personalizátor signalizace, jak dobře služba udělala ve svém výběru.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Podívejte se blíže na pořadí a výzvy k odměňování v následujících sekcích.

Před spuštěním souboru kódu přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions):

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Vyžádejte si nejlepší akci

Chcete-li dokončit požadavek pořadí, program požádá uživatele předvolby k vytvoření obsahu volby. Proces může vytvořit obsah, který má `excludeActions`být vyloučen z akcí, zobrazený jako . Rank požadavek potřebuje [akce](../concepts-features.md#actions-represent-a-list-of-options) a jejich funkce, currentContext funkce, excludeActions a jedinečné pořadí id události, přijímat seřazené odpovědi.

Tento rychlý start má jednoduché kontextové vlastnosti denní doby a preference uživatelského jídla. Ve výrobních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální záležitostí.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu


Chcete-li získat skóre odměny k odeslání žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí výběru číselnou hodnotu a poté odešle jedinečné ID události a skóre odměny jako číselnou hodnotu do rozhraní API odměny.

Tento rychlý start přiřadí jednoduché číslo jako skóre odměny, buď nulu nebo 1. Ve výrobních systémech může být určení, kdy a co poslat na [výzvu Odměna,](../concept-rewards.md) netriviální záležitostí v závislosti na vašich konkrétních potřebách.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci pomocí souboru Node.js z adresáře aplikace.

```console
node sample.js
```

![Program rychlého startu se zeptá na několik otázek, aby shromáždil uživatelské předvolby, známé jako funkce, a poté poskytuje nejvyšší akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
