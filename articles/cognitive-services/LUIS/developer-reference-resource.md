---
title: Prostředky pro vývojáře – Language Understanding
description: Sady SDK, rozhraní REST API a CLI vám pomůžou vyvíjet aplikace Language Understanding (LUIS) ve vašem programovacím jazyce. Spravujte své prostředky Azure a LUIS předpovědi.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946177"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Materiály pro vývojáře sady SDK, REST a CLI pro Language Understanding (LUIS)

Sady SDK, rozhraní REST API a CLI vám pomůžou vyvíjet aplikace Language Understanding (LUIS) ve vašem programovacím jazyce. Spravujte své prostředky Azure a LUIS předpovědi.

## <a name="azure-resource-management"></a>Správa prostředků Azure

Pomocí vrstvy správy Azure Cognitive Services můžete vytvořit, upravit, vypsat a odstranit prostředek Language Understanding nebo služby pro rozpoznávání.

Vyhledejte referenční dokumentaci na základě nástroje:

* [Azure CLI](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


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
|Předpověď|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[Technologie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Koncové body REST

LUIS má aktuálně 2 typy koncových bodů:

* **vytváření obsahu** pro školicí koncový bod
* **předpověď** dotazů na koncový bod modulu runtime.

|Účel|URL|
|--|--|
|Vytváření kódu v2 na školicím bodu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Vytváření obsahu V3 na školicím bodu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Předpověď v2 – všechny předpovědi v koncovém bodu modulu runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Předpověď předpovědi verze V3 na koncový bod za běhu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Předpověď předpovědi V3 – předpověď slotu na koncový bod za běhu|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Následující tabulka popisuje parametry, které jsou označeny složenými závorkami `{}` v předchozí tabulce.

|Parametr|Účel|
|--|--|
|`your-resource-name`|Název prostředku Azure|
|`q` nebo `query`|utterance text odeslaný z klientské aplikace, jako je třeba robota chatu|
|`version`|10 název verze znaku|
|`slot`| `production` nebo `staging`|

### <a name="rest-query-string-parameters"></a>Parametry řetězce dotazu REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Aplikační schéma

[Schéma aplikace](app-schema-definition.md) je importováno a exportováno ve `.json` `.lu` formátu nebo.

### <a name="language-based-sdks"></a>Sady SDK založené na jazyce

|Jazyk |Referenční dokumentace|Balíček|Rychlé starty|
|--|--|--|--|
|C#|[Vytváření obsahu](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Předpovědi](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[Vytváření balíčků NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Předpověď NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Vytváření obsahu](./client-libraries-rest-api.md?pivots=rest-api)<br>[Prediktivní dotazování](./client-libraries-rest-api.md?pivots=rest-api)|
|Přejít|[Vytváření a předpověď](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SADĚ](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Vytváření a předpověď](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Vytváření Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Předpověď Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Vytváření obsahu](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Předpovědi](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[Vytváření NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Předpověď NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Vytváření obsahu](./client-libraries-rest-api.md?pivots=rest-api)<br>[Předpovědi](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Vytváření a předpověď](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Vytváření obsahu](./client-libraries-rest-api.md?pivots=rest-api)<br>[Předpovědi](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Kontejnery

Language Understanding (LUIS) poskytuje [kontejner](luis-container-howto.md) pro poskytování místních a obsažených verzí vaší aplikace.

### <a name="export-and-import-formats"></a>Formáty exportu a importu

Language Understanding poskytuje možnost spravovat svou aplikaci a její modely ve formátu JSON, ve `.LU` formátu[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)(formát) a komprimovaném balíčku pro kontejner Language Understanding.

Import a export těchto formátů je dostupný z rozhraní API a z portálu LUIS. Portál poskytuje import a export jako součást seznamu a verzí seznamu aplikací.

## <a name="workshops"></a>Semináře

* GitHub: (workshop) [konverzace – AI: NLU pomocí Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Nástroje pro kontinuální integraci

* GitHub: (Preview) [vývoj aplikace Luis pomocí postupů DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) – nástroje podporující průběžnou integraci a nasazování pro služby NLU.

## <a name="bot-framework-tools"></a>Nástroje pro robot Framework

Rozhraní bot Framework je dostupné jako [sada SDK](https://github.com/Microsoft/botframework) v různých jazycích a jako služba pomocí [Azure bot Service](https://dev.botframework.com/).

Robot Framework nabízí [několik nástrojů](https://github.com/microsoft/botbuilder-tools) , které vám pomůžou s Language Understanding, včetně:
* [Emulátor rozhraní robot](https://github.com/Microsoft/BotFramework-Emulator/releases) – desktopová aplikace umožňující vývojářům robotům testovat a ladit roboty sestavené pomocí sady robot Framework SDK
* [Robot Framework robot](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – integrovaný vývojový nástroj pro vývojáře a týmy pro více týmů k sestavování roboty a konverzací v prostředí Microsoft bot Framework
* [Ukázky rozhraní bot](https://github.com/microsoft/botbuilder-samples) – v #C, JavaScriptu, TypeScriptu a Pythonu

## <a name="next-steps"></a>Další kroky

* Další informace o běžných [kódech chyb HTTP](luis-reference-response-codes.md)
* [Referenční dokumentace](../../index.yml) pro všechna rozhraní API a sady SDK
* [Rozhraní bot Framework](https://github.com/Microsoft/botbuilder-dotnet) a [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Rozpoznávání kontejnerů](../cognitive-services-container-support.md)
