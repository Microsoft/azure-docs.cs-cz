---
title: 'Rychlý Start: Language Understanding (LUIS) vytváření klientské knihovny pro Python'
titleSuffix: Azure Cognitive Services
description: Začněte s tímto rychlým startem pomocí klientské knihovny LUIS pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 398220306fad4dc3459446fa5f77371ac669dd7f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381490"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-python"></a>Rychlý Start: Language Understanding (LUIS) vytváření klientské knihovny pro Python

Začínáme s klientskou knihovnou pro vytváření Language Understanding (LUIS) pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.  Language Understanding (LUIS) vám umožňuje použít vlastní informace o strojovém učení na konverzaci uživatele, text přirozeného jazyka a předpovědět celkový význam a vyžádat si relevantní a podrobné informace. 

Pomocí klientské knihovny pro vytváření Language Understanding (LUIS) pro Python:

* Vytvořte aplikaci.
* Přidejte záměry, entity a příklad projevy.
* Přidejte funkce, jako je například seznam frází.
* Výuka a publikování aplikace

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [vytváření obsahu (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Požadavky

* Účet portálu Language Understanding (LUIS): [Vytvořte si ho zdarma](https://www.luis.ai).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-starter-key"></a>Získání spouštěcího klíče pro Language Understanding (LUIS)

Získejte [počáteční klíč](luis-how-to-azure-subscription.md#starter-key) vytvořením prostředku pro vytváření Luis. Zachovejte klíč a oblast klíče pro další krok.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a oblasti pro klíč vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_AUTHORING_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `LUIS_REGION` – oblast přidružená ke klíči. Například `westus`.

Použijte pokyny pro váš operační systém.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_REGION <replace-with-your-luis-region>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_REGION=<replace-with-your-luis-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změna projevila.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key> 
export LUIS_REGION=<replace-with-your-luis-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změna projevila.
***

### <a name="install-the-python-library-for-luis"></a>Instalace knihovny Pythonu pro LUIS

V adresáři aplikace nainstalujte Language Understanding (LUIS) pro vytváření klientských knihoven pro Python pomocí následujícího příkazu:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektový model

Klient pro vytváření Language Understanding (LUIS) je objekt [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) , který se ověřuje v Azure, který obsahuje váš klíč pro vytváření obsahu.

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* Aplikace – [vytvořit](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [Odstranit](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publikovat](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Příklad projevy – [Přidání v dávce](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [odstranění podle ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-) 
* Funkce – Správa [seznamů frází](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) 
* Model – Správa [záměrů](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) a entit
* Vzor – Správa [vzorů](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Výuka aplikace [](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) a dotazování na [stav školení](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Verze](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) – Správa pomocí klonování, exportu a odstranění


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro vytváření Language Understanding (LUIS) pro Python:

* [Vytvoření aplikace](#create-a-luis-app)
* [Přidání entit](#create-entities-for-the-app)
* [Přidání záměrů](#create-intent-for-the-app)
* [Přidat příklad projevy](#add-example-utterance-to-intent)
* [Výuka aplikace](#train-the-app)
* [Publikování aplikace](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci v Pythonu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí. Pak importujte následující knihovny. 

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) .

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Vytvořte aplikaci LUIS, která bude obsahovat pracovní záměry modelu NLP (přirozený jazyk Processing), entity a příklad projevy. 

1. Vytvořte metodu [Add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) objektu [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) k vytvoření aplikace. Název a jazyková verze jazyka jsou požadované vlastnosti. 

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Vytvořit záměr pro aplikaci
Primárním objektem v modelu aplikace LUIS je záměr. Záměr se zarovnává se seskupením _záměrů_uživatelů utterance. Uživatel může požádat o dotaz nebo vytvořit příkaz, který hledá konkrétní _zamýšlenou_ odpověď z robota (nebo jiné klientské aplikace). Příklady záměrů je vyúčtování letu, dotazování na počasí v cílovém městě a dotazování na kontaktní informace pro zákaznické služby.   

Použijte metodu [model. add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) s názvem jedinečného záměru a potom předejte ID aplikace, ID verze a název nového záměru. 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

I když se entity nevyžadují, najdete je ve většině aplikací. Entita extrahuje informace z utterance uživatele, která je nutná k zaúmyslnému záměru uživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) a vlastních entit, z nichž každý má vlastní modely DTO (Data Transformation Object).  Mezi běžné předem připravené entity, které se mají přidat do vaší aplikace, patří [číslo](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), [geographyV2](luis-reference-prebuilt-geographyv2.md)a [Ordinal](luis-reference-prebuilt-ordinal.md). 

Tato metoda **add_entities** vytvořila `Location` jednoduchou entitu se dvěma rolemi, `Class` jednoduchou entitou, `Flight` složenou entitou a přidává několik předem sestavených entit.

Je důležité, abyste věděli, že entity nejsou označené záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkový uživatel projevy je označen pro konkrétní, jednotlivý záměr.

Metody vytváření entit jsou součástí třídy [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python) . Každý typ entity má svůj vlastní model objektu pro transformaci dat (DTO). 

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Přidat příklad utterance k záměru

Aby bylo možné určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a označit všechny vlastní entity. Předem připravené entity není nutné označit. 

Přidejte příklad projevy vytvořením seznamu objektů [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python) , jeden objekt pro každý příklad utterance. Každý příklad by měl označit všechny entity pomocí slovníku dvojic název-hodnota a hodnota entity. Hodnota entity by měla být přesně tak, jak se zobrazuje v textu příkladu utterance. 

[Příklady volání. Batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) s ID aplikace, ID verze a seznam příkladů. Volání odpoví seznamem výsledků. Je potřeba zkontrolovat výsledek každého příkladu, abyste se ujistili, že se úspěšně přidal do modelu. 

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]
    
## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu musí být aplikace LUIS vyškolená pro tuto verzi modelu. Vyškolený model se dá použít v [kontejneru](luis-container-howto.md)nebo ho můžete [publikovat](luis-how-to-publish-app.md) na pracovní nebo výrobní sloty. 

Metoda [vlak. train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) potřebuje ID aplikace a ID verze. 

Velmi malý model, jako je například tento rychlý Start, bude vlak velmi rychlý. Pro aplikace na úrovni produkčního prostředí by mělo školení aplikace zahrnovat volání metody [get_Status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) k určení, kdy nebo jestli se školení podařilo. Odpověď je seznam objektů [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) s odděleným stavem pro každý objekt. Aby bylo školení považovat za dokončené, musí být všechny objekty úspěšné.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publikování aplikace Language Understanding

Publikujte aplikaci LUIS pomocí metody [App. Publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-) . Tím se publikuje aktuální vyškolená verze v zadaném slotu na koncovém bodu. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakci entit.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python` v souboru rychlého startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources) 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Vytvoření aplikace v LUIS k určení záměrů uživatelů](luis-quickstart-intents-only.md)

* [Co je rozhraní API pro Language Understanding (LUIS)?](what-is-luis.md)
* [Co je nového?](whats-new.md)
* [Záměry](luis-concept-intent.md), [entity](luis-concept-entity-types.md)a [Příklady projevy](luis-concept-utterance.md)a [předem připravených entit](luis-reference-prebuilt-entities.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)
