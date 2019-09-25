---
title: 'Rychlý start: Klientská knihovna pro přizpůsobování pro Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Začínáme s klientskou knihovnou přizpůsobování pro Python pomocí výukové smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 014a5f264b9beed666f718cda52d197381d58876
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266252"
---
# <a name="quickstart-personalize-client-library-for-python"></a>Rychlý start: Přizpůsobení klientské knihovny pro Python

Zobrazit přizpůsobený obsah v tomto rychlém startu Pythonu pomocí služby pro přizpůsobování

Začínáme s klientskou knihovnou přizpůsobování pro Python Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

 * Seřadit seznam akcí pro přizpůsobení.
 * Vykázat skóre odměňování, které indikuje úspěšnost horní seřazené akce.

[Ukázky balíčku (PyPi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-personalizer-azure-resource"></a>Vytvoření prostředku Azure pro přizpůsobování

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro přizpůsobení pomocí [Azure Portal](https://portal.azure.com/) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Další podrobnosti najdete [v tématu Postup vytvoření prostředku Cognitive Services pomocí Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) . Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY`pro klíč prostředku.
* `PERSONALIZER_ENDPOINT`pro koncový bod prostředku.

V Azure Portal jsou hodnoty klíč a koncový bod k dispozici na stránce **rychlý Start** .


### <a name="install-the-python-library-for-personalizer"></a>Instalace knihovny Python pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-personalizer
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

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

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro přizpůsobování pro Python:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Vyžádat pořadí](#request-a-rank)
* [Poslat odměnu](#send-a-reward)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci Python v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `sample.py`. 

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **program.cs** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující `using` kód následujícími `using` direktivami:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Ve třídě **program** vytvořte proměnné pro klíč Azure prostředku a koncový bod získaný z proměnných prostředí s názvem `PERSONALIZER_RESOURCE_KEY` a. `PERSONALIZER_RESOURCE_ENDPOINT` Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey `PERSONALIZER_RESOURCE_KEY`je.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, které má přizpůsobené přizpůsobovat. Přidejte následující metody do třídy program pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-a-rank) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí, pro přizpůsobení obsahu, je následováno volání odměna, které přizpůsobuje, jak dobře se služba přiřadí k obsahu. 

Následující kód v `main` metodě programu se cyklicky vychází z cyklu dotazování na předvolby uživatele na příkazovém řádku, který odešle tyto informace do přizpůsobeného pořadí a prezentuje vybranému výběru zákazníkovi, aby si mohl vybrat z Seznamte se s tím, že do přizpůsobeného signálu přizpůsobíte, jak dobře služba provedla řazení výběru.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Vyžádat pořadí

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby `currentContent` vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z pořadí, zobrazené `excludeActions`jako. Požadavek na řazení potřebuje akce, currentContext, excludeActions a jedinečné ID události klasifikace (jako identifikátor GUID), aby mohl přijímat seřazené odpovědi. 

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](concept-feature-evaluation.md) [akcí a funkcí](concepts-features.md) netriviální.  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu

Aby bylo možné dokončit požadavek na odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselné hodnoty každému výběru a pak pošle jedinečné ID události klasifikace a číselnou hodnotu k metodě odměňování.

V tomto rychlém startu se přiřadí jednoduché číslo jako odměna, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy. 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Pythonem z adresáře vaší aplikace.

```console
python sample.py
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
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)
