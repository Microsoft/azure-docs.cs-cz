---
title: Umožňuje využít řeč C# SDK
titleSuffix: Azure Cognitive Services
description: Služba Speech umožňuje pomocí jednoho požadavku přijímat zvuk a vracet objekty JSON s předpověďmi služby LUIS. V tomto článku stáhnete projekt jazyka C# v sadě Visual Studio a použijete ho k vyslovení promluvy do mikrofonu a přijetí informací o předpovědi služby LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: df81c892df29b1ffd5fab2e93c82f664c7b613b7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216155"
---
# <a name="integrate-speech-service-with-your-language-understanding-app"></a>Speech service integrovat s vaší aplikací Language Understanding
[Služba Speech](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) umožňuje pomocí jednoho požadavku přijímat zvuk a vracet objekty JSON s předpověďmi služby LUIS. V tomto článku stáhnete projekt jazyka C# v sadě Visual Studio a použijete ho k vyslovení promluvy do mikrofonu a přijetí informací o předpovědi služby LUIS. V tomto projektu se používá balíček [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) služby Speech, který je již zahrnutý jako odkaz. 

Pro účely tohoto článku potřebujete bezplatný webový účet [LUIS][LUIS], abyste mohli aplikaci importovat.

## <a name="create-luis-endpoint-key"></a>Vytvoření klíče koncového bodu služby LUIS
Na webu Azure Portal [vytvořte](luis-how-to-azure-subscription.md) klíč služby **Language Understanding** (LUIS). 

## <a name="import-human-resources-luis-app"></a>Import aplikace LUIS pro lidské zdroje
Záměry a projevy pro účely tohoto článku pocházejí z aplikace LUIS lidské zdroje, k dispozici [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) úložiště GitHub. Stáhněte soubor [HumanResources.json](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources.json), uložte ho s příponou `.json` a [importujte](luis-how-to-start-new-app.md#import-new-app) ho do služby LUIS. 

Tato aplikace obsahuje záměry, entity a promluvy související s doménou lidských zdrojů. Mezi ukázkové promluvy patří:

|Ukázkové promluvy|
|--|
|Kdo je manažer Jana Nováka?|
|Čí manažer je Jan Novák?|
|Kde je formulář 123456?|
|Mám nějaké placené volno?|


## <a name="add-keyphrase-prebuilt-entity"></a>Přidání předem připravené entity klíčové fráze
Po importu aplikace vyberte **Entities** (Entity) a pak **Add prebuilt entity** (Přidat předem připravenou entitu). Přidejte entitu **klíčové fráze**. Entita klíčové fráze extrahuje z promluvy klíčové téma.

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
 Klonovat nebo stáhnout [Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding) úložiště. V sadě Visual Studio otevřete [projekt převodu řeči na záměr](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-speech-intent-recognition) a obnovte balíčky NuGet. Soubor řešení VS je documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln.

Sada SDK služby Speech je již zahrnutá jako odkaz. 

[![Snímek obrazovky sady Visual Studio 2017 zobrazování balíček Microsoft.CognitiveServices.Speech NuGet](./media/luis-tutorial-speech-to-intent/nuget-package.png "zobrazování balíček Microsoft.CognitiveServices.Speech NuGet snímek obrazovky sady Visual Studio 2017")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

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

Nezapomeňte odstranit adresář, jakmile budete hotovi pomocí ukázkového kódu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrace služby LUIS s chatbotem](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
