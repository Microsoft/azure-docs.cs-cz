---
title: 'Rychlý start: Klientská knihovna pro přizpůsobování pro Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Začínáme s klientskou knihovnou přizpůsobování pro Node. js s využitím výukové smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 49fa2cd53ebefa811673ea25a1851081f8cfa415
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958916"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>Rychlý start: Přizpůsobení klientské knihovny pro Node. js

Zobrazit přizpůsobený obsah v tomto rychlém startu Node. js pomocí služby pro přizpůsobování

Začínáme s klientskou knihovnou pro přizpůsobování pro Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

 * Seřadit seznam akcí pro přizpůsobení.
 * Vykázat skóre odměňování, které indikuje úspěšnost horní seřazené akce.

[](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) balíčku zdrojového kódu knihovny[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-personalizer-azure-resource"></a>Vytvoření prostředku Azure pro přizpůsobování

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro přizpůsobení pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`pro klíč prostředku.
* `PERSONALIZER_ENDPOINT`pro koncový bod prostředku.

V Azure Portal jsou hodnoty klíč a koncový bod k dispozici na stránce **rychlý Start** .


### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

`npm init -y` Spuštěním příkazu`package.json` vytvořte soubor. 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>Instalace knihovny Node. js pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Node. js pomocí následujícího příkazu:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Nainstalujte zbývající balíčky NPM pro tento rychlý Start:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V prostředku přizpůsobeného nástroji v Azure Portal změňte **Četnost aktualizace modelu** na 10 sekund. Tím se služba bude vytvářet rychle a umožní vám to zjistit, jak se hlavní akce mění pro každou iteraci.

![Změna frekvence aktualizace modelu](./media/settings/configure-model-update-frequency-settings.png)

Když se poprvé vytvoří instance smyčky pro přizpůsobení, neexistuje žádný model, protože neexistovala žádná Neplatní volání rozhraní API pro vlak. Volání Rank budou pro každou položku vracet stejné pravděpodobnosti. Vaše aplikace by měla stále vždy Seřadit obsah pomocí výstupu RewardActionId.

## <a name="object-model"></a>Objektový model

Klient pro přizpůsobování je objekt PersonalizerClient, který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Chcete-li se zeptat na rozměr obsahu, vytvořte RankRequest a pak ho předejte klientovi. Rank – metoda Metoda Rank vrátí RankResponse, který obsahuje seřazený obsah. 

Pokud chcete odeslat odměnu pro přizpůsobení, vytvořte RewardRequest a pak ho předejte klientovi. Metoda odměňování. 

Stanovení odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. To by mělo být jedno z primárních rozhodnutí o návrhu v architektuře pro přizpůsobování. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro přizpůsobování pro Node. js:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Vyžádat pořadí](#request-a-rank)
* [Poslat odměnu](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

Vytvořte novou aplikaci Node. js v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `sample.js`. 

## <a name="add-the-dependencies"></a>Přidat závislosti

Otevřete soubor **Sample. js** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `requires` pro přidání balíčků npm:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Vytvořte proměnné pro klíč a koncový bod prostředku Azure z proměnných prostředí s názvem `PERSONALIZER_KEY` a. `PERSONALIZER_ENDPOINT` Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey `PERSONALIZER_RESOURCE_KEY`je.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, které má přizpůsobené přizpůsobovat. Přidejte následující metody do třídy program pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-a-rank) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí, pro přizpůsobení obsahu, je následováno volání odměna, které přizpůsobuje, jak dobře se služba přiřadí k obsahu. 

Následující smyčka kódu projde cyklem, který požádá uživatele o své předvolby na příkazovém řádku, aby si tyto informace přihlásili k zařazení, a prezentuje seřazený výběr zákazníkovi, který si zvolí ze seznamu, a pak pošle odměnu do Přizpůsobené signalizace, jak dobře služba provedla řazení výběru.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

## <a name="request-a-rank"></a>Vyžádat pořadí

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z pořadí, zobrazené `excludeActions`jako. Požadavek na řazení potřebuje [Akce](concepts-features.md#actions-represent-a-list-of-options), CurrentContext, excludeActions a jedinečné ID události klasifikace (jako identifikátor GUID), aby mohl přijímat seřazené odpovědi. 

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](concept-feature-evaluation.md) [akcí a funkcí](concepts-features.md) netriviální.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu

Aby bylo možné dokončit požadavek na odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselné hodnoty každému výběru a pak pošle jedinečné ID události klasifikace a číselnou hodnotu k metodě odměňování.

V tomto rychlém startu se přiřadí jednoduché číslo jako odměna, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Node. js z adresáře aplikace.

```console
node sample.js
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Jak přizpůsobovat práci](how-personalizer-works.md)

* [Co je přizpůsobování?](what-is-personalizer.md)
* [Kde můžete použít přizpůsobování?](where-can-you-use-personalizer.md)
* [Odstraňování potíží](troubleshooting.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)
