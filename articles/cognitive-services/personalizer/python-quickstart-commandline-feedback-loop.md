---
title: 'Rychlý Start: Klientská knihovna pro přizpůsobování pro Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Začínáme s klientskou knihovnou přizpůsobování pro Python pomocí výukové smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 02c4e0142ed7b3719cc07306f089769c532d6653
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494432"
---
# <a name="quickstart-personalizer-client-library-for-python"></a>Rychlý Start: Klientská knihovna pro přizpůsobování pro Python

Zobrazit přizpůsobený obsah v tomto rychlém startu Pythonu pomocí služby pro přizpůsobování

Začínáme s klientskou knihovnou přizpůsobování pro Python Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

 * Seřadit seznam akcí pro přizpůsobení.
 * Vykázat skóre odměňování, které indikuje úspěšnost horní seřazené akce.

[Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [ukázky](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3. x](https://www.python.org/)

## <a name="using-this-quickstart"></a>V tomto rychlém startu


V tomto rychlém startu se používá několik kroků:

* V Azure Portal vytvořte prostředek pro přizpůsobení.
* V Azure Portal u prostředku přizpůsobeného nástroji změňte na stránce **Konfigurace** četnost aktualizace modelu.
* V editoru kódu vytvořte soubor kódu a upravte soubor s kódem.
* V příkazovém řádku nebo terminálu nainstalujte sadu SDK z příkazového řádku.
* V příkazovém řádku nebo terminálu spusťte soubor kódu.


## <a name="create-a-personalizer-azure-resource"></a>Vytvoření prostředku Azure pro přizpůsobování

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro přizpůsobení pomocí [Azure Portal](https://portal.azure.com/) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Další podrobnosti najdete [v tématu Postup vytvoření prostředku Cognitive Services pomocí Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) . Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` klíče prostředku.
* `PERSONALIZER_ENDPOINT` pro koncový bod prostředku

V Azure Portal jsou hodnoty klíč a koncový bod k dispozici na stránce **rychlý Start** .


## <a name="install-the-python-library-for-personalizer"></a>Instalace knihovny Python pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V Azure Portal změňte v prostředku přizpůsobeném na stránce **Konfigurace** **Četnost aktualizace modelu** na 10 sekund. Tím se služba bude vytvářet rychle a umožní vám to zjistit, jak se hlavní akce mění pro každou iteraci.

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

Vytvořte novou aplikaci v Pythonu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí s názvem `sample.py`. 

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **Sample.py** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Vytvořte proměnné pro klíč Azure prostředku a koncový bod z proměnných prostředí s názvem `PERSONALIZER_RESOURCE_KEY` a `PERSONALIZER_RESOURCE_ENDPOINT`. Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

Název prostředku je součástí adresy URL koncového bodu: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, které má přizpůsobené přizpůsobovat. Přidejte následující metody pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-a-rank) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí, pro přizpůsobení obsahu, je následováno volání odměna, které přizpůsobuje, jak dobře se služba přiřadí k obsahu. 

Následující smyčka kódu projde cyklem, který uživateli požádá o své předvolby na příkazovém řádku, aby si tyto informace přihlásili k zařazení, a prezentuje seřazený výběr zákazníkovi, který si vybírá ze seznamu, a pak posílá odměnu přizpůsobenému nástroji. signalizace, jak dobře služba provedla řazení výběru.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Podívá se podrobněji na volání pořadí a odměny v následujících oddílech.

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* get_user_preference
* get_user_timeofday
* get_actions

## <a name="request-a-rank"></a>Vyžádat pořadí

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil `currentContent` možností obsahu. Proces může vytvořit obsah, který se má vyloučit z rozsahu, který je zobrazený jako `excludeActions`. Požadavek na řazení potřebuje akce, currentContext, excludeActions a jedinečné ID události klasifikace (jako identifikátor GUID), aby mohl přijímat seřazené odpovědi. 

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
* [Řešení potíží](troubleshooting.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
