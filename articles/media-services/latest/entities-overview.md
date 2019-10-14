---
title: Filtrování, řazení, stránkování Media Services entit – Azure | Microsoft Docs
description: Tento článek popisuje filtrování, řazení, stránkování Azure Media Services entit.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298946"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování Media Services entit

Toto téma popisuje možnosti dotazů OData a Podpora stránkování, která je k dispozici při výpisu Azure Media Servicesch entit systému V3.

## <a name="considerations"></a>Odůvodněn

* Vlastnosti entit, které jsou typu DateTime, jsou vždy ve formátu UTC.
* Před odesláním žádosti by měl být prázdný znak v řetězci dotazu zakódovaný na adrese URL.

## <a name="comparison-operators"></a>Operátory porovnání

K porovnání pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: test, zda je pole **rovno** konstantní hodnotě
- `ne`: test, zda pole není **rovno** konstantní hodnotě

Operátory rozsahu:

- `gt`: test, zda je pole **větší než** konstantní hodnota
- `lt`: test, zda je pole **menší než** hodnota konstanty
- `ge`: test, zda je pole **větší nebo rovno** konstantní hodnotě
- `le`: test, zda je pole **menší nebo rovno** konstantní hodnotě

## <a name="filter"></a>Filtrovací

**$Filter** – pomocí filtru zadejte parametr filtru OData, abyste našli jenom objekty, které vás zajímají.

Následující příklad filtruje alternateId assetu:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Následující C# příklad filtruje datum vytvoření prostředku:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Řadit podle

**$OrderBy** – slouží k řazení vrácených objektů podle zadaného parametru. Příklad:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Chcete-li výsledky seřadit ve vzestupném nebo sestupném pořadí, přidejte buď `asc`, nebo `desc` k názvu pole oddělené mezerou. Například `$orderby properties/created desc`.

## <a name="skip-token"></a>Přeskočit token

**$skiptoken** – Pokud odpověď dotazu obsahuje mnoho položek, vrátí služba hodnotu tokenu skip (`@odata.nextLink`), kterou použijete k získání další stránky výsledků. Tato možnost se dá použít k vytvoření stránky celé sady výsledků dotazu.

V Media Services V3 nemůžete konfigurovat velikost stránky. Velikost stránky se liší podle typu entity, přečtěte si prosím jednotlivé části, které následují za podrobnosti.

Pokud jsou při stránkování vytvořeny nebo smazány entity, změny se projeví v vrácených výsledcích (pokud jsou tyto změny součástí kolekce, která nebyla stažena). 

> [!TIP]
> Vždy byste měli použít `nextLink` k vytvoření výčtu kolekce a nezáleží na konkrétní velikosti stránky.
>
> @No__t-0 bude k dispozici pouze v případě, že existuje více než jedna stránka entit.

Vezměte v úvahu následující příklad, kde se používá $skiptoken. Ujistěte se, že jste nahradili *amstestaccount* názvem vašeho účtu a nastavíte hodnotu *rozhraní API-Version* na nejnovější verzi.

Pokud si vyžádáte seznam prostředků, takto:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Měla by se vám vrátit odpověď podobnou této:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Pak odešlete požadavek GET na další stránku.

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Následující C# příklad ukazuje, jak vytvořit výčet ze všech lokátorů streamování v účtu.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Kombinování možností dotazu pomocí logických operátorů

Media Services V3 podporuje logické operátory "nebo" a "a". 

Následující příklad funkce REST kontroluje stav úlohy:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Stejný dotaz C# sestavíte takto: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Možnosti filtrování a řazení entit

Následující tabulka ukazuje, jak lze použít možnosti filtrování a řazení u různých entit:

|Název entity|Název vlastnosti|Filtrovací|Za|
|---|---|---|---|
|[Hmot](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` a `desc`|
||Properties. alternateId |`eq`||
||Properties. assetId |`eq`||
||vlastnosti. vytvořeno| `eq`, `gt` `lt`| `asc` a `desc`|
|[Zásady pro klíč obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Properties. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||Properties. policyId|`eq`, `ne`||
|[Úlohy](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` a `desc`|
||vlastnosti. State        | `eq`, `ne`        |                         |
||vlastnosti. vytvořeno      | `gt`, `ge` `lt`, `le`| `asc` a `desc`|
||Properties. lastModified | `gt`, `ge` `lt`, `le` | `asc` a `desc`| 
|[Lokátory streamování](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. čas_ukončení    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Transformace](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` a `desc`|
|| vlastnosti. vytvořeno      | `gt`, `ge` `lt`, `le`| `asc` a `desc`|
|| Properties. lastModified | `gt`, `ge` `lt`, `le`| `asc` a `desc`|

## <a name="next-steps"></a>Další kroky

* [Vypsat prostředky](https://docs.microsoft.com/rest/api/media/assets/list)
* [Seznam zásad pro klíč obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Vypsat úlohy](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Vypsat zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Seznam lokátorů streamování](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Kvóty a omezení](limits-quotas-constraints.md)
