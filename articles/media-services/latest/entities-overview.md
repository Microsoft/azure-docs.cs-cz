---
title: Filtrování, řazení a stránkování entit Mediálních služeb
titleSuffix: Azure Media Services
description: Další informace o filtrování, řazení a stránkování entit Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584537"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrování, řazení a stránkování entit Mediálních služeb

Toto téma popisuje možnosti dotazu OData a podporu stránkování, které jsou k dispozici, když uvádíte entity Azure Media Services v3.

## <a name="considerations"></a>Požadavky

* Vlastnosti entit, které `Datetime` jsou typu jsou vždy ve formátu UTC.
* Prázdné místo v řetězci dotazu by mělo být před odesláním požadavku zakódováno adresou URL.

## <a name="comparison-operators"></a>Operátory porovnání

Následující operátory můžete použít k porovnání pole s konstantní hodnotou:

Operátory rovnosti:

- `eq`: Otestujte, zda se pole *rovná* konstantní hodnotě.
- `ne`: Otestujte, zda se pole *nerovná* konstantní hodnotě.

Operátory rozsahu:

- `gt`: Otestujte, zda je pole *větší než* konstantní hodnota.
- `lt`: Otestujte, zda je pole *menší než* konstantní hodnota.
- `ge`: Otestujte, zda je pole *větší nebo rovno* konstantní hodnotě.
- `le`: Otestujte, zda je pole *menší nebo rovno* konstantní hodnotě.

## <a name="filter"></a>Filtr

Slouží `$filter` k zadání parametru filtru OData k vyhledání pouze objektů, které vás zajímají.

Následující příklad REST filtruje hodnotu `alternateId` majetku:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Následující příklad jazyka C# filtruje datum vytvoření datového zdroje:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Pořadí podle

Slouží `$orderby` k seřazení vrácených objektů podle zadaného parametru. Například:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Chcete-li výsledky seřadit ve vzestupném `asc` nebo `desc` sestupném pořadí, připojte buď název pole, oddělený mezerou. Například: `$orderby properties/created desc`.

## <a name="skip-token"></a>Přeskočit token

Pokud odpověď na dotaz obsahuje mnoho `$skiptoken` položek, vrátí služba hodnotu (`@odata.nextLink`), kterou použijete k získání další stránky výsledků. Použijte ji k procházení celé sady výsledků.

Ve službě Media Services v3 nelze nakonfigurovat velikost stránky. Velikost stránky se liší podle typu entity. Podrobnosti načtete v jednotlivých oddílech, které následují.

Pokud entity jsou vytvořeny nebo odstraněny při stránkování kolekce, změny se projeví v vrácené výsledky (pokud tyto změny jsou v části kolekce, která nebyla stažena).

> [!TIP]
> Vždy `nextLink` použijte k výčet kolekce a nezávisí na konkrétní velikost stránky.
>
> Hodnota `nextLink` bude k dispozici pouze v případě, že existuje více než jedna stránka entit.

Zvažte následující příklad, kde `$skiptoken` se používá. Ujistěte se, že nahradíte *amstestaccount* názvem účtu a nastavte hodnotu *verze rozhraní api* na nejnovější verzi.

Pokud požadujete seznam datových zdrojů, jako je tento:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Dostanete zpět odpověď podobnou této:

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

Poté byste požádali o další stránku odesláním žádosti o přijetí:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Následující příklad jazyka C# ukazuje, jak vytvořit výčet prostřednictvím všech lokátorů streamování v účtu.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Použití logických operátorů ke kombinování možností dotazu

Media Services v3 podporuje **operátory OR** a **A** logické. 

Následující příklad REST kontroluje stav úlohy:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Vytvořit stejný dotaz v C# takto: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtrování a řazení entit

Následující tabulka ukazuje, jak můžete použít možnosti filtrování a řazení na různé entity:

|Název entity|Název vlastnosti|Filtr|Objednání|
|---|---|---|---|
|[Prostředky](https://docs.microsoft.com/rest/api/media/assets/)|jméno|`eq`, `gt`, `lt`, `ge`, `le`|`asc` a `desc`|
||vlastnosti.alternateId |`eq`||
||vlastnosti.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` a `desc`|
|[Klíčové zásady obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|jméno|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti.popis    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||vlastnosti.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||vlastnosti.policyId|`eq`, `ne`||
|[Úlohy](https://docs.microsoft.com/rest/api/media/jobs)| jméno  | `eq`            | `asc` a `desc`|
||vlastnosti.stav        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|
||vlastnosti.lastModified | `gt`, `ge`, `lt`, `le` | `asc` a `desc`| 
|[Lokátory streamování](https://docs.microsoft.com/rest/api/media/streaminglocators)|jméno|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` a `desc`|
||vlastnosti.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Zásady streamování](https://docs.microsoft.com/rest/api/media/streamingpolicies)|jméno|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` a `desc`|
|[Transformace](https://docs.microsoft.com/rest/api/media/transforms)| jméno | `eq`            | `asc` a `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|
|| vlastnosti.lastModified | `gt`, `ge`, `lt`, `le`| `asc` a `desc`|

## <a name="next-steps"></a>Další kroky

* [Seznam datových zdrojů](https://docs.microsoft.com/rest/api/media/assets/list)
* [Zásady klíče obsahu seznamu](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Seznam úloh](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Zásady streamování seznamu](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Seznam lokátorů streamování](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Streamování souboru](stream-files-dotnet-quickstart.md)
* [Kvóty a omezení](limits-quotas-constraints.md)
