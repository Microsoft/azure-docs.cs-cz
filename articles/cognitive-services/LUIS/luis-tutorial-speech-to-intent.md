---
title: Služba luis umožňuje využít řeč C# SDK
titleSuffix: Azure Cognitive Services
description: Služba Speech umožňuje používat jeden požadavek přijímat zvuk a vrátit LUIS předpovědi objekty JSON. V tomto článku stažení a použití projektu v jazyce C# v sadě Visual Studio mluvit utterance do mikrofon a přijímat informace predikcí služby LUIS. Projekt používá balíček NuGet řeči, již zahrnut jako odkaz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 1625bb9e9f51f8460db4e7ccbaf6e5eada3f8180
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831055"
---
# <a name="integrate-speech-service"></a>Integrace Speech service
[Speech service](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umožňuje použití jednoho požadavku a přijímat zvuk vrátit LUIS předpovědi objekty JSON. V tomto článku stažení a použití projektu v jazyce C# v sadě Visual Studio mluvit utterance do mikrofon a přijímat informace predikcí služby LUIS. Projekt používá Řeč [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) balíčku již zahrnut jako odkaz. 

Pro účely tohoto článku budete potřebovat bezplatný [LUIS] [ LUIS] webu účtu, aby bylo možné importovat aplikaci.

## <a name="create-luis-endpoint-key"></a>Vytvoření klíče koncového bodu služby LUIS
Na webu Azure Portal [vytvořit](luis-how-to-azure-subscription.md#create-luis-endpoint-key) **Language Understanding** klíč (LUIS). 

## <a name="import-human-resources-luis-app"></a>Importovat lidských zdrojů LUIS aplikace
Záměry a projevy pro účely tohoto článku pocházejí z aplikace LUIS lidské zdroje, k dispozici [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) úložiště Github. Stáhněte si [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) soubor uložte s příponou *.json a [importovat](luis-how-to-start-new-app.md#import-new-app) ji do služby LUIS. 

Tato aplikace nemá záměrů, entit a projevy související s domény lidských zdrojů. Příklad projevy patří:

|Ukázkové promluvy|
|--|
|Kdo je správce John Smith?|
|Tím, kdo spravovat John Smith?|
|Kde je formulář 123456?|
|Mám placené kdykoli?|


## <a name="add-keyphrase-prebuilt-entity"></a>Přidat KeyPhrase předem připravených entit
Po importu aplikace, vyberte **entity**, pak **spravovat předem připravených entit**. Přidat **KeyPhrase** entity. KeyPhrase entity extrahuje klíče odborník na danou z utterance.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. V horní, pravé navigační panel, vyberte **trénování** tlačítko tak moct trénovat aplikace LUIS.

2. Vyberte **spravovat** v pravém horním rohu panelu, vyberte **klíče a koncových bodů** v levém navigačním panelu. 

3. Na **klíče a koncových bodů** stránce, přiřaďte LUIS klíče vytvořeného v [klíče koncového bodu služby LUIS vytvořit](#create-luis-endpoint-key) oddílu.

  Na této stránce shromažďování ID aplikace, publikovat oblasti a ID předplatného služby LUIS klíče vytvořené v [klíče koncového bodu služby LUIS vytvořit](#create-luis-endpoint-key) oddílu. Budete muset upravit kód, který použije tyto hodnoty dále v tomto článku. 
  
  Proveďte **není** použít bezplatnou úvodní klíč pro toto cvičení. Pouze **Language Understanding** klíče vytvořeného na webu Azure Portal bude fungovat pro účely tohoto cvičení. 

  https://**oblasti**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? klíč předplatného =**LUISKEY**& q =


4. Publikování aplikace LUIS tak, že vyberete **publikovat** tlačítko v horní části pravého panelu. 

## <a name="audio-device"></a>Zvukové zařízení
Tento článek používá zvukové zařízení ve vašem počítači. Který může být sluchátka s mikrofonem mikrofon nebo integrované zvukové zařízení. Zkontrolujte vstupní úrovně hlasitosti zobrazíte, pokud jste měli mluvit hlasitost, než byste obvykle mají řeč detekoval zvukové zařízení. 

## <a name="download-the-luis-sample-project"></a>Stáhněte si ukázkový LUIS projekt
 Klonovat nebo stáhnout [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) úložiště. Otevřít [převod řeči na záměr projektu](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) pomocí sady Visual Studio a obnovte balíčky NuGet. Soubor řešení VS je.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Sadou SDK pro řeč je již zahrnut jako odkaz. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Zobrazení balíčku Microsoft.CognitiveServices.Speech NuGet snímek obrazovky sady Visual Studio 2017")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Upravte kód jazyka C#
Otevřít **LUIS_samples.cs** soubor a změňte následující proměnné:

|Název proměnné|Účel|
|--|--|
|luisSubscriptionKey|Odpovídá hodnotě klíč předplatného adresu URL koncového bodu ze stránky publikovat|
|luisRegion|Odpovídá subdoménu první adresa URL koncového bodu|
|luisAppId|Odpovídá na trasu URL koncového bodu po **aplikace /**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Snímek obrazovky sady Visual Studio 2017 zobrazení LUIS_samples.cs proměnné")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Soubor už má záměry lidských zdrojů namapována.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Snímek obrazovky sady Visual Studio 2017 zobrazení LUIS_samples.cs záměrů")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Sestavte a spusťte aplikaci. 

## <a name="test-code-with-utterance"></a>Testovací kód s utterance
Vyberte **1** a do mikrofonu "Kdo je správce Jan Macek".

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

Správné záměr **GetEmployeeOrgChart**, byl nalezen s 61 % spolehlivostí. Byl vrácen keyPhrase entity. 

Sadou SDK pro řeč vrátí celé odpovědi služby LUIS. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, odstraňte aplikaci LUIS Lidskézdroje. Uděláte to tak, vyberte aplikaci, a potom v kontextové panelu nástrojů nad seznamem, vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

Nezapomeňte odstranit LUIS-Samples directory po dokončení pomocí ukázkového kódu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrace LUIS s využitím BOTU](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website