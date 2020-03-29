---
title: Zdroje pro vývojáře – Language Understanding
description: Sady SDK, rozhraní REST API, rozhraní CLI, které vám pomohou vyvíjet aplikace pro jazykové porozumění (LUIS) v programovacím jazyce. Spravujte prostředky Azure a předpovědi LUIS.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457980"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Vývojářské prostředky sady SDK, REST a CLI pro principy jazyků (LUIS)

Sady SDK, rozhraní REST API, rozhraní CLI, které vám pomohou vyvíjet aplikace pro jazykové porozumění (LUIS) v programovacím jazyce. Spravujte prostředky Azure a předpovědi LUIS.

## <a name="azure-resource-management"></a>Správa prostředků Azure

Pomocí vrstvy Azure Cognitive Services Management můžete vytvořit, upravit, vypsat a odstranit prostředek znalostního jazyka nebo kognitivní služby.

Najděte referenční dokumentaci založenou na nástroji:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Žádosti o vytváření a predikci jazyka

Služba Language Understanding je přístupná z prostředku Azure, který potřebujete vytvořit. Existují dva zdroje:

* Pomocí **zdrojového materiálu** pro školení můžete vytvářet, upravovat, trénovat a publikovat.
* Pomocí **předpovědi** pro běhový čas odeslat text uživatele a získat předpověď.

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

Ukázkový [kód služeb Cognitive Services](https://github.com/Azure-Samples/cognitive-services-quickstart-code) slouží k učení a používání nejběžnějších úloh.

### <a name="rest-specifications"></a>REST specifikace

[Specifikace LUIS REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)spolu se [všemi specifikacemi Azure REST](https://github.com/Azure/azure-rest-api-specs)jsou veřejně dostupné na GitHubu.

### <a name="rest-apis"></a>Rozhraní REST API

Rozhraní API koncového bodu pro vytváření a předpověď jsou k dispozici z rozhraní REST API:

|Typ|Version|
|--|--|
|Vytváření obsahu|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[náhled V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Prediction (Předpověď)|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Koncové body REST

Služba LUIS má aktuálně 2 typy koncových bodů:

* vytváření na koncovém bodu školení
* předpověď dotazu v koncovém bodě runtime.

|Účel|zprostředkovatele identity|
|--|--|
|Vytváření na koncovém bodu školení|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime – všechny předpovědi v koncovém bodě runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime – předpověď verzí v koncovém bodě runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime – predikce slotu v koncovém bodě runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Následující tabulka vysvětluje parametry označené složenými závorkami `{}`v předchozí tabulce.

|Parametr|Účel|
|--|--|
|`your-resource-name`|Název prostředku Azure|
|`q` nebo `query`|utterance text odeslaný z klientské aplikace, jako je chat bot|
|`version`|Název verze 10 znaků|
|`slot`| `production` nebo `staging`|

### <a name="language-based-sdks"></a>Sady SDK založené na jazyce

|Jazyk |Referenční dokumentace|Balíček|ukázky|Rychlé starty|
|--|--|--|--|--|
|C#|[Vytváření obsahu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Předpověď](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Vytváření NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet předpověď](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Ukázky sady .Net SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Vytvoření a správa aplikace](sdk-authoring.md?pivots=programming-language-csharp)<br>[Koncový bod předpovědi dotazů](sdk-query-prediction-endpoint.md)|
|Přejít|[Vytváření a predikce](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Vytváření a predikce pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Vytváření a predikce](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven authoring](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven předpověď](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Vytváření a predikce](luis-get-started-get-intent-from-rest.md)
|Node.js|[Vytváření obsahu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Předpověď](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM authoring](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Predikce npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Předpověď](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Vytváření a predikce pomocí REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Vytváření a predikce](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Vytváření obsahu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Vytváření obsahu](sdk-authoring.md?pivots=programming-language-python)<br>[Predikce pomocí REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Kontejnery

Language Understanding (LUIS) poskytuje [kontejner](luis-container-howto.md) pro poskytování místních a obsažených verzí vaší aplikace.

### <a name="export-and-import-formats"></a>Formáty exportu a importu

Language Understanding poskytuje možnost spravovat aplikaci a její modely ve `.LU` formátu JSON, ve formátu ([LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)a komprimovaný balíček pro kontejner Language Understanding.

Import a export těchto formátů je k dispozici z rozhraní API a z portálu LUIS. Portál poskytuje import a export jako součást seznamu aplikace a verze.

## <a name="other-tools-and-sdks"></a>Další nástroje a sady SDK

Rozhraní botje k dispozici jako [sada SDK](https://github.com/Microsoft/botframework) v různých jazycích a jako služba využívající [službu Azure Bot Service](https://dev.botframework.com/).

Architektura bot poskytuje [několik nástrojů, které](https://github.com/microsoft/botbuilder-tools) vám pomohou s jazykovým porozuměním, včetně:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – vytvářejte modely jazyka LUIS pomocí souborů markdown
* [SPRÁVA RYCHLÉHO NASTAVENÍ LUIS](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – vytvoření a správa LUIS.ai aplikací
* [Odeslání](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– správa nadřazených a podřízených aplikací
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Automaticky generovat záložní třídy Jazyka C#/Typescript pro vaše záměry a entity LUIS.
* [Bot Framework emulátor](https://github.com/Microsoft/BotFramework-Emulator/releases) - desktopová aplikace, která umožňuje vývojářům botů testovat a ladit roboty vytvořené pomocí bot frameworku SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - integrovaný vývojový nástroj pro vývojáře a multidisciplinární týmy pro vytváření robotů a konverzačních zkušeností s Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Nástroje podporující průběžnou integraci a nasazení pro služby NLU.

## <a name="next-steps"></a>Další kroky

* Informace o běžných [kódech chyb protokolu HTTP](luis-reference-response-codes.md)
* [Referenční dokumentace](https://docs.microsoft.com/azure/index) pro všechna api a sady SDK
* [Architektura botů](https://github.com/Microsoft/botbuilder-dotnet) a [služba Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitivní kontejnery](../cognitive-services-container-support.md)
