---
title: Vytváření koncový bod RESTful pro vlastní zprostředkovatelé
description: V tomto kurzu se přenášejí prostřednictvím jak si můžete vytvořit koncový bod RESTful pro vlastní zprostředkovatele. Sledování přejde do podrobností o tom, jak zpracovávat požadavky a odpovědi pro podporovaných metod rozhraní RESTful HTTP.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800033"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Vytváření koncový bod RESTful pro vlastní zprostředkovatelé

Vlastní zprostředkovatelé umožňují přizpůsobit pracovní postupy v Azure. Vlastní zprostředkovatel je kontrakt mezi Azure a `endpoint`. V tomto kurzu se projít procesem vytváření vlastního poskytovatele RESTful `endpoint`. Pokud nejste obeznámeni s vlastní poskytovatele Azure, přečtěte si téma [přehled o poskytovatelích vlastní prostředek](./custom-providers-overview.md).

V tomto kurzu je rozdělen do následujících kroků:

- Práce s vlastní akce a vlastních prostředků
- Jak dělit vlastní prostředky ve službě storage
- Podpora vlastního poskytovatele RESTful metody
- Integrace rozhraní RESTful operace

V tomto kurzu budete stavět v následujících kurzech:

- [Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> Tento kurz vytvoří vypnout předchozím kurzu. Některé kroky v tomto kurzu bude fungovat jenom v případě funkce Azure po nastavení pro práci s vlastní poskytovatele.

## <a name="working-with-custom-actions-and-custom-resources"></a>Práce s vlastní akce a vlastních prostředků

V tomto kurzu budeme aktualizovat funkci fungovat jako koncový bod RESTful pro naše vlastního zprostředkovatele. V Azure jsou vytvořeny prostředkům a akcím podle základní specifikace rozhraní RESTful: PUT – vytvoří nový prostředek, GET (instance) - načte existující prostředek, odstranění – odebere existující prostředek, POST - aktivovat akci a získání přístupu (kolekce) – obsahuje seznam všech existujících prostředků. Pro účely tohoto kurzu jsme pomocí tabulek Azure jako naše úložiště, ale můžete pracovat jakoukoli službu database nebo úložiště.

## <a name="how-to-partition-custom-resources-in-storage"></a>Jak dělit vlastní prostředky ve službě storage

Vzhledem k tomu vytváříme služba RESTful, potřebujeme k uložení vytvořené prostředky ve službě storage. Pro Azure Table storage je potřeba vygenerovat klíče oddílu a řádku pro naše data. Pro vlastní poskytovatele dat by měly být rozdělené do vlastního zprostředkovatele. Odeslání příchozí žádosti do vlastního zprostředkovatele vlastního zprostředkovatele přidá `x-ms-customproviders-requestpath` záhlaví odchozí požadavek na `endpoint`.

Ukázka `x-ms-customproviders-requestpath` záhlaví pro vlastní prostředek:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Podle předchozí ukázka `x-ms-customproviders-requestpath` záhlaví, můžeme vytvořit partitionKey a rowKey pro naše úložiště následujícím způsobem:

Parametr | Šablona | Popis
---|---
partitionKey | "{subscriptionId}: {resourceGroupName}: {název resourceProviderName}. | PartitionKey je, jak se data rozdělit na oddíly. Většině případů by měly být rozdělené data instancí vlastního zprostředkovatele.
RowKey | '{myResourceType}:{myResourceName}' | RowKey je identifikátor jednotlivé pro data. Ve většině případů toto je název prostředku.

Kromě toho také potřebujeme vytvořit novou třídu pro modelování našich vlastních prostředků. V tomto kurzu přidáme `CustomResource` třídy pro naše funkce, která je obecná třída, která přijímá libovolné detekován dat:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

Tím se vytvoří základní třídy na základě `TableEntity`, který se používá k ukládání dat. `CustomResource` Třída dědí ze dvou vlastností `TableEntity`: partitionKey a rowKey.

## <a name="support-custom-provider-restful-methods"></a>Podpora vlastního poskytovatele RESTful metody

> [!NOTE]
> Pokud nekopírujete kód přímo z tohoto kurzu, obsah odpovědi by měl být platný kód JSON a nastaví `Content-Type` záhlaví jako `application/json`.

Když teď máme instalační program pro dělení dat, můžeme generování uživatelského rozhraní si základní metody CRUD a aktivační události pro vlastní prostředky a vlastní akce. Protože vlastní zprostředkovatelé sloužil jako proxy, požadavků a odpovědí musí být modelovat a zpracovány RESTful `endpoint`. Postupujte podle následující fragmenty kódu pro zpracování základních operací RESTful:

### <a name="trigger-custom-action"></a>Vlastní akce aktivační události

Pro vlastní poskytovatele vlastní akce vyvolané prostřednictvím `POST` požadavky. Vlastní akce může volitelně přijmout tělo požadavku, který obsahuje sadu vstupní parametry. Akci byste pak vrátit zpět odpověď signally výsledek akce také, zda bylo úspěšné nebo neúspěšné. V tomto kurzu přidáme metodu `TriggerCustomAction` naše funkce:

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

`TriggerCustomAction` Metoda přijímá příchozí žádosti a jednoduše vrací zpět odpověď s stavový kód úspěchu. 

### <a name="create-custom-resource"></a>Vytvoření vlastních prostředků

Pro vlastní poskytovatele, vlastní prostředek je vytvořen pomocí `PUT` požadavky. Vlastní zprostředkovatel bude akceptovat hlavní část žádosti JSON, který obsahuje sadu vlastností pro vlastní prostředek. Prostředky v Azure, postupujte podle RESTful model. Stejná adresa URL požadavku, která byla použita k vytvoření prostředku by měl také moci načíst a odstranit prostředek. V tomto kurzu přidáme metodu `CreateCustomResource` vytvářet nové prostředky:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

`CreateCustomResource` Metoda aktualizuje příchozí požadavek pro zahrnutí Azure konkrétních polí: `id`, `name`, a `type`. Toto jsou vlastnosti nejvyšší úrovně, které jsou používané službami v Azure. Umožňují vlastního zprostředkovatele pro integraci s jinými službami, jako je Azure Policy, šablon Azure Resource Manageru a protokolů aktivit Azure.

Vlastnost | Ukázka | Popis
---|---|---
name | '{myCustomResourceName}' | Název vlastního prostředku.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Obor názvů typu prostředku.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | ID prostředku.

Kromě přidání vlastnosti, jsme také uložte dokument do Azure Table Storage. 

### <a name="retrieve-custom-resource"></a>Načíst vlastní prostředek

Pro vlastní poskytovatele, vlastní prostředek je získat pomocí `GET` požadavky. Vlastní poskytovatel *není* přijmout text požadavku JSON. V případě třídy `GET` požadavky, **koncový bod** používejte `x-ms-customproviders-requestpath` hlavička prostředek již byly vytvořeny. V tomto kurzu přidáme metodu `RetrieveCustomResource` načíst existující prostředky:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

V Azure prostředky by měly dodržovat RESTful model. Adresa URL požadavku, který byl vytvořen prostředek by mělo také vrátit prostředku, pokud `GET` vykonáním žádosti.

### <a name="remove-custom-resource"></a>Odebrat vlastní prostředek

Pro vlastní poskytovatele, vlastní prostředek se odebere prostřednictvím `DELETE` požadavky. Vlastní poskytovatel *není* přijmout text požadavku JSON. V případě třídy `DELETE` požadavky, **koncový bod** používejte `x-ms-customproviders-requestpath` záhlaví odstranit už vytvořeného prostředku. V tomto kurzu přidáme metodu `RemoveCustomResource` odebrat existující prostředky:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

V Azure prostředky by měly dodržovat RESTful model. Adresa URL požadavku, který byl vytvořen prostředek by měl také odstranit prostředek, pokud `DELETE` vykonáním žádosti.

### <a name="list-all-custom-resources"></a>Seznam všech vlastních prostředků

Pro vlastní poskytovatele, mohou být seznam existujících vlastní prostředky uvedené prostřednictvím kolekce `GET` požadavky. Vlastní poskytovatel *není* přijmout text požadavku JSON. V případě kolekce `GET` požadavky, `endpoint` používejte `x-ms-customproviders-requestpath` záhlaví výčet už vytvořené prostředky. V tomto kurzu přidáme metodu `EnumerateAllCustomResources` výčet stávající prostředky.

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> Klíč řádku více než a menší než Azure Table syntaxe provést dotaz na "startswith" pro řetězce. 

Pro výpis všech existujících prostředků, se vygeneruje Azure Table dotaz, který zajistí, že prostředky existují v našem oddílu vlastního zprostředkovatele. Dotaz a kontroly, které klíč řádku, spustí se stejným `{myResourceType}`.

## <a name="integrate-restful-operations"></a>Integrace rozhraní RESTful operace

Jakmile RESTful metody se přidají do funkce, abychom mohli aktualizovat hlavní `Run` metodu chce volat funkce pro zpracování různých ZBÝVAJÍCÍ požadavky:

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for an custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
``` 

Aktualizovaný `Run` teď bude zahrnovat metodu `tableStorage` vstupní vazby, která byla přidána pro Azure Table storage. První část metody budou nyní číst `x-ms-customproviders-requestpath` záhlaví a použití `Microsoft.Azure.Management.ResourceManager.Fluent` knihovny se analyzovat hodnotu jako ID prostředku. `x-ms-customproviders-requestpath` Hlavičku posílá vlastního zprostředkovatele a označuje umístění příchozího požadavku. Pomocí ID prostředku analyzovaný, můžeme nyní můžete generovat partitionKey a rowKey data vyhledávání identifikátoru objektu nebo ukládat vlastní prostředky.

Kromě přidání metod a tříd, musíme aktualizovat na pomocí metody pro funkci. Na začátek souboru přidejte následující:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Pokud jste se dostali ke ztrátě během libovolného bodu v tomto kurzu, ukázkový kód dokončené najdete tady [vlastního zprostředkovatele C# odkaz na koncový bod RESTful](./reference-custom-providers-csharp-endpoint.md). Po dokončení funkce uložte adresu URL funkce, které můžete použít k aktivaci funkce, jako se použije v dalších kurzech.

## <a name="next-steps"></a>Další postup

V tomto článku jsme vytvořili koncový bod RESTful pro práci s Azure vlastního zprostředkovatele `endpoint`. Přejdete k dalším článku se dozvíte, jak vytvořit vlastního zprostředkovatele.

- [Kurz: Vytvoření vlastního zprostředkovatele](./tutorial-custom-providers-create.md)
