---
title: Vytvoření koncového bodu RESTful
description: V tomto kurzu se dozvíte, jak vytvořit RESTful koncový bod pro vlastní poskytovatele. Podrobnosti o tom, jak zpracovávat požadavky a odpovědi pro podporované metody HTTP RESTful.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200205"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Vytvořit RESTful koncový bod pro vlastní zprostředkovatele

Vlastní poskytovatel je kontrakt mezi Azure a koncovým bodem. S vlastními poskytovateli můžete přizpůsobit pracovní postupy v Azure. V tomto kurzu se dozvíte, jak vytvořit vlastního RESTful koncového bodu poskytovatele. Pokud nejste obeznámeni se službou Azure Custom Providers, přečtěte si [Přehled vlastních poskytovatelů prostředků](overview.md).

> [!NOTE]
> Tento kurz sestaví v kurzu [nastavení Azure Functions pro vlastní poskytovatele Azure](./tutorial-custom-providers-function-setup.md). Některé kroky v tomto kurzu fungují jenom v případě, že aplikace Azure Functions je nastavená tak, aby fungovala s vlastními poskytovateli.

## <a name="work-with-custom-actions-and-custom-resources"></a>Práce s vlastními akcemi a vlastními prostředky

V tomto kurzu aktualizujete aplikaci Function App tak, aby fungovala jako RESTful koncový bod pro vlastního poskytovatele. Prostředky a akce v Azure jsou modelovány po následující základní specifikaci RESTful:

- **Put**: vytvoření nového prostředku
- **Get (instance)**: Načte existující prostředek.
- **Odstranit**: odebrat existující prostředek
- **Post**: Aktivace akce
- **Get (Collection)**: vypsat všechny existující prostředky

 V tomto kurzu použijete službu Azure Table Storage. Ale všechny databáze nebo služby úložiště můžou fungovat.

## <a name="partition-custom-resources-in-storage"></a>Rozdělení vlastních prostředků v úložišti

Vzhledem k tomu, že vytváříte službu RESTful, je potřeba uložit vytvořené prostředky. Pro službu Azure Table Storage potřebujete pro svá data vygenerovat klíče oddílů a řádků. U vlastních zprostředkovatelů by měla být data rozdělená na vlastního zprostředkovatele. Když se příchozí požadavek pošle vlastnímu poskytovateli, vlastní zprostředkovatel přidá `x-ms-customproviders-requestpath` hlavičku pro odchozí požadavky na koncový bod.

Následující příklad ukazuje `x-ms-customproviders-requestpath` hlavičku vlastního prostředku:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

V závislosti na záhlaví tohoto příkladu `x-ms-customproviders-requestpath` můžete pro své úložiště vytvořit parametry *PartitionKey* a *rowKey* , jak je znázorněno v následující tabulce:

Parametr | Template (Šablona) | Description
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametr *partitionKey* určuje, jak jsou data rozdělená na oddíly. Data jsou obvykle rozdělená instancemi vlastního zprostředkovatele.
*rowKey* | `{myResourceType}:{myResourceName}` | Parametr *rowKey* určuje jednotlivý identifikátor pro data. Identifikátor je obvykle název prostředku.

Je také potřeba vytvořit novou třídu k modelování vlastního prostředku. V tomto kurzu přidáte do aplikace Function App tuto třídu **CustomResource** :

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** je jednoduchá obecná třída, která přijímá vstupní data. Vychází z **TableEntity**, který se používá k ukládání dat. Třída **CustomResource** dědí dvě vlastnosti z **TableEntity**: **partitionKey** a **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Podpora RESTful metod vlastního zprostředkovatele

> [!NOTE]
> Pokud nekopírujete kód přímo z tohoto kurzu, je nutné, aby obsah odpovědi byl platný ve formátu JSON, který nastaví `Content-Type` hlavičku na `application/json` .

Teď, když jste nastavili dělení dat, vytvořte základní metody CRUD a triggeru pro vlastní prostředky a vlastní akce. Vzhledem k tomu, že vlastní zprostředkovatelé fungují jako proxy, koncový bod RESTful musí model a zpracovat požadavek a odpověď. Následující fragmenty kódu ukazují, jak zpracovávat základní operace RESTful.

### <a name="trigger-a-custom-action"></a>Aktivace vlastní akce

Pro vlastní poskytovatele se spustí vlastní akce prostřednictvím požadavků POST. Vlastní akce může volitelně přijmout tělo žádosti, které obsahuje sadu vstupních parametrů. Akce pak vrátí odpověď, která signalizuje výsledek akce a zda byla úspěšná nebo neúspěšná.

Do aplikace Function App přidejte následující metodu **TriggerCustomAction** :

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
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

Metoda **TriggerCustomAction** přijímá příchozí požadavek a jednoduše vrací zpět odpověď se stavovým kódem.

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Pro vlastní poskytovatele se vlastní prostředek vytvoří prostřednictvím požadavků PUT. Vlastní zprostředkovatel přijme text požadavku JSON, který obsahuje sadu vlastností pro vlastní prostředek. Prostředky v Azure se řídí modelem RESTful. Stejný požadavek URL můžete použít k vytvoření, načtení nebo odstranění prostředku.

