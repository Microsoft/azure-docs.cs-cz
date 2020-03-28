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
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122919"
---
[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [knihovny (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Samples](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>Pomocí tohoto rychlého startu


Tento rychlý start lze použít několika kroky:

* Na webu Azure Portal vytvořte prostředek personalisty
* Na webu Azure Portal pro prostředek personalikátoru na stránce **Konfigurace** změňte frekvenci aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor kódu
* V příkazovém řádku nebo terminálu nainstalujte sdk z příkazového řádku
* V příkazovém řádku nebo terminálu spusťte soubor kódu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>Instalace knihovny Pythonu pro personalikára

Nainstalujte klientskou knihovnu Personalisty pro Python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>Objektový model

Klient Personalista je objekt [PersonalistaClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) který se ověřuje v Azure pomocí Microsoft.Rest.ServiceClientCredentials, který obsahuje váš klíč.

Chcete-li požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)a předejte jej klientovi. Rank metoda. Rank Metoda vrátí RankResponse.

Chcete-li odeslat skóre odměny personalistovi, nastavte ID události a skóre odměny (hodnotu) pro odeslání metodě [Odměny](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) ve třídě EventOperations.

Určení odměny, v tomto rychlém startu je triviální. Ve výrobním systému, určení toho, co má vliv na [skóre odměny](../concept-rewards.md) a o kolik může být složitý proces, který se můžete rozhodnout změnit v průběhu času. To by mělo být jedním z primárních rozhodnutí o návrhu v architektuře personalisty.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Personalizátor pro Python:

* [Vytvoření klienta personalisty](#create-a-personalizer-client)
* [Pořadí API](#request-the-best-action)
* [Rozhraní API pro odměny](#send-a-reward)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace pythonu

Vytvořte novou aplikaci Pythonu ve `sample.py`vašem preferovaném editoru nebo ide s názvem .

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu otevřete **soubor sample.py** v upřednostňovaném editoru nebo rozhraní IDE. Přidejte následující:

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidání informací o prostředcích personalisty

Vytvořte proměnné pro klíč Azure a koncový bod vašeho prostředku, `PERSONALIZER_RESOURCE_KEY` `PERSONALIZER_RESOURCE_ENDPOINT`který je vytažen z proměnných prostředí s názvem a . Pokud jste vytvořili proměnné prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

Název prostředku je součástí adresy URL `https://<your-resource-name>.api.cognitive.microsoft.com/`koncového bodu: .

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta personalisty

Dále vytvořte metodu pro vrácení klienta personalisty. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Získat volby obsahu reprezentované jako akce

Akce představují volby obsahu, ze kterých má personalikátor vybrat nejlepší položku obsahu. Přidejte do třídy Program následující metody, které představují sadu akcí a jejich funkcí.

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Učební smyčka personalisty je cyklus volání [hodnosti](#request-the-best-action) a [odměny.](#send-a-reward) V tomto rychlém startu každé volání hodnosti pro přizpůsobení obsahu následuje výzva k odměňování, která personalistovi řekne, jak dobře si služba vedla.

Následující kód smyčky přes cyklus dotazování uživatele jejich preference na příkazovém řádku, odesílání těchto informací personalista vybrat nejlepší akci, předložení výběru zákazníkovi vybrat ze seznamu, pak odeslání odměny na Personalizátor signalizace, jak dobře služba udělala ve svém výběru.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

Před spuštěním souboru kódu přidejte následující metody, které [získají volby obsahu](#get-content-choices-represented-as-actions):

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>Vyžádejte si nejlepší akci


Chcete-li dokončit požadavek na pořadí, program požádá `currentContent` uživatele o předvolby k vytvoření volby obsahu. Proces může vytvořit obsah, který má `excludeActions`být vyloučen z akcí, zobrazený jako . Rank požadavek potřebuje akce a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události, přijímat odpovědi.

Tento rychlý start má jednoduché kontextové vlastnosti denní doby a preference uživatelského jídla. Ve výrobních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální záležitostí.

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu


Chcete-li získat skóre odměny k odeslání žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí výběru číselnou hodnotu a poté odešle jedinečné ID události a skóre odměny jako číselnou hodnotu do rozhraní API odměny.

Tento rychlý start přiřadí jednoduché číslo jako skóre odměny, buď nulu nebo 1. Ve výrobních systémech může být určení, kdy a co poslat na [výzvu Odměna,](../concept-rewards.md) netriviální záležitostí v závislosti na vašich konkrétních potřebách.

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci s pythonem z adresáře aplikace.

```console
python sample.py
```

![Program rychlého startu se zeptá na několik otázek, aby shromáždil uživatelské předvolby, známé jako funkce, a poté poskytuje nejvyšší akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)