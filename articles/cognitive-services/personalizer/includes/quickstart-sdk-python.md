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
ms.openlocfilehash: d35f11125809167c4834b33f913d9369a6402ac0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188849"
---
[Reference documentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | knihovny Referenční dokumentace[(PyPi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>V tomto rychlém startu


V tomto rychlém startu se používá několik kroků:

* V Azure Portal vytvořte prostředek pro přizpůsobení.
* V Azure Portal u prostředku přizpůsobeného nástroji změňte na stránce **Konfigurace** četnost aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor s kódem.
* V příkazovém řádku nebo terminálu nainstalujte sadu SDK z příkazového řádku.
* V příkazovém řádku nebo terminálu spusťte soubor kódu.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instalace knihovny Python pro přizpůsobování

Nainstalujte klientskou knihovnu pro přizpůsobování pro Python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objektový model

Klient pro přizpůsobování je objekt [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Pokud chcete požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)a pak ji předejte klientovi. Rank – metoda Metoda Rank vrátí RankResponse.

Chcete-li odeslat skóre odměna do přizpůsobeného, nastavte ID události a skóre (hodnota) pro odeslání na metodu [odměňování](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) do třídy EventOperations.

Stanovení odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](../concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. To by mělo být jedno z primárních rozhodnutí o návrhu v architektuře pro přizpůsobování.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro přizpůsobování pro Python:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci Python v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `sample.py`.

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **Sample.py** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Vytvořte proměnné pro klíč a koncový bod prostředku Azure z proměnných prostředí s názvem `PERSONALIZER_RESOURCE_KEY` a. `PERSONALIZER_RESOURCE_ENDPOINT` Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

Název prostředku je součástí adresy URL koncového bodu: `https://<your-resource-name>.api.cognitive.microsoft.com/`.

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat možnosti obsahu reprezentované jako akce

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživateli požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, která prezentuje výběr zákazníkovi, který si zvolí ze seznamu, a pak pošle do přizpůsobené signalizaci, jak dobře služba vybrala.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions)před spuštěním souboru kódu:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Požádat o nejlepší akci


Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby `currentContent` vytvořil výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které `excludeActions`jsou zobrazené jako. Požadavek na řazení potřebuje pro příjem odpovědi akce a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu


Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s Pythonem z adresáře vaší aplikace.

```console
python sample.py
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)