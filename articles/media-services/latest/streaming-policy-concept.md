---
title: Streamování zásady ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou zásady streamování a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/22/2018
ms.author: juliako
ms.openlocfilehash: d74ce913a2189dd1062b30f9def919cbbabe7b64
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742520"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services v3 streamování zásady umožňují definovat protokolů streamování a možnosti šifrování pro vaše StreamingLocators. Můžete zadat název streamování zásady, které jste vytvořili, nebo použijte jednu z předdefinovaných zásad streamování. Předdefinované datové proudy zásady, které se aktuálně k dispozici jsou: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Při použití vlastního [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), by měly omezenou sadu zásad návrhu pro svůj účet Media Service a znovu je použít pro vaše lokátory streamování pokaždé, když jsou potřeba stejné možnosti šifrování a protokoly. Kvóta pro počet položek streamování zásady, které má váš účet Media Service. By neměl být vytváření nových zásad streamování pro každý Lokátor streamování.

## <a name="streamingpolicy-definition"></a>Definice StreamingPolicy

Následující tabulka uvádí vlastnosti StreamingPolicy a umožňuje jejich definice.

|Název|Popis|
|---|---|
|id|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|Název prostředku.|
|properties.commonEncryptionCbcs|Konfigurace CommonEncryptionCbcs|
|properties.commonEncryptionCenc|Konfigurace CommonEncryptionCenc|
|Properties.Created |Čas vytvoření datových proudů zásad|
|properties.defaultContentKeyPolicyName |Výchozí ContentKey používá aktuální zásady streamování|
|properties.envelopeEncryption  |Konfigurace EnvelopeEncryption|
|properties.noEncryption|Konfigurace NoEncryption|
|type|Typ prostředku.|

Kompletní definici, naleznete v tématu [streamování zásady](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Služba Media Services podporuje následující možnosti dotazu OData pro streamování zásady: 

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

Následující tabulka ukazuje, jak tyto možnosti může použít u vlastnosti StreamingPolicy: 

|Název|Filtr|Objednání|
|---|---|---|
|id|||
|jméno|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.Created |Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Stránkování

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 10.

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. 

Pokud StreamingPolicy jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla.) 

Následující příklad jazyka C# ukazuje, jak zobrazit výčet prostřednictvím všech StreamingPolicies v účtu.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

ZBÝVAJÍCÍ příklady naleznete v tématu [streamování zásady – seznam](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
