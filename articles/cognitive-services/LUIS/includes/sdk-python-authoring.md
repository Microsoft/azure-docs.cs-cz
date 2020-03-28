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
ms.openlocfilehash: 631185c20b816191530158fab2b7cd1ed68c3092
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372055"
---
Pomocí klientské knihovny pro vytváření jazyka (Language Understanding (LUIS) pro Python:

* Vytvořte aplikaci.
* Přidejte záměry, entity a příklad projevy.
* Přidejte funkce, například seznam frází.
* Trénování a publikování aplikace.

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py) [zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [knihovny (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | 

## <a name="prerequisites"></a>Požadavky

* Účet portálu language understanding (LUIS): [Vytvořte si jeden zdarma](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-starter-key"></a>Získejte počáteční klíč luis (Language Understanding" (LUIS)

Získejte [počáteční klíč](../luis-how-to-azure-subscription.md#starter-key) vytvořením vývojového prostředku LUIS. Udržujte klíč a oblast klíče pro další krok.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a oblasti pro klíč vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_AUTHORING_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `LUIS_REGION`- Oblast spojená s vaším klíčem. Například `westus`.

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="install-the-python-library-for-luis"></a>Instalace knihovny Pythonu pro službu LUIS

V adresáři aplikace nainstalujte klientskou knihovnu pro vytváření jazyka (Language Understanding ( LUIS) pro python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektový model

Autorský klient jazyka (LUIS) je objekt [LUISAuthoringClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) který se ověřuje v Azure, který obsahuje váš klíč pro vytváření.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* Aplikace - [vytvořit](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [odstranit](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publikovat](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Příklad y utterances - [add by batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), delete by [ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Funkce - správa [seznamů frází](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Model - správa [záměrů](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) a entit
* Vzor - správa [vzorů](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Vlak - [vlak](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) aplikace a anketa pro [stav školení](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Verze](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) - správa pomocí klonování, exportu a odstranění


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí jazykové znalosti (LUIS) vytváření klientské knihovny pro python:

* [Vytvoření aplikace](#create-a-luis-app)
* [Přidání entit](#create-entities-for-the-app)
* [Přidání záměrů](#create-intent-for-the-app)
* [Přidat ukázkové promluvy](#add-example-utterance-to-intent)
* [Trénování aplikace](#train-the-app)
* [Publikování aplikace](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace pythonu

Vytvořte novou aplikaci Pythonu ve vašem preferovaném editoru nebo IDE. Pak importujte následující knihovny.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte jej s koncovým bodem k vytvoření objektu [LUISAuthoringClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Vytvořte aplikaci LUIS, která bude obsahovat model zpracování přirozeného jazyka (NLP), který obsahuje záměry, entity a ukázkové projevy.

1. Vytvořte metodu [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) objektu [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) k vytvoření aplikace. Název a jazyková verze jsou požadované vlastnosti.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Vytvoření záměru pro aplikaci
Primární objekt v modelu aplikace LUIS je záměr. Záměr zarovná se seskupí _záměry projevy_uživatele . Uživatel může položit otázku, nebo učinit prohlášení hledá konkrétní _zamýšlené_ odpovědi od robota (nebo jiné klientské aplikace). Příklady záměrů jsou rezervace letu, dotazování se na počasí v cílovém městě a dotazování se na kontaktní informace pro služby zákazníkům.

Použijte metodu [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) s názvem jedinečného záměru a pak předaj ID aplikace, ID verze a nový název záměru.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

Zatímco entity nejsou vyžadovány, nacházejí se ve většině aplikací. Entita extrahuje informace z promluvy uživatele, nezbytné k fullfil záměruživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) a vlastních entit, z nichž každý má vlastní modely objektu transformace dat (DTO).  Běžné předem vytvořené entity, které chcete přidat do aplikace, zahrnují [číslo](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinální](../luis-reference-prebuilt-ordinal.md).

Tato **metoda** add_entities `Location` vytvořila jednoduchou entitu se dvěma rolemi, jednoduchou `Class` entitou, složenou entitou `Flight` a přidala několik předem sestavených entit.

Je důležité vědět, že entity nejsou označeny záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkové projevy uživatelů jsou označeny pro konkrétní, jeden záměr.

Metody vytváření entit jsou součástí třídy [ModelOperations.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) Každý typ entity má svůj vlastní model objektu transformace dat (DTO).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Přidat ukázkový projev záměru

Chcete-li určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a měly by označit všechny vlastní entity. Předem sestavené entity není nutné označovat.

Přidejte ukázkové projevy vytvořením seznamu objektů [ExampleLabelObject,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) jednoho objektu pro každý příklad utterance. Každý příklad by měl označit všechny entity slovníkem dvojic název/hodnota názvu entity a hodnoty entity. Hodnota entity by měla být přesně tak, jak je uvedena v textu promluvy příkladu.

Volání [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) s ID aplikace, ID verze a seznam příkladů. Volání odpoví seznamem výsledků. Je třeba zkontrolovat výsledek každého příkladu a ujistěte se, že byl úspěšně přidán do modelu.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu aplikace LUIS musí být trénované pro tuto verzi modelu. Trénovaný model lze použít v [kontejneru](../luis-container-howto.md)nebo [publikovat](../luis-how-to-publish-app.md) do pracovních nebo produktových slotů.

Metoda [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) potřebuje ID aplikace a ID verze.

Velmi malý model, jako je tento rychlý start ukazuje, bude trénovat velmi rychle. Pro aplikace na úrovni produkčního prostředí by školení aplikace měla zahrnovat volání dotazování na metodu [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) k určení, kdy nebo zda školení proběhlo úspěšně. Odpověď je seznam [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) objekty se samostatným stavem pro každý objekt. Všechny objekty musí být úspěšné, aby školení bylo považováno za úplné.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publikování aplikace Language Understanding

Publikujte aplikaci LUIS pomocí metody [app.publish.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) To publikuje aktuální trénované verze do zadaného slotu v koncovém bodě. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakce entity.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python` v souboru quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení s vaše předpovědi, vyčistit práci z tohoto rychlého startu odstraněním souboru a jeho podadresáře.