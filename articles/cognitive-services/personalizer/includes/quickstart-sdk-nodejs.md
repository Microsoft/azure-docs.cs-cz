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
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 237ba5ba390b4065a67f29611fbd43375c239578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188848"
---
[Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Ukázky](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | knihovny Referenční dokumentace[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org) a npm.

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

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalace knihovny Node. js pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Node. js pomocí následujícího příkazu:

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

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro přizpůsobování pro Node. js:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte novou aplikaci Node. js v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `sample.js`.

## <a name="add-the-dependencies"></a>Přidat závislosti

Otevřete soubor **Sample. js** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `requires` pro přidání balíčků npm:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Vytvořte proměnné pro klíč a koncový bod prostředku Azure z proměnných prostředí s názvem `PERSONALIZER_KEY` a. `PERSONALIZER_ENDPOINT` Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživateli požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, která prezentuje výběr zákazníkovi, který si zvolí ze seznamu, a pak pošle do přizpůsobené signalizaci, jak dobře služba vybrala.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Požádat o nejlepší akci

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které `excludeActions`jsou zobrazené jako. Požadavek na řazení potřebuje [Akce](../concepts-features.md#actions-represent-a-list-of-options) a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události klasifikace, aby mohl přijímat seřazené odpovědi.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu


Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Node. js z adresáře aplikace.

```console
node sample.js
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
