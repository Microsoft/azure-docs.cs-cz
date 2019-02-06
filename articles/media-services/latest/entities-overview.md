---
title: Filtrování, řazení, stránkování entit Azure Media Services – Azure | Dokumentace Microsoftu
description: Tento článek popisuje filtrování, řazení, stránkování entit Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4c6e3281bd2b37b60c8d165c6c3152e970a5ce32
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745092"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování entit Media Services

## <a name="overview"></a>Přehled

Služba Media Services podporuje následující možnosti dotazu OData pro službu Media Services v3 entity: 

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

Vlastnosti entity, které jsou typu datum a čas jsou vždy ve formátu UTC.

## <a name="page-results"></a>Výsledky stránky

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. Velikost stránky se liší podle typu entity, přečtěte si prosím následující jednotlivé části Podrobnosti.

Pokud entity jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla). 

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

## <a name="assets"></a>Prostředky

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak filtrování a řazení možnosti může použít u [Asset](https://docs.microsoft.com/rest/api/media/assets) vlastnosti: 

|Název|Filtr|Objednání|
|---|---|---|
|id|||
|jméno|eq, gt, lt| Vzestupným a sestupným|
|properties.alternateId |EQ||
|properties.assetId |EQ||
|Properties.Container |||
|Properties.Created| eq, gt, lt| Vzestupným a sestupným|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

Následující příklad jazyka C# filtry na datum vytvoření:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Stránkování 

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 1000.

#### <a name="c-example"></a>Příklad jazyka C#

Následující příklad jazyka C# ukazuje, jak zobrazit výčet prostřednictvím všechny prostředky v účtu.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Příklad REST

Podívejte se na následující příklad použití $skiptoken. Ujistěte se, že nahradíte *amstestaccount* se název účtu a sadou *verze api-version* hodnotu na nejnovější verzi.

Pokud jste požádali o seznam prostředků tímto způsobem:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

By získáte zpět odpověď podobná následujícímu:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Na další stránku byste požádat o pak zasláním požadavek get:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Další příklady REST, najdete v článku [prostředky – seznam](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="content-key-policies"></a>Zásady symetrických klíčů

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti mohou být použity u [obsahu zásady klíčů](https://docs.microsoft.com/rest/api/media/contentkeypolicies) vlastnosti: 

|Název|Filtr|Objednání|
|---|---|---|
|id|||
|jméno|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Created |Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Description |Eq, ne, ge, le, gt, lt||
|properties.lastModified|Eq, ne, ge, le, gt, lt|Vzestupným a sestupným|
|Properties.Options |||
|properties.policyId|Eq, ne||
|type|||

### <a name="pagination"></a>Stránkování

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 10.

Následující C# příklad ukazuje, jak vytvořit výčet všech **obsahu zásady klíčů** v účtu.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

ZBÝVAJÍCÍ příklady naleznete v tématu [obsahu klíč zásady – seznam](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Úlohy

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti mohou být použity u [úlohy](https://docs.microsoft.com/rest/api/media/jobs) vlastnosti: 

| Název    | Filtr                        | Objednání |
|---------|-------------------------------|-------|
| jméno                    | EQ            | Vzestupným a sestupným|
| properties.state        | Eq, ne        |                         |
| Properties.Created      | gt, ge, lt, le| Vzestupným a sestupným|
| properties.lastModified | gt, ge, lt, le | Vzestupným a sestupným| 


### <a name="pagination"></a>Stránkování

Úlohy stránkování je podporována v Media Services v3.

Následující C# příklad ukazuje, jak zobrazit výčet prostřednictvím úlohy v rámci účtu.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

ZBÝVAJÍCÍ příklady naleznete v tématu [úloh – seznam](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Lokátory streamování

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

## <a name="streaming-policies"></a>Zásady streamování

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


## <a name="transform"></a>Transformace

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti mohou být použity u [transformuje](https://docs.microsoft.com/rest/api/media/transforms) vlastnosti: 

| Název    | Filtr                        | Objednání |
|---------|-------------------------------|-------|
| jméno                    | EQ            | Vzestupným a sestupným|
| Properties.Created      | gt, ge, lt, le| Vzestupným a sestupným|
| properties.lastModified | gt, ge, lt, le | Vzestupným a sestupným|

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
