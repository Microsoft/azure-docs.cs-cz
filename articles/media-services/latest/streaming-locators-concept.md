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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 658843fd5acbe0d4e29947e99c00edf4909fe9f4
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742741"
---
# <a name="streaming-locators"></a>Lokátory streamování

Je potřeba zadat kódovaný klienty pomocí adresy URL, můžete použít k přehrávání video nebo zvukové soubory, je potřeba vytvořit [Lokátor streamování](https://docs.microsoft.com/rest/api/media/streaminglocators) a vytvoření adresy URL pro streamování. Další informace najdete v tématu [Stream souboru](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>Definice StreamingLocator

Následující tabulka uvádí vlastnosti StreamingLocator a umožňuje jejich definice.

|Název|Popis|
|---|---|
|id |Plně kvalifikované ID prostředku pro prostředek.|
|jméno|Název prostředku.|
|properties.alternativeMediaId|ID alternativních médií tento Lokátor streamování.|
|properties.assetName|Název assetu|
|properties.contentKeys|Klíčů ContentKeys, používá tento Lokátor streamování.|
|Properties.Created|Čas vytvoření Lokátor streamování.|
|properties.defaultContentKeyPolicyName|Název výchozí ContentKeyPolicy používá tento Lokátor streamování.|
|properties.endTime|Čas ukončení Lokátor streamování.|
|properties.startTime|Čas zahájení Lokátor streamování.|
|properties.streamingLocatorId|StreamingLocatorId Lokátor streamování.|
|properties.streamingPolicyName |Název zásady streamování používá tento Lokátor streamování. Zadejte název streamování zásady, které jste vytvořili nebo použijte jednu z předdefinovaných zásad streamování. Předdefinované datové proudy zásady k dispozici jsou: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming.|
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
