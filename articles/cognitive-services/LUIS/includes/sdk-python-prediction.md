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
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372272"
---
Pro Python použijte Language Understanding (LUIS) předvídání klientské knihovny pro:

* Získat předpovědi podle slotu
* Získat předpovědi podle verze

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [prediktivního běhového balíčku (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Požadavky

* Účet portálu Language Understanding (LUIS) – [Vytvořte si ho zdarma](https://www.luis.ai) .
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-runtime-key"></a>Získat klíč běhu Language Understanding (LUIS)

Získejte [klíč za běhu](../luis-how-to-azure-subscription.md) vytvořením prostředku modulu runtime Luis. Zachovejte klíč a koncový bod klíče pro další krok.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Vytvořit nový soubor Pythonu

Vytvořte nový soubor Pythonu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE) s názvem `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu modulu runtime pro Language Understanding (LUIS) pro Python pomocí následujícího příkazu:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektový model

Běhový klient pro modul runtime pro Language Understanding (LUIS) je objekt [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) , který se ověřuje v Azure, který obsahuje váš klíč prostředku.

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* Předpověď podle [přípravného nebo produkčního slotu](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Předpověď podle [verze](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro Language Understanding (LUIS) předpovědi modulu runtime pro Python:

* [Předpověď podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor `prediction_quickstart.py` v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující závislosti:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro vlastní požadované informace LUIS:

    Přidejte proměnné pro správu vašeho klíč předpovědi načtený z proměnné prostředí s názvem `LUIS_RUNTIME_KEY`. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou pro uložení názvu prostředku `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Vytvořte proměnnou pro ID aplikace jako proměnnou prostředí s názvem `LUIS_APP_ID`. Nastavte proměnnou prostředí na veřejnou aplikaci IoT **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Vytvořte proměnnou pro nastavení `production` publikovaný slot.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Vytvořte objekt přihlašovacích údajů s vaším klíčem a použijte ho u svého koncového bodu k vytvoření objektu pro vytváření https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() [LUISRuntimeClientConfiguration].

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Získat předpovědi z modulu runtime

Přidejte následující metodu pro vytvoření žádosti pro modul runtime předpovědi.

Uživatel utterance je součástí objektu [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) .

Metoda **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** potřebuje několik parametrů, jako je ID aplikace, název slotu a objekt žádosti o předpověď splnění žádosti. Další možnosti jako verbose, Zobrazit všechny záměry a protokol jsou volitelné. Požadavek vrátí objekt [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) .

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Pomocí následující metody Main spojíte proměnné a metody dohromady a získáte předpověď.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python prediction_quickstart.py` z adresáře aplikace.

```console
python prediction_quickstart.py
```

V konzole pro rychlé zprovoznění se zobrazí výstup:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s předpovědi hotovi, vyčistěte práci z tohoto rychlého startu tak, že odstraníte soubor a jeho podadresáře.
