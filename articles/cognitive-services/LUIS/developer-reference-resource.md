---
title: Prostředky pro vývojáře – Language Understanding
titleSuffix: Azure Cognitive Services
description: Vývojáři mají rozhraní REST API i sady SDK pro Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: 1cde28bff48bc239e22fe47a882e3218bd2d938b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278567"
---
# <a name="developer-resources-for-language-understanding"></a>Materiály pro vývojáře pro Language Understanding

Vývojáři můžou použít rozhraní REST API i sady SDK pro Language Understanding. 

## <a name="azure-resource-management"></a>Správa prostředků Azure

Pomocí vrstvy správy Azure Cognitive Services můžete vytvořit, upravit, vypsat a odstranit prostředek Language Understanding nebo služby pro rozpoznávání.

Vyhledejte referenční dokumentaci na základě nástroje:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding žádosti o vytváření a předpovědi

Služba Language Understanding je k dispozici z prostředku Azure, který potřebujete vytvořit. Existují dva prostředky: vytváření a předpovědi prostředků koncového bodu. Oba tyto prostředky umožňují řízení prostředků LUIS. 

Přečtěte si o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

### <a name="rest-apis"></a>Rozhraní REST API

Rozhraní API pro vytváření obsahu a předpovědi jsou dostupná z rozhraní REST API:

|Typ|Version|
|--|--|
|Vytváření obsahu|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Preview verze 3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Předpověď|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Technologie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Sady SDK založené na jazyce

|Jazyk |Referenční dokumentace|Balíček|Ukázky|Šablony Rychlý start|
|--|--|--|--|--|
|C#|[Vytváření obsahu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Předpověď](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Vytváření balíčků NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Předpověď NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ukázky sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Vytvoření a Správa aplikace](sdk-csharp-quickstart-authoring-app.md)<br>[Koncový bod prediktivního dotazování](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[Vytváření a předpověď](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sada SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Vytváření a předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Vytváření a předpověď](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Vytváření Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Předpověď Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Vytváření a předpověď](luis-get-started-get-intent-from-rest.md)
|Node.js|[Vytváření obsahu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Předpověď](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Vytváření NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[Předpověď NPM](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Vytváření a předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Vytváření a předpověď](sdk-python-quickstart-authoring-app.md)|[PIP](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Vytváření obsahu](sdk-python-quickstart-authoring-app.md)<br>[Předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Kontejnery

Language Understanding (LUIS) poskytuje [kontejner](luis-container-howto.md) pro poskytování místních a obsažených verzí vaší aplikace. 

### <a name="export-and-import-formats"></a>Formáty exportu a importu

Language Understanding poskytuje možnost spravovat svou aplikaci a její modely ve formátu JSON, ve formátu `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) a komprimovaném balíčku pro kontejner Language Understanding. 

Import a export těchto formátů je dostupný z rozhraní API a z portálu LUIS. Portál poskytuje import a export jako součást seznamu a verzí seznamu aplikací. 

## <a name="other-tools-and-sdks"></a>Další nástroje a sady SDK

Rozhraní bot Framework je dostupné jako [sada SDK](https://github.com/Microsoft/botframework) v různých jazycích a jako služba pomocí [Azure bot Service](https://dev.botframework.com/). 

Robot Framework nabízí [několik nástrojů](https://github.com/microsoft/botbuilder-tools) , které vám pomůžou s Language Understanding, včetně:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – vytváření Luis jazyka pro porozumění modelům pomocí souborů Markdownu
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – vytvoření a správa aplikací Luis.AI
* [Odesílání](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– Správa nadřazených a podřízených aplikací
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – automaticky generuje záložní C#třídy/TypeScript pro Luis a entity.
* [Emulátor bot](https://github.com/Microsoft/BotFramework-Emulator/releases) – desktopová aplikace umožňující vývojářům robotům testovat a ladit roboty sestavené pomocí sady robot Framework SDK


## <a name="next-steps"></a>Další kroky

* Další informace o běžných [kódech chyb HTTP](luis-reference-response-codes.md)
* [Referenční dokumentace](https://docs.microsoft.com/azure/index#pivot=sdkstools) pro všechna rozhraní API a sady SDK
* [Rozhraní bot Framework](https://github.com/Microsoft/botbuilder-dotnet) a [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Rozpoznávání kontejnerů](../cognitive-services-container-support.md)