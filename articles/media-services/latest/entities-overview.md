---
title: Filtrování, řazení a stránkování Media Services entit
titleSuffix: Azure Media Services
description: Přečtěte si o filtrování, řazení a stránkování entit Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9a8cff3685cdaad011332adf58dc76f74976cd44
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89300183"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrování, řazení a stránkování Media Services entit

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Toto téma popisuje možnosti dotazů OData a Podpora stránkování, která je dostupná při výpisu Azure Media Servicesch entit v3.

## <a name="considerations"></a>Požadavky

* Vlastnosti entit, které jsou typu, `Datetime` jsou vždy ve formátu UTC.
* Prázdný znak v řetězci dotazu by měl být zakódovaný pomocí adresy URL před odesláním žádosti.

## <a name="comparison-operators"></a>Operátory porovnání

K porovnání pole s konstantní hodnotou můžete použít následující operátory:

Operátory rovnosti:

- `eq`: Otestujte, zda je pole *rovno* konstantní hodnotě.
- `ne`: Otestujte, zda se pole *nerovná* konstantní hodnotě.

Operátory rozsahu:

- `gt`: Otestujte, zda je pole *větší než* konstantní hodnota.
- `lt`: Otestujte, zda je pole *menší než* hodnota konstanty.
- `ge`: Otestujte, zda je pole *větší než nebo rovno* konstantní hodnotě.
- `le`: Otestujte, zda je pole *menší nebo rovno* konstantní hodnotě.

## <a name="filter"></a>Filtr

Použijte `$filter` k poskytnutí parametru filtru OData, abyste našli jenom objekty, které vás zajímají.

Následující příklad filtru REST filtruje `alternateId` hodnotu prostředku:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Následující příklad v jazyce C# filtruje datum vytvoření prostředku:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Řadit podle

Použijte `$orderby` k řazení vrácených objektů zadaným parametrem. Příklad:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

K řazení výsledků ve vzestupném nebo sestupném pořadí, přidejte buď `asc` nebo `desc` k názvu pole oddělené mezerou. Například: `$orderby properties/created desc`.

## <a name="skip-token"></a>Přeskočit token

Pokud odpověď dotazu obsahuje mnoho položek, vrátí služba `$skiptoken` `@odata.nextLink` hodnotu (), kterou použijete k získání další stránky výsledků. Použijte ji ke stránkám celé sady výsledků dotazu.

V Media Services V3 nemůžete konfigurovat velikost stránky. Velikost stránky se liší podle typu entity. Přečtěte si jednotlivé části, které následují za podrobnosti.

Pokud jsou entity vytvářeny nebo smazány při stránkování prostřednictvím kolekce, změny se projeví ve vrácených výsledcích (pokud jsou tyto změny součástí kolekce, která nebyla stažena).

> [!TIP]
> Vždy použít `nextLink` k zobrazení výčtu kolekce a nezáleží na konkrétní velikosti stránky.
>
> `nextLink`Hodnota bude přítomna pouze v případě, že existuje více než jedna stránka entit.

Vezměte v úvahu následující příklad `$skiptoken` použití. Ujistěte se, že jste nahradili *amstestaccount* názvem vašeho účtu a nastavíte hodnotu *rozhraní API-Version* na nejnovější verzi.

Pokud si vyžádáte seznam prostředků, takto:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Vrátíte odpověď podobnou této:

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

Následující příklad jazyka C# ukazuje, jak vytvořit výčet ze všech lokátorů streamování v účtu.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Kombinování možností dotazu pomocí logických operátorů

Media Services V3 podporuje logické operátory **nebo** **a a.** 

Následující příklad funkce REST kontroluje stav úlohy:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Stejný dotaz vytvoříte v jazyce C# následujícím způsobem: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Možnosti filtrování a řazení entit

Následující tabulka ukazuje, jak můžete použít možnosti filtrování a řazení u různých entit:

|Název entity|Název vlastnosti|Filtr|Objednání|
|---|---|---|---|
|[Aktiva](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` a `desc`|
||Properties. alternateId |`eq`||
||Properties. assetId |`eq`||
||vlastnosti. vytvořeno| `eq`, `gt`, `lt`| `asc` a `desc`|
|[Zásady symetrických klíčů](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Properties. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||Properties. policyId|`eq`, `ne`||
|[Úlohy](/rest/api/media/jobs)| name  | `eq`            | `asc` a `desc`|
||vlastnosti. State        | `eq`, `ne`        |                         |
||vlastnosti. vytvořeno      | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|
||Properties. lastModified | `gt`, `ge`, `lt`, `le` | `asc` a `desc`| 
|[Lokátory streamování](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` a `desc`|
||vlastnosti. čas_ukončení    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Zásady streamování](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti. vytvořeno    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Transformace](/rest/api/media/transforms)| name | `eq`            | `asc` a `desc`|
|| vlastnosti. vytvořeno      | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|
|| Properties. lastModified | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|

## <a name="next-steps"></a>Další kroky

* [Vypsat prostředky](/rest/api/media/assets/list)
* [Seznam zásad pro klíč obsahu](/rest/api/media/contentkeypolicies/list)
* [Vypsat úlohy](/rest/api/media/jobs/list)
* [Vypsat zásady streamování](/rest/api/media/streamingpolicies/list)
* [Seznam lokátorů streamování](/rest/api/media/streaminglocators/list)
* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Kvóty a omezení](limits-quotas-constraints.md)
