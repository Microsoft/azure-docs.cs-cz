---
title: Použití sady C# SDK služby Speech se službou LUIS
titleSuffix: Azure Cognitive Services
description: Služba Speech umožňuje pomocí jednoho požadavku přijímat zvuk a vracet objekty JSON s předpověďmi služby LUIS. V tomto článku stáhnete projekt jazyka C# v sadě Visual Studio a použijete ho k vyslovení promluvy do mikrofonu a přijetí informací o předpovědi služby LUIS. V tomto projektu se používá balíček NuGet služby Speech, který je již zahrnutý jako odkaz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: f98d640f032fed5f91df8e9d4fb55d3f20550339
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883920"
---
# <a name="integrate-speech-service"></a>Integrace služby Speech
[Služba Speech](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umožňuje pomocí jednoho požadavku přijímat zvuk a vracet objekty JSON s předpověďmi služby LUIS. V tomto článku stáhnete projekt jazyka C# v sadě Visual Studio a použijete ho k vyslovení promluvy do mikrofonu a přijetí informací o předpovědi služby LUIS. V tomto projektu se používá balíček [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) služby Speech, který je již zahrnutý jako odkaz. 

Pro účely tohoto článku potřebujete bezplatný webový účet [LUIS][LUIS], abyste mohli aplikaci importovat.

## <a name="create-luis-endpoint-key"></a>Vytvoření klíče koncového bodu služby LUIS
Na webu Azure Portal [vytvořte](luis-how-to-azure-subscription.md#create-luis-endpoint-key) klíč služby **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Import aplikace LUIS pro lidské zdroje
Záměry a promluvy v tomto článku pocházejí z aplikace LUIS pro lidské zdroje, která je k dispozici v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) na GitHubu. Stáhněte soubor [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources.json), uložte ho s příponou `.json` a [importujte](luis-how-to-start-new-app.md#import-new-app) ho do služby LUIS. 

Tato aplikace obsahuje záměry, entity a promluvy související s doménou lidských zdrojů. Mezi ukázkové promluvy patří:

|Ukázkové promluvy|
|--|
|Kdo je manažer Jana Nováka?|
|Čí manažer je Jan Novák?|
|Kde je formulář 123456?|
|Mám nějaké placené volno?|


## <a name="add-keyphrase-prebuilt-entity"></a>Přidání předem připravené entity klíčové fráze
Po importu aplikace vyberte **Entities** (Entity) a pak **Manage prebuilt entities** (Spravovat předem připravené entity). Přidejte entitu **klíčové fráze**. Entita klíčové fráze extrahuje z promluvy klíčové téma.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. Na pravém horním navigačním panelu vyberte tlačítko **Train** (Trénovat) a natrénujte aplikaci LUIS.

2. Na pravém horním panelu vyberte **Manage** (Spravovat) a pak v levé navigaci vyberte **Keys and endpoints** (Klíče a koncové body). 

3. Na stránce **Keys and endpoints** (Klíče a koncové body) přiřaďte klíč služby LUIS, který jste vytvořili v části [Vytvoření klíče koncového bodu služby LUIS](#create-luis-endpoint-key).

  Na této stránce si poznamenejte ID aplikace, oblast publikování a ID předplatného pro klíč služby LUIS, který jste vytvořili v části [Vytvoření klíče koncového bodu služby LUIS](#create-luis-endpoint-key). V pozdější části tohoto článku budete muset upravit kód s použitím těchto hodnot. 
  
  Pro účely tohoto cvičení **nepoužívejte** bezplatný počáteční klíč. V tomto cvičení bude fungovat pouze klíč služby **Language Understanding** vytvořený na webu Azure Portal. 

  https://**OBLAST**.api.cognitive.microsoft.com/luis/v2.0/apps/**ID_APLIKACE**?subscription-key=**KLÍČ_SLUŽBY_LUIS**&q=


4. Publikujte aplikaci LUIS výběrem tlačítka **Publish** (Publikovat) na pravém horním panelu. 

## <a name="audio-device"></a>Zvukové zařízení
V tomto článku se používá zvukové zařízení na vašem počítači. Tímto zařízením může být náhlavní souprava s mikrofonem nebo integrované zvukové zařízení. Zkontrolujte úrovně zvukového vstupu a zjistěte, jestli je potřeba mluvit hlasitěji než normálně, aby zvukové zařízení rozpoznalo váš projev. 

## <a name="download-the-luis-sample-project"></a>Stažení ukázkového projektu LUIS
 Naklonujte nebo si stáhněte úložiště [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples). V sadě Visual Studio otevřete [projekt převodu řeči na záměr](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) a obnovte balíčky NuGet. Soubor řešení sady Visual Studio je .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Sada SDK služby Speech je již zahrnutá jako odkaz. 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Snímek obrazovky sady Visual Studio 2017 se zobrazeným balíčkem NuGet Microsoft.CognitiveServices.Speech")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>Úprava kódu jazyka C#
Otevřete soubor `Program.cs` a změňte následující proměnné:

|Název proměnné|Účel|
|--|--|
|LUIS_assigned_endpoint_key|Odpovídá hodnotě klíče předplatného přiřazeného k adrese URL koncového bodu ze stránky publikování|
|LUIS_endpoint_key_region|Odpovídá první subdoméně adresy URL koncového bodu, například `westus`|
|LUIS_app_ID|Odpovídá trase adresy URL koncového bodu, která následuje za **apps/**|

Soubor `Program.cs` již obsahuje namapované záměry pro lidské zdroje.

Sestavte a spusťte aplikaci. 

## <a name="test-code-with-utterance"></a>Test kódu pomocí promluvy
Řekněte do mikrofonu „Kdo jsou osvědčení zubní lékaři v Redmondu?“.

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

S 85% spolehlivostí se zjistil správný záměr **GetEmployeeBenefits**. Vrátila se entita klíčové fráze. 

Sada SDK služby Speech vrací úplnou odpověď služby LUIS. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už ji nepotřebujete, odstraňte aplikaci LUIS pro lidské zdroje. Provedete to tak, že vyberete aplikaci a pak na kontextovém panelu nástrojů nad seznamem vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

Až se vzorovým kódem skončíte, nezapomeňte odstranit adresář LUIS-Samples.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Integrace služby LUIS s chatbotem](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website