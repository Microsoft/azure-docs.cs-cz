---
title: Streamování lokátory ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou lokátory streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: cb34855fc9451679c885eebb0ef5a2fab0be8c57
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086841"
---
# <a name="streaming-locators"></a>Lokátory streamování

Vašim klientům poskytnout adresu URL, kterou můžete použít k přehrání kódované video nebo zvukové soubory, je potřeba vytvořit [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a vytvoření adresy URL pro streamování. Další informace najdete v tématu [Stream souboru](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definice StreamingLocator

Následující tabulka uvádí vlastnosti StreamingLocator a umožňuje jejich definice.

|Název|Popis|
|---|---|
|id |Plně kvalifikované ID prostředku pro prostředek.|
|jméno   |Název prostředku.|
|properties.alternativeMediaId|ID alternativních médií tento Lokátor streamování.|
|properties.assetName   |Název assetu|
|properties.contentKeys |Klíčů ContentKeys, používá tento Lokátor streamování.|
|Properties.Created |Čas vytvoření Lokátor streamování.|
|properties.defaultContentKeyPolicyName|Název výchozí ContentKeyPolicy používá tento Lokátor streamování.|
|properties.endTime |Čas ukončení Lokátor streamování.|
|properties.startTime|Čas zahájení Lokátor streamování.|
|properties.streamingLocatorId|StreamingLocatorId Lokátor streamování.|
|properties.streamingPolicyName |Název zásady streamování používá tento Lokátor streamování. Zadejte název streamování zásady, které jste vytvořili nebo použijte jednu z předdefinovaných zásad streamování. Jsou předdefinované datové proudy zásady k dispozici: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_ MultiDrmStreaming.|
|type|Typ prostředku.|

Kompletní definici, naleznete v tématu [lokátory streamování](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Služba Media Services podporuje následující možnosti dotazu OData pro lokátory streamování: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Popis operátoru:

* EQ = rovno
* Ne = není rovno
* Ge = větší než nebo rovno
* Le = menší než nebo rovno
* Gt = je větší než
* Lt = menší než

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti může použít u vlastnosti StreamingLocator: 

|Název|Filtr|Objednání|
|---|---|---|
|id |||
|jméno|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.Created |Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Stránkování

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 10.

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. 

Pokud StreamingLocators jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla.) 

Následující příklad jazyka C# ukazuje, jak zobrazit výčet prostřednictvím všech StreamingLocators v účtu.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

ZBÝVAJÍCÍ příklady naleznete v tématu [lokátory streamování – seznam](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
