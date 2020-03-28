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
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372272"
---
Pomocí klientské knihovny předpovědi language understanding (LUIS) pro Python:

* Získejte předpověď podle slotu
* Získat předpověď podle verze

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [knihovny Predikce runtime balíčku (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [ ](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Požadavky

* Účet portálu language understanding (LUIS) – [vytvořte si účet zdarma](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-runtime-key"></a>Získejte klíč runtime jazyka (LUIS)

Získejte [klíč runtime](../luis-how-to-azure-subscription.md) vytvořením prostředku runtime LUIS. Udržujte klíč a koncový bod klíče pro další krok.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Vytvoření nového souboru pythonu

Vytvořte nový soubor pythonu v upřednostňovaném `prediction_quickstart.py`editoru nebo ide s názvem .

### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu runtime predikce jazyka (LANGUAGE Understanding) (LUIS) pro Python pomocí následujícího příkazu:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektový model

Klient runtime předpovědi jazyka (LUIS) je objekt [LUISRuntimeClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) který se ověřuje v Azure, který obsahuje klíč prostředků.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* Predikce [podle pracovního nebo produkčního slotu](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Predikce podle [verze](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s knihovnou runtime predikce jazyka (LUIS) pro Python:

* [Predikce podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu `prediction_quickstart.py` otevřete soubor v upřednostňovaném editoru nebo ide. Přidejte následující závislosti:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro vlastní požadované informace luis:

    Přidejte proměnné pro správu klíče předpověď `LUIS_RUNTIME_KEY`vytáhl z proměnné prostředí s názvem . Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou, která `LUIS_RUNTIME_ENDPOINT`bude obsahovat název prostředku .

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Vytvořte proměnnou pro ID aplikace `LUIS_APP_ID`jako proměnnou prostředí s názvem . Nastavte proměnnou prostředí na veřejnou **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** aplikaci IoT . Vytvořte proměnnou `production` pro nastavení publikované patice.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Vytvořte objekt pověření s klíčem a použijte ho s koncovým bodem khttps://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() vytvoření objektu [LUISRunTimeClientConfiguration].

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Získat předpověď z běhu

Přidejte následující metodu k vytvoření požadavku do běhu předpověď.

Projev uživatele je součástí [objektu prediction_request.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)

Metoda **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** potřebuje několik parametrů, jako je například ID aplikace, název patice a objekt požadavku předpověď ke splnění požadavku. Ostatní možnosti, jako je například podrobné, zobrazit všechny záměry a protokol jsou volitelné. Požadavek vrátí [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) objektu.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Použijte následující hlavní metodu k provázání proměnných a metod dohromady, abyste získali předpověď.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python prediction_quickstart.py` z adresáře aplikace.

```console
python prediction_quickstart.py
```

Konzola rychlého startu zobrazuje výstup:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení s vaše předpovědi, vyčistit práci z tohoto rychlého startu odstraněním souboru a jeho podadresáře.
