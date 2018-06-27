---
title: Rozpoznávání řeči C# SDK pomocí LEOŠ - Azure | Microsoft Docs
titleSuffix: Azure
description: Pomocí řeči C# SDK – ukázka do mikrofon a získat LEOŠ záměr a entity předpovědi vrátila.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b8a2c0dbadb0124b9250849a0260f5b34d38a5c3
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021659"
---
# <a name="integrate-speech-service"></a>Integrace služby řeči
[Řeči služby](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umožňuje používat jeden požadavek na obdrží zvuk a vrátí LEOŠ předpovědi objekty JSON.

V tomto článku můžete stáhnout a používat projektu C# v sadě Visual Studio řeči utterance do mikrofonu a přijímat informace LEOŠ předpovědi. Projekt využívá Řeč [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) balíčku, které jsou již zahrnuty jako odkaz. 

V tomto článku budete potřebovat bezplatný [LEOŠ] [ LUIS] účtu webu, aby bylo možné importovat aplikaci.

## <a name="create-luis-endpoint-key"></a>Vytvořte klíč LEOŠ koncového bodu
Na portálu Azure [vytvořit](luis-how-to-azure-subscription.md#create-luis-endpoint-key) **znalosti jazyka** klíč (LEOŠ). 

## <a name="import-human-resources-luis-app"></a>Import lidských zdrojů LEOŠ aplikace
Záměry a utterances pro tento článek se z lidských zdrojů LEOŠ aplikace dostupná z [LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples) úložiště Github. Stažení [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) soubor, uložte ji s příponou *.json a [importovat](create-new-app.md#import-new-app) do LEOŠ. 

Tato aplikace nemá záměry entity a utterances související s domény lidských zdrojů. Příklad utterances patří:

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>Přidat KeyPhrase předem entity
Po importu aplikace, vyberte **entity**, pak **spravovat předem entity**. Přidat **KeyPhrase** entity. Entita KeyPhrase extrahuje tematických klíče z utterance.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. V horní, pravé navigačním panelu, vyberte **cvičení** tlačítko ke cvičení LEOŠ aplikace.

2. Vyberte **publikovat** přejděte na stránku publikovat. 

3. V dolní části **publikovat** přidejte LEOŠ klíče vytvořeného v [klíč koncového bodu vytvořit LEOŠ](#create-luis-endpoint-key) části.

4. Publikování aplikace LEOŠ výběrem **publikovat** tlačítko napravo od pozice publikovat. 

  Na **publikovat** stránky, shromažďování ID aplikace, publikujte oblast a ID předplatného LEOŠ klíče vytvořené v [klíč koncového bodu vytvořit LEOŠ](#create-luis-endpoint-key) části. Budete muset upravit kód, který použije tyto hodnoty později v tomto článku. 

  Tyto hodnoty jsou zahrnuty v adrese URL koncového bodu v dolní části **publikovat** stránky pro klíč, který jste vytvořili. Proveďte **není** pomocí volné starter klíče pro toto cvičení. 

  https://**oblast**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**? klíč předplatného =**LUISKEY**& q =

## <a name="audio-device"></a>Zvukového zařízení
Tento článek používá zvukového zařízení ve vašem počítači. Který může být mikrofonem s mikrofon nebo integrované zvukového zařízení. Zkontrolujte vstupní úrovně hlasitosti chcete zobrazit, pokud jste měli řeči hlasitost než za normálních okolností byste mít řeč zjištěný zvukového zařízení. 

## <a name="download-the-luis-sample-project"></a>Stáhnout LEOŠ ukázkového projektu
 Klonovat nebo stáhnout [LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples) úložiště. Otevřete [převod řeči na záměrné projektu](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) pomocí sady Visual Studio a obnovování balíčků NuGet. Soubor řešení VS je.\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Rozpoznávání řeči SDK je už součástí sady jako odkaz. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Snímek obrazovky nástroje Visual Studio 2017 zobrazování balíček Microsoft.CognitiveServices.Speech NuGet")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Upravit kód C#
Otevřete **LUIS_samples.cs** soubor a změňte následující proměnné:

|Název proměnné|Účel|
|--|--|
|luisSubscriptionKey|Odpovídá hodnotě klíč předplatného adresu URL koncového bodu ze stránky publikování|
|luisRegion|Odpovídá subdomény první adresa URL koncového bodu|
|luisAppId|Odpovídá na trasu URL koncového bodu, následující **aplikace nebo**|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Snímek obrazovky nástroje Visual Studio 2017 zobrazení LUIS_samples.cs proměnné")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

Soubor již záměry lidských zdrojů namapované.

[![](./media/luis-tutorial-speech-to-intent/intents.png "Snímek obrazovky nástroje Visual Studio 2017 zobrazení LUIS_samples.cs záměry")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

Sestavte a spusťte aplikaci. 

![Snímek obrazovky příkazového řádku spuštění programu](./media/luis-tutorial-speech-to-intent/cmdline-1.png)

## <a name="test-code-with-utterance"></a>Testování kódu s utterance
Vyberte **8** a do mikrofonu "Kdo je správce John Smith".

```cmd
1. Speech recognition with microphone input.
2. Speech recognition in the specified language.
3. Speech recognition with file input.
4. Speech recognition using customized model.
5. Speech continuous recognition using events.
6. Translation with microphone input.
7. Translation with file input.
8. Speech recognition of LUIS intent.
0. Stop.
Your choice: 8
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

Správné záměr **GetEmployeeOrgChart**, byl nalezen s jistotou 61 %. Byl vrácen keyPhrase entity. 

Sada SDK řeči vrátí celé LEOŠ odpovědi. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete, odstraňte aplikaci LEOŠ Lidskézdroje. To pokud chcete udělat, vyberte v seznamu aplikací, vyberte v nabídce tři tečky (...) vpravo od názvu aplikace **odstranit**. V místním dialogovém okně **aplikaci odstranit?**, vyberte **Ok**.

Nezapomeňte odstranit LEOŠ-Samples directory po dokončení pomocí ukázkový kód.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrovat LEOŠ ROBOTU](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website