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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 4d02ddf50660cd700b2f1c5999ceadfb472b6906
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380956"
---
# <a name="streaming-policies"></a>Zásady streamování

V Azure Media Services v3 streamování zásady umožňují definovat protokolů streamování a možnosti šifrování pro vaše StreamingLocators. Můžete zadat název streamování zásady, které jste vytvořili, nebo použijte jednu z předdefinovaných zásad streamování. Jsou předdefinované datové proudy zásady, které se aktuálně k dispozici: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" a "Predefined_ MultiDrmStreaming ".

> [!IMPORTANT]
> Pokud chcete definovat vlastní [zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies), doporučujeme navrhnout pro účet služby Media Service omezený počet takovýchto zásad a používat je opakovaně pro streamovací lokátory, kdykoli potřebujete stejné protokoly a možnosti šifrování. Počet záznamů StreamingPolicy je pro účty služby Media Service omezený kvótou. Neměli byste vytvářet samostatnou zásadu streamování pro každý streamovací lokátor.

## <a name="streamingpolicy-definition"></a>Definice StreamingPolicy

Následující tabulka uvádí vlastnosti StreamingPolicy a umožňuje jejich definice.

|Název|Typ|Popis|
|---|---|---|
|id|řetězec|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|řetězec|Název prostředku.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs|Konfigurace CommonEncryptionCbcs|
|properties.commonEncryptionCenc|CommonEncryptionCenc|Konfigurace CommonEncryptionCenc|
|Properties.Created |řetězec|Čas vytvoření datových proudů zásad|
|properties.defaultContentKeyPolicyName |řetězec|Výchozí ContentKey používá aktuální zásady streamování|
|properties.envelopeEncryption  |EnvelopeEncryption|Konfigurace EnvelopeEncryption|
|properties.noEncryption|NoEncryption|Konfigurace NoEncryption|
|type|řetězec|Typ prostředku.|

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

ZBÝVAJÍCÍ příklady naleznete v tématu [streamování zásady – seznam](https://docs.microsoft.com/rest/api/media/streamingpolicies/streamingpolicies_list)

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