Přidejte následující metodu **CreateCustomResource** k vytvoření nových prostředků:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
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

Metoda **CreateCustomResource** aktualizuje příchozí požadavek tak, aby zahrnoval pole **ID**, **název** a **typ** specifická pro Azure. Tato pole jsou vlastnosti nejvyšší úrovně používané službami v Azure. Umožňují vlastnímu zprostředkovateli spolupracovat s dalšími službami, jako jsou Azure Policy, Azure Resource Manager šablony a protokol aktivit Azure.

Vlastnost | Příklad | Description
---|---|---
**Jméno** | {myCustomResourceName} | Název vlastního prostředku
**textový** | Microsoft. CustomProviders/resourceProviders/{ResourceType} | Obor názvů typu prostředku
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>Zprostředkovatelé/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{ResourceType}/{myCustomResourceName} | ID prostředku

Kromě přidávání vlastností jste také uložili dokument JSON do úložiště tabulek Azure.

### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

U vlastních zprostředkovatelů se vlastní prostředek načte prostřednictvím požadavků GET. Vlastní *zprostředkovatel* nepřijímá text požadavku JSON. V případě požadavků GET koncový bod používá `x-ms-customproviders-requestpath` hlavičku k vrácení již vytvořeného prostředku.

Přidejte následující metodu **RetrieveCustomResource** k načtení existujících prostředků:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
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

V Azure se prostředky řídí modelem RESTful. Adresa URL požadavku, která vytvoří prostředek, vrátí prostředek, pokud je proveden požadavek GET.

### <a name="remove-a-custom-resource"></a>Odebrání vlastního prostředku

U vlastních zprostředkovatelů se vlastní prostředek odebere přes požadavky na odstranění. Vlastní *zprostředkovatel* nepřijímá text požadavku JSON. Pro žádost o odstranění použije koncový bod `x-ms-customproviders-requestpath` hlavičku k odstranění již vytvořeného prostředku.

Chcete-li odebrat existující prostředky, přidejte následující metodu **RemoveCustomResource** :

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
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

V Azure se prostředky řídí modelem RESTful. Adresa URL požadavku, která vytvoří prostředek, odstraní prostředek i v případě, že je proveden požadavek na odstranění.

### <a name="list-all-custom-resources"></a>Zobrazit seznam všech vlastních prostředků

Pro vlastní poskytovatele můžete vytvořit výčet seznamu existujících vlastních prostředků pomocí požadavků GET Collection. Vlastní *zprostředkovatel* nepřijímá text požadavku JSON. Pro kolekci požadavků GET používá koncový bod `x-ms-customproviders-requestpath` hlavičku k zobrazení výčtu již vytvořených prostředků.

Přidejte následující metodu **EnumerateAllCustomResources** k vytvoření výčtu existujících prostředků:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
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
> RowKey QueryComparisons. GreaterThan a QueryComparisons. LessThan je syntaxe služby Azure Table Storage k provedení dotazu "StartsWith" pro řetězce.

Pokud chcete zobrazit seznam všech existujících prostředků, vygenerujte dotaz na úložiště tabulek Azure, který zajišťuje, aby prostředky existovaly ve vlastním oddílu poskytovatele. Dotaz pak zkontroluje, zda klíč řádku začíná stejnou `{myResourceType}` hodnotou.

## <a name="integrate-restful-operations"></a>Integrace operací RESTful

Po přidání všech metod RESTful do aplikace Function App aktualizujte hlavní metodu **Run** , která volá funkce pro zpracování různých požadavků REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
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
        // Action request for a custom action.
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

Aktualizovaná metoda **Run** teď zahrnuje vstupní vazbu *tableStorage* , kterou jste přidali pro Azure Table Storage. První část metody načte `x-ms-customproviders-requestpath` hlavičku a použije `Microsoft.Azure.Management.ResourceManager.Fluent` knihovnu k analýze hodnoty jako ID prostředku. `x-ms-customproviders-requestpath`Hlavička je odeslána vlastním poskytovatelem a určuje cestu příchozího požadavku.

Pomocí analyzovaného ID prostředku můžete vygenerovat hodnoty **partitionKey** a **rowKey** pro data, která chcete vyhledat, nebo ukládat vlastní prostředky.

Po přidání metod a tříd je potřeba aktualizovat metody **using** aplikace Function App. Do horní části souboru jazyka C# přidejte následující kód:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

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

Pokud se v jakémkoli bodě tohoto kurzu ztratíte, najdete kompletní ukázku kódu v [referenčních informacích ke koncovému bodu C# RESTful ve vlastním zprostředkovateli](./reference-custom-providers-csharp-endpoint.md). Po dokončení aplikace Function App uložte adresu URL aplikace Function App. Dá se použít k aktivaci aplikace Function App v pozdějších kurzech.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili koncový bod RESTful pro práci s koncovým bodem vlastního poskytovatele Azure. Pokud se chcete dozvědět, jak vytvořit vlastního poskytovatele, přečtěte si článek [kurz: Vytvoření vlastního zprostředkovatele](./tutorial-custom-providers-create.md).
