---
title: Prostředky pro vývojáře – Language Understanding
description: Sady SDK, rozhraní REST API a CLI vám pomůžou vyvíjet aplikace Language Understanding (LUIS) ve vašem programovacím jazyce. Spravujte své prostředky Azure a LUIS předpovědi.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 820811c53f143c9747cd11f45cafb075398b080b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589002"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Materiály pro vývojáře sady SDK, REST a CLI pro Language Understanding (LUIS)

Sady SDK, rozhraní REST API a CLI vám pomůžou vyvíjet aplikace Language Understanding (LUIS) ve vašem programovacím jazyce. Spravujte své prostředky Azure a LUIS předpovědi.

## <a name="azure-resource-management"></a>Správa prostředků Azure

Pomocí vrstvy správy Azure Cognitive Services můžete vytvořit, upravit, vypsat a odstranit prostředek Language Understanding nebo služby pro rozpoznávání.

Vyhledejte referenční dokumentaci na základě nástroje:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding žádosti o vytváření a předpovědi

Služba Language Understanding je k dispozici z prostředku Azure, který potřebujete vytvořit. Existují dva prostředky:

* K vytváření, úpravám, školení a publikování můžete použít prostředek pro **vytváření obsahu** .
* K odeslání textu uživatele a získání předpovědi použijte **předpověď** pro modul runtime.

Přečtěte si o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Pomocí [Cognitive Services ukázkový kód](https://github.com/Azure-Samples/cognitive-services-quickstart-code) se naučíte a používat nejběžnější úlohy.

### <a name="rest-specifications"></a>Specifikace REST

[Specifikace Luis REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)spolu se všemi [specifikacemi Azure REST](https://github.com/Azure/azure-rest-api-specs)jsou veřejně dostupné na GitHubu.

### <a name="rest-apis"></a>Rozhraní REST API

Rozhraní API pro vytváření obsahu a předpovědi jsou dostupná z rozhraní REST API:

|Typ|Verze|
|--|--|
|Vytváření obsahu|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Preview verze 3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prediction (Předpověď)|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Technologie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Koncové body REST

LUIS má aktuálně 2 typy koncových bodů:

* vytváření obsahu pro školicí koncový bod
* předpověď dotazů na koncový bod modulu runtime.

|Účel|URL|
|--|--|
|Vytváření obsahu na školicím bodu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 runtime – všechny předpovědi v koncovém bodu za běhu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Verze v3 runtime – předpověď verzí na koncovém bodu za běhu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Verze v3 runtime – předpověď slotu v běhovém koncovém bodu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Následující tabulka popisuje parametry, které jsou označeny složenými závorkami `{}` v předchozí tabulce.

|Parametr|Účel|
|--|--|
|`your-resource-name`|Název prostředku Azure|
|`q` nebo `query`|utterance text odeslaný z klientské aplikace, jako je třeba robota chatu|
|`version`|10 název verze znaku|
|`slot`| `production` nebo `staging`|

## <a name="app-schema"></a>Schéma aplikace

[Schéma aplikace](app-schema-definition.md) je importováno a exportováno ve `.json` `.lu` formátu nebo.

### <a name="language-based-sdks"></a>Sady SDK založené na jazyce

|Jazyk |Referenční dokumentace|Balíček|ukázky|Rychlé starty|
|--|--|--|--|--|
|C#|[Vytváření obsahu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Předpovědi](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Vytváření balíčků NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Předpověď NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ukázky sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Vytvoření a správa aplikace](sdk-authoring.md?pivots=programming-language-csharp)<br>[Koncový bod předpovědi dotazů](sdk-query-prediction-endpoint.md)|
|Go|[Vytváření a předpověď](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sada SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Předpovědi](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Vytváření a předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Vytváření a předpověď](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Vytváření Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Předpověď Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Předpovědi](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Vytváření a předpověď](luis-get-started-get-intent-from-rest.md)
|Node.js|[Vytváření obsahu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Předpovědi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Vytváření NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Předpověď NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Předpovědi](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Vytváření a předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Vytváření a předpověď](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Vytváření obsahu](sdk-authoring.md?pivots=programming-language-python)<br>[Předpověď pomocí REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Kontejnery

Language Understanding (LUIS) poskytuje [kontejner](luis-container-howto.md) pro poskytování místních a obsažených verzí vaší aplikace.

### <a name="export-and-import-formats"></a>Formáty exportu a importu

Language Understanding poskytuje možnost spravovat svou aplikaci a její modely ve formátu JSON, ve `.LU` formátu[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)(formát) a komprimovaném balíčku pro kontejner Language Understanding.

Import a export těchto formátů je dostupný z rozhraní API a z portálu LUIS. Portál poskytuje import a export jako součást seznamu a verzí seznamu aplikací.

## <a name="other-tools-and-sdks"></a>Další nástroje a sady SDK

Rozhraní bot Framework je dostupné jako [sada SDK](https://github.com/Microsoft/botframework) v různých jazycích a jako služba pomocí [Azure bot Service](https://dev.botframework.com/).

Robot Framework nabízí [několik nástrojů](https://github.com/microsoft/botbuilder-tools) , které vám pomůžou s Language Understanding, včetně:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – vytváření Luis jazyka pro porozumění modelům pomocí souborů Markdownu
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – vytvoření a správa aplikací Luis.AI
* [Odesílání](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– Správa nadřazených a podřízených aplikací
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – automatické generování záložních tříd C#/TypeScript pro vaše Luis záměry a entity.
* [Emulátor rozhraní robot](https://github.com/Microsoft/BotFramework-Emulator/releases) – desktopová aplikace umožňující vývojářům robotům testovat a ladit roboty sestavené pomocí sady robot Framework SDK
* [Robot Framework robot](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – integrovaný vývojový nástroj pro vývojáře a týmy pro více týmů k sestavování roboty a konverzací v prostředí Microsoft bot Framework
* [Microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) – nástroje podporující průběžnou integraci a nasazování pro služby NLU.

## <a name="next-steps"></a>Další kroky

* Další informace o běžných [kódech chyb HTTP](luis-reference-response-codes.md)
* [Referenční dokumentace](https://docs.microsoft.com/azure/index) pro všechna rozhraní API a sady SDK
* [Rozhraní bot Framework](https://github.com/Microsoft/botbuilder-dotnet) a [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Rozpoznávání kontejnerů](../cognitive-services-container-support.md)
